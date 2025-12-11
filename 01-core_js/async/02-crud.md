# CRUD

Vou dividir em três partes, bem enxutas e conscientes:

1. O que é esse CRUD no contexto certo
2. Arquitetura mínima do projeto (frontend puro → backend HTTP)
3. Um mini-projeto funcional (JS + fetch + DOM)

---

## O que estamos construindo (mentalmente)

Queremos fechar este ciclo completo:

```
Usuário → Interface → HTTP → Backend (Spring) → HTTP → Interface
```

Sem React, apenas:

* HTML
* JavaScript moderno
* `fetch`
* JSON

Aqui você aprende:

* como o frontend **conversa de verdade** com o backend
* como o backend espera receber dados
* onde React entra depois (spoiler: só facilita renderização)

---

## Contrato com o backend (essencial)

Vamos assumir um backend REST padrão em Spring Boot:

```
GET    /users        → lista usuários
POST   /users        → cria usuário
PUT    /users/{id}   → atualiza usuário
DELETE /users/{id}   → remove usuário
```

Usuário em JSON:

```json
{
  "id": 1,
  "name": "Ana",
  "email": "ana@email.com"
}
```

Esse contrato é o **ponto de encontro**. O frontend só precisa respeitar isso.

---

## Mini-projeto CRUD (frontend puro)

### Estrutura mínima

```
index.html
script.js
```

Nada mais.

---

## `index.html`

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>CRUD Usuários</title>
</head>
<body>

  <h1>Usuários</h1>

  <form id="user-form">
    <input type="text" id="name" placeholder="Nome" required />
    <input type="email" id="email" placeholder="Email" required />
    <button type="submit">Salvar</button>
  </form>

  <ul id="user-list"></ul>

  <script src="script.js"></script>
</body>
</html>
```

Nada de mágico aqui. DOM cru.

---

## `script.js` — explicação progressiva

### 1️⃣ Configuração base

```js
const API_URL = "https://localhost:8080/users"

const form = document.getElementById("user-form");
const list = document.getElementById("user-list");
```

Simples e direto.

---

### READ — buscar e renderizar usuários

```js
async function loadUsers() {
    const response = await fetch(`${API_URL}`);
    const users = await response.json();

    list.innerHTML = "";

    users.forEach(user => {
        const li = document.createElement("li");
        
        li.innerHTML = `
            ${user.name} (${user.email})
          <button onclick="deleteUser(${user.id})">Excluir</button>
        `;

        list.appendChild(li);
    });
}
```

Aqui você já vê:

* fetch
* async/await
* transformação de JSON
* render manual (isso vira JSX depois)

---

### CREATE — enviar dados para o backend

```js
form.addEventListener("submit", async (event) => {
  event.preventDefault();

  const user = {
    name: name.value,
    email: email.value
  };

  await fetch(API_URL, {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(user)
  });

  form.reset();
  loadUsers();
});
```

Aqui está o **núcleo da integração frontend ↔ backend**.

Se você entende esse trecho, entende React depois.

---

### DELETE — fechar o ciclo

```js
async function deleteUser(id) {
  await fetch(`${API_URL}/${id}`, {
    method: "DELETE"
  });

  loadUsers();
}
```

Simples, limpo, direto.

---

### Boot inicial

```js
loadUsers();
```

Pronto. CRUD completo.

---

## O que você acabou de aprender (importante)

Sem perceber, você dominou:

* HTTP methods (`GET`, `POST`, `DELETE`)
* JSON como contrato
* `fetch` como cliente HTTP
* estado manual (lista de usuários)
* renderização baseada em dados

### Isso é React *sem React*.

---

## Onde React entra depois (visão honesta)

No código acima:

* `loadUsers()` → vira `useEffect`
* `users` → vira `useState`
* `innerHTML` → vira JSX
* `onclick` → vira props

Nada conceitual muda. Só ergonomia.

---


