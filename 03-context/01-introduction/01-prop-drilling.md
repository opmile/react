# Sobre Prop Drilling

---

## O que é prop drilling

**Prop drilling** acontece quando um dado precisa sair de um componente “pai” e atravessar **vários componentes intermediários** apenas para chegar a um componente mais profundo — mesmo que esses intermediários **não usem esse dado**.

Eles só viram “correios”.

---

## Exemplo clássico com To-Do List (com prop drilling)

Imagine essa árvore:

```
App
 └─ TodoPage
     └─ TodoList
         └─ TodoItem
```

O estado vive no topo, porque faz sentido: a lista de tarefas é global dentro da página.

### App.jsx

```jsx
function App() {
  const [todos, setTodos] = useState([]);

  function toggleTodo(id) {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id
          ? { ...todo, done: !todo.done }
          : todo
      )
    );
  }

  return (
    <TodoPage
      todos={todos}
      toggleTodo={toggleTodo}
    />
  );
}
```

### TodoPage.jsx

```jsx
function TodoPage({ todos, toggleTodo }) {
  return (
    <TodoList
      todos={todos}
      toggleTodo={toggleTodo}
    />
  );
}
```

### TodoList.jsx

```jsx
function TodoList({ todos, toggleTodo }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          toggleTodo={toggleTodo}
        />
      ))}
    </ul>
  );
}
```

### TodoItem.jsx

```jsx
function TodoItem({ todo, toggleTodo }) {
  return (
    <li onClick={() => toggleTodo(todo.id)}>
      {todo.done ? "✅" : "⬜"} {todo.text}
    </li>
  );
}
```

---

## Onde está o problema?

Tecnicamente: **funciona**.
Arquiteturalmente: começa a feder rápido.

### Problemas reais do prop drilling

1. **Acoplamento excessivo**

   * `TodoPage` e `TodoList` precisam conhecer props que **não usam**
   * Se você mudar o nome ou a assinatura de `toggleTodo`, quebra tudo

2. **Componentes viram buracos de passagem**

   * Eles existem só pra repassar props
   * Isso mata a ideia de componentes reutilizáveis

3. **Escalabilidade péssima**

   * Adiciona `deleteTodo`, `editTodo`, `filterTodo`
   * Agora são 4, 5, 6 props descendo a árvore

4. **Manutenção horrível**

   * Um bug num handler → rastrear props em cadeia
   * Debug vira caça ao tesouro

Resumo direto:
**prop drilling transforma sua árvore de componentes numa gambiarra hierárquica.**

---

## Quando prop drilling É aceitável?

Opinião forte: só em dois casos

1. **Árvore rasa (1 ou 2 níveis)**
2. **Props puramente visuais**

   * `variant`
   * `size`
   * `disabled`

Estado de domínio (to-dos, usuário, auth, carrinho)?
Prop drilling é erro de design.

---

## A solução: Context API

Ou, como eu prefiro chamar: **fonte única de verdade bem posicionada**

A ideia é simples:

* O estado continua centralizado
* Mas **os componentes acessam diretamente**
* Sem intermediários inúteis

---

## Refatorando o To-Do com Context API

### 1. Criando o contexto

```jsx
import { createContext, useContext, useState } from "react";

const TodoContext = createContext(null);

export function TodoProvider({ children }) {
  const [todos, setTodos] = useState([]);

  function toggleTodo(id) {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id
          ? { ...todo, done: !todo.done }
          : todo
      )
    );
  }

  return (
    <TodoContext.Provider value={{ todos, toggleTodo }}>
      {children}
    </TodoContext.Provider>
  );
}

export function useTodos() {
  const context = useContext(TodoContext);
  if (!context) {
    throw new Error("useTodos deve ser usado dentro de TodoProvider");
  }
  return context;
}
```

Aqui você criou:

* **estado central**
* **API explícita**
* **contrato claro de uso**

---

### 2. Envolvendo a aplicação

```jsx
function App() {
  return (
    <TodoProvider>
      <TodoPage />
    </TodoProvider>
  );
}
```

---

### 3. Componentes consumindo direto (sem drilling)

#### TodoList.jsx

```jsx
function TodoList() {
  const { todos } = useTodos();

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}
```

#### TodoItem.jsx

```jsx
function TodoItem({ todo }) {
  const { toggleTodo } = useTodos();

  return (
    <li onClick={() => toggleTodo(todo.id)}>
      {todo.done ? "✅" : "⬜"} {todo.text}
    </li>
  );
}
```

Repara no ganho brutal:

* `TodoPage` não passa nada
* `TodoList` só consome o que precisa
* `TodoItem` acessa o estado direto

---

## Por que isso é “fonte única de verdade”?

Porque agora:

* Existe **um único lugar** que define:

  * quais dados existem
  * como eles mudam
* Nenhum componente mantém cópia local inconsistente
* Nenhuma prop precisa ser sincronizada manualmente

Isso é o mesmo princípio que você vai ver depois em:

* Redux
* Zustand
* Jotai
* React Query (para dados remotos)

Context API é o **primeiro degrau sério** nisso.

---

## Opinião final (importante)

* Context **não é pra tudo**

* Context é ótimo para:

  * estado de domínio compartilhado
  * autenticação
  * tema
  * preferências
  * to-dos, carrinho, usuário

* Context **não é bom** para:

  * estados ultra locais
  * animações
  * inputs isolados

Se você sentir que está passando props demais:
**não force — o código está pedindo contexto.**