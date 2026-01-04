# CRUD (sem update)

---

## 1. Modelo mental correto (guarde isso)

> **O estado vive no Context.
> O localStorage apenas espelha esse estado.**

Isso implica três decisões arquiteturais claras:

1. **Nenhum componente acessa `localStorage`**
2. **Nenhum componente cria ou mantém cópia do estado**
3. **Toda leitura e escrita passam pelo Provider**

O Context vira:

* a fonte única de verdade
* o ponto central de regras
* o único lugar que conhece persistência

---

## 2. Estrutura geral do CRUD (Create, Read, Delete)

Vamos assumir:

* entidade: `todo`
* operações: criar, listar, remover
* sem edição (como você pediu)

Arquitetura:

```
TodoContext
 ├─ estado (todos)
 ├─ ações (addTodo, deleteTodo)
 ├─ hidratação (localStorage → estado)
 └─ persistência (estado → localStorage)
```

---

## 3. Criando o Context e o Provider

### TodoContext.js

```jsx
import { createContext, useContext, useEffect, useState } from "react";

const TodoContext = createContext(null);
```

---

## 4. Provider: onde tudo acontece

Aqui está o **núcleo da aplicação**.

```jsx
export function TodoProvider({ children }) {
  const [todos, setTodos] = useState([]);
```

### 4.1 READ — hidratação inicial (localStorage → estado)

Isso roda **uma vez**, quando o Provider monta.

```jsx
  useEffect(() => {
    const storedTodos = localStorage.getItem("todos");

    if (storedTodos) {
      setTodos(JSON.parse(storedTodos));
    }
  }, []);
```

Pontos importantes:

* só o Provider acessa `localStorage`
* o estado começa vazio
* se existir persistência, ela hidrata o estado

---

### 4.2 Persistência automática (estado → localStorage)

Aqui entra exatamente a alternativa que você citou:

```jsx
  useEffect(() => {
    localStorage.setItem("todos", JSON.stringify(todos));
  }, [todos]);
```

O que isso faz:

* sempre que `todos` muda
* o efeito roda
* o localStorage é atualizado

Isso garante:

* consistência
* zero duplicação de lógica
* nenhuma chance de esquecer de salvar

---

## 5. CREATE — criando uma tarefa

```jsx
  function addTodo(text) {
    const newTodo = {
      id: Date.now(),
      text,
    };

    setTodos(prev => [...prev, newTodo]);
  }
```

Repare no detalhe crítico:

* **nenhuma chamada ao localStorage aqui**
* só atualização de estado
* persistência é consequência, não responsabilidade

---

## 6. DELETE — removendo uma tarefa

```jsx
  function deleteTodo(id) {
    setTodos(prev => prev.filter(todo => todo.id !== id));
  }
```

Mesmo padrão:

* estado muda
* efeito cuida da persistência
* regras simples e previsíveis

---

## 7. Expondo a API pública do Context

```jsx
  const value = {
    todos,
    addTodo,
    deleteTodo,
  };

  return (
    <TodoContext.Provider value={value}>
      {children}
    </TodoContext.Provider>
  );
}
```

Esse `value` é:

* o contrato da aplicação
* tudo que o resto do app pode fazer

---

## 8. Custom hook (obrigatório)

```jsx
export function useTodos() {
  const context = useContext(TodoContext);

  if (!context) {
    throw new Error("useTodos deve ser usado dentro de TodoProvider");
  }

  return context;
}
```

Isso garante:

* uso correto do Context
* erro imediato se alguém errar
* desacoplamento total dos componentes

---

## 9. Consumo nos componentes (exemplo rápido)

### Listagem

```jsx
function TodoList() {
  const { todos, deleteTodo } = useTodos();

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
          <button onClick={() => deleteTodo(todo.id)}>
            Remover
          </button>
        </li>
      ))}
    </ul>
  );
}
```

### Criação

```jsx
function AddTodo() {
  const { addTodo } = useTodos();
  const [text, setText] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    addTodo(text);
    setText("");
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button>Adicionar</button>
    </form>
  );
}
```

Nenhum componente:

* conhece `localStorage`
* conhece persistência
* mantém estado duplicado

---

## 10. Fluxo completo (resumo mental)

1. Provider monta
2. lê `localStorage`
3. hidrata o estado
4. UI renderiza
5. usuário cria/remove todo
6. estado muda
7. `useEffect([todos])` persiste automaticamente
8. refresh da página mantém tudo

Simples. Determinístico. Robusto.

---

## 11. Por que essa é a implementação correta

Porque ela respeita:

* **fonte única de verdade** (Context)
* **separação de responsabilidades**
* **reatividade do React**
* **persistência declarativa**
* **baixo acoplamento**

Isso escala:

* para múltiplos components
* para múltiplas telas
* para troca futura de storage (API, IndexedDB, backend)

---

> Context gerencia estado.
> localStorage persiste estado.
> `useEffect` conecta os dois.
