# Axios

**Axios é um cliente HTTP** baseado em Promises. Ponto.

Ele resolve três dores clássicas do `fetch`:

* não transforma JSON manualmente
* não considera erro HTTP automaticamente
* não tem interceptação nativa (tokens, logs, retry)

Mental model correto:

> Axios ≈ fetch + boas decisões padrão

---

## Axios em JavaScript puro (base real)

### Instalação (via CDN, cenário simples)

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

### Intalação via `npm`

```bash
npm install axios
```

E depois, no código

```js
import axios from "axios";
```

---

### GET — buscar dados

```js
axios.get("http://localhost:8080/users")
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error);
  });
```

Aqui já aparece uma diferença brutal:

* `response.data` → já é o JSON
* erro HTTP (404, 500) → cai no `.catch`

---

### GET com async/await (forma moderna)

```js
async function loadUsers() {
  try {
    const response = await axios.get("http://localhost:8080/users");
    console.log(response.data);
  } catch (error) {
    console.error(error);
  }
}
```

Axios e `async/await` se encaixam **perfeitamente**.

---

## CRUD com Axios (JS puro, essencial)

### CREATE

```js
axios.post("http://localhost:8080/users", {
  name: "Ana",
  email: "ana@email.com"
});
```

Sem:

* `JSON.stringify`
* `headers` manuais
  Axios infere.

---

### UPDATE

```js
axios.put("http://localhost:8080/users/1", {
  name: "Ana Maria"
});
```

---

### DELETE

```js
axios.delete("http://localhost:8080/users/1");
```

Isso é o mesmo CRUD que você já fez com `fetch`, só que **mais enxuto**.

---

## Configuração global (onde Axios começa a brilhar)

Esse passo é **muito importante para React depois**.

### Criando uma instância

```js
const api = axios.create({
  baseURL: "http://localhost:8080",
  timeout: 5000,
  headers: {
    "Content-Type": "application/json"
  }
});
```

Agora suas chamadas ficam assim:

```js
api.get("/users");
api.post("/users", user);
```

Isso é arquitetura, não sintaxe.

---

## Interceptors (o verdadeiro motivo de usar Axios)

### Exemplo: token JWT automático

```js
api.interceptors.request.use(config => {
  const token = localStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

Aqui você ligou diretamente:

* frontend
* auth
* backend Spring Security

Com `fetch`, isso vira boilerplate repetido.

---

## Ponte natural com React

Agora vem o encaixe mental:

### Em React, isso vira um **service**

```js
// services/userService.js
import api from "./api";

export function getUsers() {
  return api.get("/users");
}
```

Uso em um componente:

```js
useEffect(() => {
  getUsers()
    .then(response => setUsers(response.data))
    .catch(error => console.error(error));
}, []);
```

Percebe?

* React **não sabe** o que é Axios
* React só consome Promises
* Axios fica isolado

Arquiteturalmente saudável.

---

## Comparação: Fetch vs Axios

Minha posição clara:

* `fetch` → entender fundamentos
* Axios → projetos reais

Axios vence em:

* menos código
* melhor erro
* interceptors
* integração com auth

Fetch vence em:

* zero dependência
* aprendizado conceitual

---

