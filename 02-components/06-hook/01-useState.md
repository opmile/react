# React Hook: `useState`

Estado em React é um objeto de dados dentro de um componente que, ao ser alterado, faz com que o React renderize (atualize) o componente na tela, refletindo as mudanças na interface do usuário, como um contador que aumenta ou um interruptor que liga/desliga. Para gerenciar isso, usa-se o hook useState, que retorna o valor do estado e uma função para atualizá-lo, sendo essencial para criar interfaces dinâmicas e interativas em aplicações 

---

## 1) O que é um React Hook (sem romantizar)

Um **Hook** é simplesmente uma **função especial** que permite que um componente funcional:

* tenha **estado**
* tenha **efeitos colaterais**
* acesse recursos do ciclo de vida

Antes dos Hooks, **componentes funcionais eram “burros”**: só recebiam props e renderizavam JSX.
Quem tinha estado eram **classes**.

Hooks surgiram para:

* eliminar classes
* tornar lógica reutilizável
* deixar o fluxo mais previsível

👉 Hooks **não são mágicos**.
São funções que o React observa de forma muito específica.

---

## 2) O que o `useState` realmente faz

```jsx
const [contador, setContador] = useState(0);
```

Mentalmente, isso significa:

1. O React cria um **slot interno de memória**
2. Associa esse slot à **posição do hook**
3. Guarda o valor inicial (`0`)
4. Retorna:

   * o valor atual
   * uma função que **avisa o React que o valor mudou**

Importante:

> `useState` **não altera o valor diretamente**.
> Ele **agenda uma atualização**.

---

## 3) Por que a imutabilidade é obrigatória

React **não observa variáveis**.
Ele observa **referências**.

Exemplo errado:

```jsx
const [user, setUser] = useState({ nome: "Ana" });

user.nome = "Maria"; // mutação direta
setUser(user);
```

O problema:

* A referência do objeto é a mesma
* Para o React: “nada mudou”
* Resultado: **não re-renderiza**

---

## 4) Como o React detecta mudanças

React faz algo conceitualmente assim:

```js
Object.is(valorAnterior, novoValor)
```

Se for **a mesma referência**, ele ignora.

Por isso, você **cria um novo objeto**:

```jsx
setUser({
  ...user,
  nome: "Maria"
});
```

Agora:

* Nova referência
* React detecta mudança
* Re-render acontece

---

## 5) Imutabilidade em arrays (caso clássico)

Errado:

```jsx
const [lista, setLista] = useState([]);

lista.push("novo item");
setLista(lista);
```

Certo:

```jsx
setLista([...lista, "novo item"]);
```

Ou:

```jsx
setLista(prev => [...prev, "novo item"]);
```

Opinião direta:

> Sempre prefira a forma com `prev` quando o novo estado depende do anterior.

---

## 6) Quando usar `useState`

Use `useState` quando:

* Um dado **muda ao longo do tempo**
* Essa mudança **impacta o que é renderizado**
* O estado pertence àquele componente

Exemplos:

* inputs de formulário
* abrir/fechar modal
* lista de produtos
* loading
* erro

---

## 7) Quando NÃO usar `useState`

Não use quando:

* O valor nunca muda
* O valor pode ser derivado de props
* O valor pode ser calculado no render

Exemplo errado:

```jsx
const [total, setTotal] = useState(preco * qtd);
```

Correto:

```jsx
const total = preco * qtd;
```

---

## 8) Por que o `setState` é assíncrono (conceitualmente)

```jsx
setContador(contador + 1);
setContador(contador + 1);
```

Resultado: incrementa **1**, não 2.

Porque:

* React **agrupa atualizações** (batching)
* `contador` ainda é o valor antigo

Forma correta:

```jsx
setContador(prev => prev + 1);
setContador(prev => prev + 1);
```

Agora funciona.

---

## 9) Regras dos Hooks (isso é crítico)

Hooks:

1. **Só no topo do componente**
2. **Nunca dentro de if, loop ou função**
3. **Sempre na mesma ordem**

Por quê?
Porque o React associa o estado **pela posição do hook**, não pelo nome.

---

* Hooks = acesso controlado ao mecanismo interno do React
* `useState` = cria estado reativo
* Imutabilidade = como o React detecta mudanças
* Nova referência → re-render
* Mesma referência → nada acontece

---
