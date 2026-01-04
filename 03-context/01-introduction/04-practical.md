# `useContext` uso prático

---

## Passo 0 — decida se Context é realmente necessário

Antes de qualquer código, responda:

* esse estado precisa ser acessado por **vários componentes**?
* esses componentes estão em **níveis diferentes da árvore**?
* esse estado representa um **conceito de domínio** (to-dos, auth, usuário, tema)?

Se a resposta for sim → Context faz sentido.
Se não → `useState` local resolve.

---

## Passo 1 — crie o Context (arquivo dedicado)

Sempre crie o Context **fora dos componentes**.

```jsx
import { createContext } from "react";

export const TodoContext = createContext(null);
```

Boas práticas aqui:

* valor inicial geralmente é `null`
* isso permite detectar uso incorreto depois
* Context **não contém lógica**

Esse passo define apenas o **canal de comunicação**.

---

## Passo 2 — crie o Provider (onde o estado vive)

Aqui acontece tudo que importa.

```jsx
import { useState } from "react";
import { TodoContext } from "./TodoContext";

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

  const value = {
    todos,
    toggleTodo,
  };

  return (
    <TodoContext.Provider value={value}>
      {children}
    </TodoContext.Provider>
  );
}
```

Regras importantes:

* **todo o estado compartilhado vive aqui**
* **toda regra de negócio vive aqui**
* você **não expõe setters crus**
* o `value` define a **API pública do contexto**

Esse componente é a **fonte única de verdade**.

---

## Passo 3 — envolva a árvore com o Provider

O Provider define o **escopo** do estado.

```jsx
function App() {
  return (
    <TodoProvider>
      <TodoPage />
    </TodoProvider>
  );
}
```

Tudo que estiver **dentro**:

* pode acessar o Context

Tudo que estiver **fora**:

* não pode

Simples, explícito e previsível.

---

## Passo 4 — crie um custom hook para consumir o Context

Esse passo é **obrigatório em código profissional**.

```jsx
import { useContext } from "react";
import { TodoContext } from "./TodoContext";

export function useTodos() {
  const context = useContext(TodoContext);

  if (!context) {
    throw new Error("useTodos deve ser usado dentro de TodoProvider");
    // garante que o hook de contexto só seja usado dentro do escopo correto do Provider
  }

  return context;
}
```

Por que isso é tão importante:

* centraliza o acesso ao Context
* evita `useContext` espalhado
* protege contra uso fora do Provider: este hook só funciona se houver um Provider acima.
* desacopla componentes da implementação

Esse hook é o **contrato de dependência**.

---

## Passo 5 — consuma o estado nos componentes

Agora os componentes só **pedem o que precisam**.

### Exemplo: lista

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

### Exemplo: item

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

Perceba:

* nenhum componente cria estado global
* nenhum componente recebe props inúteis
* cada um consome **apenas o necessário**

---

## Passo 6 — entenda o fluxo completo (modelo mental)

Sempre pense assim:

1. estado nasce no Provider
2. Provider expõe uma API (`value`)
3. hook (`useTodos`) acessa essa API
4. componentes consomem o hook
5. ações alteram o estado no Provider
6. Provider re-renderiza
7. consumers reagem automaticamente

Se você entende esse ciclo, Context deixa de ser mágico.

---

Sempre que for criar um Context, você deve ter:

1. `createContext(null)`
2. `XProvider` com:

   * estado
   * regras
   * `value` bem definido
3. `useX()` como única forma de acesso
4. Provider envolvendo a árvore
5. componentes burros e consumidores

Se faltar um desses passos, o design está incompleto.

---
