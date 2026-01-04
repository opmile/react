# Context API

---

## 1. O problema que o Context resolve (em termos técnicos)

Antes de código, pense no Context como uma resposta a essa pergunta:

> “Como vários componentes, em níveis diferentes da árvore, podem **ler e reagir ao mesmo estado**, sem que esse estado precise ser explicitamente passado via props?”

O React, por padrão, tem um **fluxo de dados unidirecional**:

* pai → filho → neto → bisneto

O Context **não quebra esse fluxo**.
Ele cria um **canal lateral controlado**, acessível apenas para quem explicitamente se inscrever nele.

---

## 2. O que é um Context, conceitualmente

Um Context é composto por **três coisas inseparáveis**:

1. **O objeto de contexto**
2. **O Provider**
3. **Os Consumers (useContext)**

Você pode pensar assim:

* o Context define **um tipo de dado compartilhável**
* o Provider fornece **uma instância concreta desse dado**
* os consumers **leem essa instância**

---

## 3. `createContext`: criando o “contrato”

```jsx
const TodoContext = createContext(null);
```

### O que acontece aqui?

* Você está criando um **canal global lógico**
* Esse canal pode transportar **qualquer valor JavaScript**
* O valor inicial (`null`) só é usado se **não houver Provider acima**

Importante:

* `createContext` **não armazena estado**
* Ele apenas define **uma referência**

Pense nele como uma tomada na parede:

* ainda não tem energia
* só define o padrão do plug

---

## 4. O Provider: onde a “energia” entra

```jsx
<TodoContext.Provider value={{ todos, toggleTodo }}>
  {children}
</TodoContext.Provider>
```

### O papel do Provider

O Provider:

* associa um **valor concreto** ao Context
* esse valor fica disponível para **todos os descendentes**
* cria um **escopo**

Se você tiver dois Providers do mesmo Context:

```jsx
<TodoProvider>...</TodoProvider>
<TodoProvider>...</TodoProvider>
```

Eles **não compartilham estado**.
Cada Provider cria uma instância independente.

Isso é crucial para:

* isolamento
* testes
* reutilização

---

## 5. O `value`: o coração do Context

```jsx
value={{ todos, toggleTodo }}
```

Esse objeto é **a API pública do seu estado**.

Tudo que um componente consumidor pode:

* ler
* chamar
* reagir

vive aqui.

### Decisão arquitetural importante

Você **não expõe `setTodos` diretamente**.

Por quê?

Porque:

* você controla as regras de negócio
* evita mutações arbitrárias
* força um caminho único de atualização

Isso transforma o Context em algo muito próximo de um **mini store**.

---

## 6. Onde o estado realmente vive?

```jsx
const [todos, setTodos] = useState([]);
```

O estado:

* vive **dentro do Provider**
* não vive no Context
* não vive nos consumers

O Context **transporta referências**, não dados por si só.

Quando `setTodos` é chamado:

1. o Provider re-renderiza
2. o `value` muda
3. todos os consumers que usam esse Context são notificados

---

## 7. `useContext`: como os componentes “escutam”

```jsx
const { todos } = useTodos();
```

Por baixo dos panos:

* o React sobe a árvore de componentes
* encontra o Provider mais próximo daquele Context
* retorna o `value` associado

Se o Provider não existir:

* retorna o valor default (`null`)
* por isso o guard clause é importante

```jsx
if (!context) {
  throw new Error("useTodos deve ser usado dentro de TodoProvider");
}
```

Isso:

* falha rápido
* evita bugs silenciosos
* documenta o contrato de uso

---

## 8. O Custom Hook (`useTodos`) não é só açúcar

```jsx
export function useTodos() {
  const context = useContext(TodoContext);
  if (!context) {
    throw new Error("...");
  }
  return context;
}
```

Esse hook:

* encapsula o acesso ao Context
* centraliza validações
* desacopla componentes do Context em si

Se amanhã você trocar:

* Context API → Zustand
* ou dividir o contexto em vários

Os componentes **não mudam**.

Isso é design profissional.

---

## 9. Fluxo completo de dados (passo a passo)

Vamos seguir um clique num item da lista:

1. Usuário clica no `<TodoItem>`
2. `toggleTodo(id)` é chamado
3. Essa função vive no Provider
4. `setTodos` atualiza o estado
5. O Provider re-renderiza
6. O `value` muda
7. React notifica todos os consumers
8. Apenas os componentes que **usam esse Context** re-renderizam
9. A UI reflete o novo estado

Nada é passado por props.
Nada é duplicado.
Nada é sincronizado manualmente.

---

## 10. Re-renderização: mito vs realidade

Um mito comum:

> “Context faz tudo re-renderizar”

Correção técnica:

* **só re-renderiza quem consome o Context**
* e apenas quando o `value` muda por referência

Por isso, boas práticas:

* evitar recriar objetos desnecessariamente
* separar contexts quando necessário
* não colocar tudo em um único Provider gigante

---

## 11. Context como fronteira de domínio

Um Context bem feito representa:

* um domínio da aplicação
* com estado
* regras
* ações

Exemplos:

* `AuthContext`
* `CartContext`
* `ThemeContext`
* `TodoContext`

Cada um:

* tem sua fonte única de verdade
* define como o estado pode mudar
* expõe apenas o necessário

---

## 12. O que o Context **não** é

Muito importante:

* Context **não é** state manager genérico
* Context **não é** substituto automático de Redux
* Context **não é** para estado efêmero

Ele é:

> um mecanismo de **injeção de dependência reativa** na árvore de componentes

Essa frase vale ouro.

---

## 13. Regra de ouro para usar Context

Use Context quando:

* vários componentes distantes precisam do mesmo estado
* esse estado representa um conceito global/local de domínio
* você quer uma API clara de leitura e escrita

Não use quando:

* o estado é local
* só um componente precisa
* não existe compartilhamento real

---