# Consumindo Dados da API (Backend)

Vou assumir um **backend Spring REST clássico** e um **front em React usando Fetch API**, sem Axios, sem Redux, sem frescura.

---

## 1. O contrato mental: backend manda regras, front consome

Antes de código, grava isso:

* **Spring** expõe **endpoints REST** (URLs + método HTTP)
* **React** só:

  * dispara requisições
  * envia dados (body, params, headers)
  * interpreta respostas (JSON, status HTTP)

O front **não conhece** regras de negócio. Ele conversa com o backend como se fosse um cliente burro.

---

## 2. Exemplo de domínio simples (realista)

Vamos imaginar um backend de **Tasks**.

### Entidade

```java
Task {
  Long id;
  String title;
  boolean completed;
}
```

### Base URL do backend

```
http://localhost:8080/api/tasks
```

---

## 3. GET – buscar dados (listar e buscar por id)

### Backend (Spring)

#### Listar todas

```java
@GetMapping
public List<Task> findAll() {
    return taskService.findAll();
}
```

#### Buscar por id

```java
@GetMapping("/{id}")
public Task findById(@PathVariable Long id) {
    return taskService.findById(id);
}
```

---

### Front (React + Fetch)

#### Buscar todas as tasks

```js
useEffect(() => {
  fetch("http://localhost:8080/api/tasks")
    .then(response => {
      if (!response.ok) {
        throw new Error("Erro ao buscar tasks");
      }
      return response.json();
    })
    .then(data => setTasks(data))
    .catch(error => console.error(error));
}, []);
```

O que está acontecendo de verdade:

* `fetch` faz um **GET automático**
* o backend devolve JSON
* `response.json()` transforma em objeto JS

---

#### Buscar uma task por id

```js
function fetchTaskById(id) {
  fetch(`http://localhost:8080/api/tasks/${id}`)
    .then(res => res.json())
    .then(data => setTask(data));
}
```

---

## 4. POST – criar recurso (enviar dados)

### Backend (Spring)

```java
@PostMapping
public Task create(@RequestBody Task task) {
    return taskService.save(task);
}
```

Repara:

* `@RequestBody` = espera JSON no corpo da requisição

---

### Front (React)

```js
function createTask() {
  fetch("http://localhost:8080/api/tasks", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      title: "Estudar Fetch API",
      completed: false
    })
  })
    .then(res => res.json())
    .then(newTask => {
      setTasks(prev => [...prev, newTask]);
    });
}
```

Ponto-chave:

* `headers` dizem ao backend que é JSON
* `body` sempre em string
* Spring converte automaticamente para objeto Java

---

## 5. PUT – atualizar recurso inteiro

### Backend

```java
@PutMapping("/{id}")
public Task update(
    @PathVariable Long id,
    @RequestBody Task task
) {
    return taskService.update(id, task);
}
```

---

### Front

```js
function updateTask(task) {
  fetch(`http://localhost:8080/api/tasks/${task.id}`, {
    method: "PUT",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(task)
  })
    .then(res => res.json())
    .then(updated => {
      setTasks(tasks =>
        tasks.map(t => (t.id === updated.id ? updated : t))
      );
    });
}
```

PUT é:

* enviar o objeto completo
* substituir o recurso no backend

---

## 6. PATCH – atualizar parcialmente

### Backend

```java
@PatchMapping("/{id}/complete")
public Task markAsCompleted(@PathVariable Long id) {
    return taskService.markCompleted(id);
}
```

---

### Front

```js
function completeTask(id) {
  fetch(`http://localhost:8080/api/tasks/${id}/complete`, {
    method: "PATCH"
  })
    .then(res => res.json())
    .then(updated => {
      setTasks(tasks =>
        tasks.map(t => (t.id === updated.id ? updated : t))
      );
    });
}
```

PATCH é perfeito quando:

* você muda **estado**
* não quer enviar o objeto inteiro

---

## 7. DELETE – remover recurso

### Backend

```java
@DeleteMapping("/{id}")
public void delete(@PathVariable Long id) {
    taskService.delete(id);
}
```

---

### Front

```js
function deleteTask(id) {
  fetch(`http://localhost:8080/api/tasks/${id}`, {
    method: "DELETE"
  })
    .then(() => {
      setTasks(tasks => tasks.filter(t => t.id !== id));
    });
}
```

Normalmente DELETE:

* não retorna body
* só status (204, 200)

---

## 8. Status HTTP importam (muito)

No backend:

* `200 OK` → sucesso
* `201 Created` → POST bem-sucedido
* `204 No Content` → DELETE sem retorno
* `400` → erro do cliente
* `404` → não encontrado
* `500` → erro do servidor

No front:

```js
if (!response.ok) {
  throw new Error("Erro HTTP");
}
```

Se você ignora isso, você está programando no escuro.

---

## 9. Padrão mental definitivo

Sempre pense assim:

| Ação            | Método | Front faz               |
| --------------- | ------ | ----------------------- |
| Buscar lista    | GET    | fetch(url)              |
| Buscar por id   | GET    | fetch(url/id)           |
| Criar           | POST   | fetch(url, { body })    |
| Atualizar tudo  | PUT    | fetch(url/id, { body }) |
| Atualizar parte | PATCH  | fetch(url/id/ação)      |
| Deletar         | DELETE | fetch(url/id)           |

---