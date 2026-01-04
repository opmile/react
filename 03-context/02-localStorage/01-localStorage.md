# `localStorage` + React

---

## 1. O que é `localStorage` (fundamento real)

`localStorage` é uma **API de armazenamento do navegador**.

Características essenciais:

* armazenamento **chave → valor**
* valores **sempre são strings**
* persistência **entre recarregamentos**
* escopo por **domínio**
* síncrono (bloqueia a thread principal)

Ou seja:
ele existe para **guardar pequenos volumes de dados persistentes do lado do cliente**.

---

## 2. O que o `localStorage` NÃO é

Antes de usar, é crucial saber o que ele não faz:

* não é banco de dados
* não suporta consultas
* não tem schema
* não é reativo
* não é seguro (qualquer JS pode ler)

Ele é **armazenamento bruto**, nada mais.

---

## 3. Operações básicas do `localStorage`

### Salvar um valor

```js
localStorage.setItem("key", "value");
```

### Ler um valor

```js
localStorage.getItem("key"); // retorna string ou null
```

### Remover um valor

```js
localStorage.removeItem("key");
```

### Limpar tudo

```js
localStorage.clear();
```

---

## 4. O problema central: localStorage só trabalha com string

Isso é o ponto que confunde muita gente.

Se você tentar salvar um array ou objeto diretamente:

```js
localStorage.setItem("todos", []);
```

O que será salvo é:

```
"[object Object]"
```

Ou seja: lixo.

### A solução padrão

Você **sempre** usa:

* `JSON.stringify` ao salvar
* `JSON.parse` ao ler

---

## 5. Salvando dados estruturados corretamente

### Exemplo: array de tarefas

```js
const todos = [
  { id: 1, text: "Estudar React", done: false },
  { id: 2, text: "Treinar corrida", done: true },
];

localStorage.setItem("todos", JSON.stringify(todos));
```

### Lendo depois

```js
const storedTodos = localStorage.getItem("todos");

const todos = storedTodos
  ? JSON.parse(storedTodos)
  : [];
```

Essa verificação é **obrigatória**:

* `getItem` retorna `null` se não existir
* `JSON.parse(null)` quebra

---

## 6. Onde o localStorage entra no React

React tem um princípio claro:

> A UI é função do estado.

Mas o `localStorage`:

* não é reativo
* não dispara re-render

Logo:

* **ele não substitui o estado**
* ele **complementa o estado**

A regra correta é:

> O estado vive no React.
> O localStorage persiste esse estado.

Nunca o contrário.

---

## 7. Estratégia correta de uso no React

Sempre pense em duas etapas:

1. **hidratar o estado** a partir do localStorage
2. **persistir o estado** sempre que ele mudar

---

## 8. CRUD simples (Create, Read, Delete) com React + localStorage

Vamos usar o contexto de uma lista de tarefas, sem edição.

### Estrutura de dados

```js
{
  id: number,
  text: string
}
```

---

## 9. READ — carregando dados ao iniciar a aplicação

Isso acontece **uma única vez**, na montagem do componente.

```jsx
const [todos, setTodos] = useState([]);

useEffect(() => {
  const storedTodos = localStorage.getItem("todos");

  if (storedTodos) {
    setTodos(JSON.parse(storedTodos));
  }
}, []);
```

Por que funciona:

* `useEffect` com array vazio roda uma vez
* o estado inicial vem do armazenamento persistente

---

## 10. CREATE — criando e salvando uma tarefa

```jsx
function addTodo(text) {
  const newTodo = {
    id: Date.now(),
    text,
  };

  setTodos(prev => {
    const updatedTodos = [...prev, newTodo];
    localStorage.setItem("todos", JSON.stringify(updatedTodos));
    return updatedTodos;
  });
}
```

Pontos importantes:

* o estado continua sendo a fonte de verdade
* o localStorage apenas reflete a mudança
* salvar **dentro do setState** evita inconsistências

---

## 11. DELETE — removendo uma tarefa

```jsx
function deleteTodo(id) {
  setTodos(prev => {
    const updatedTodos = prev.filter(todo => todo.id !== id);
    localStorage.setItem("todos", JSON.stringify(updatedTodos));
    return updatedTodos;
  });
}
```

Aqui acontece o mesmo padrão:

* estado muda
* persistência acompanha

---

## 12. Fluxo completo do CRUD (modelo mental)

Sempre visualize assim:

1. aplicação inicia
2. React lê o localStorage
3. estado é hidratado
4. UI renderiza
5. usuário cria ou remove dados
6. estado muda
7. localStorage é atualizado
8. refresh da página mantém os dados

Esse ciclo é previsível e confiável.

---

## 13. Alternativa mais limpa: persistir via `useEffect`

Outra abordagem comum:

```jsx
useEffect(() => {
  localStorage.setItem("todos", JSON.stringify(todos));
}, [todos]);
```

Isso significa:

* toda mudança no estado dispara persistência
* separa lógica de estado e IO

Essa abordagem é **mais declarativa** e geralmente preferível.

---

## 14. Erros comuns ao usar localStorage em React

Evite sempre:

* ler localStorage em todo render
* salvar sem `JSON.stringify`
* usar localStorage como estado principal
* esquecer verificação de `null`
* misturar lógica de UI com persistência

Esses erros geram:

* bugs difíceis
* performance ruim
* código frágil

---

## 15. Quando usar localStorage em CRUDs

Use quando:

* os dados são pequenos
* não há backend
* persistência simples é suficiente
* estado é local ao usuário

Não use quando:

* há dados sensíveis
* há múltiplos usuários
* consistência forte é necessária
* dados precisam ser compartilhados

---

* `localStorage` persiste dados, não gerencia estado
* React continua sendo a fonte de verdade
* sempre use `JSON.stringify` / `JSON.parse`
* hidrate o estado na inicialização
* persista o estado quando ele mudar
* CRUD simples funciona muito bem com esse modelo
