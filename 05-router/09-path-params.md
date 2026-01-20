# URLs Dinâmicas: `path params`

---

## O problema real

Você tem uma lista de coisas:

* tarefas
* usuários
* produtos
* posts

E quer algo assim:

* `/tasks` → lista
* `/tasks/42` → **uma tarefa específica**

Você **não vai** criar uma rota pra cada ID.
Você cria **uma rota com parâmetro**.

---

## A ideia central (guarda isso)

Uma **URL dinâmica** tem um *placeholder*:

```
/tasks/:id
```

Esse `:id` significa:

> “qualquer valor aqui entra como parâmetro”

O React Router:

* lê esse valor da URL
* te entrega dentro do componente

---

## 1. Declarando a rota com path param

```jsx
<Route path="/tasks" element={<Tasks />} />
<Route path="/tasks/:id" element={<TaskDetails />} />
```

Aqui:

* `/tasks/1`
* `/tasks/abc`
* `/tasks/999`

Tudo cai em `TaskDetails`.

---

## 2. Navegando para uma URL dinâmica

### A partir de uma lista

```jsx
import { Link } from "react-router-dom";

export default function Tasks() {
  const tasks = [
    { id: 1, title: "Estudar React Router" },
    { id: 2, title: "Fazer café" }
  ];

  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Link to={`/tasks/${task.id}`}>
            {task.title}
          </Link>
        </li>
      ))}
    </ul>
  );
}
```

Aqui você **monta a URL**.
Nada mágico.

---

## 3. Lendo o parâmetro da URL

Dentro da página de detalhe:

```jsx
import { useParams } from "react-router-dom";

export default function TaskDetails() {
  const { id } = useParams();

  return <h2>Tarefa {id}</h2>;
}
```

`useParams()` retorna um objeto:

```js
{ id: "1" }
```

⚠️ **Sempre string**.
Se precisar de número, converta.

---

## 4. Cenário real: buscar dados pelo ID

Aqui é onde isso faz sentido de verdade.

```jsx
import { useParams } from "react-router-dom";
import { useEffect, useState } from "react";

export default function TaskDetails() {
  const { id } = useParams();
  const [task, setTask] = useState(null);

  useEffect(() => {
    fetch(`/api/tasks/${id}`)
      .then(res => res.json())
      .then(data => setTask(data));
  }, [id]);

  if (!task) return <p>Carregando...</p>;

  return (
    <>
      <h2>{task.title}</h2>
      <p>{task.description}</p>
    </>
  );
}
```

Fluxo real:

1. URL muda (`/tasks/42`)
2. Router renderiza `TaskDetails`
3. `useParams()` lê `id`
4. Front busca `/api/tasks/42`
5. Backend Java responde
6. Tela renderiza

---

## 5. Usando path params com nested routes (comum em dashboards)

```jsx
<Route path="/dashboard/tasks" element={<Tasks />}>
  <Route path=":id" element={<TaskDetails />} />
</Route>
```

URL final:

```
/dashboard/tasks/42
```

E o `<Outlet />` continua funcionando normalmente.

---

## 6. O que path param **não é**

* ❌ não é query string (`?id=42`)
* ❌ não é estado global
* ❌ não é segurança

Ele representa:

> **identidade do recurso**

Se o dado tem identidade única, ele merece estar na URL.

---