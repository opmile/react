# Navegação

---

## 1. Navegação declarativa — `<Link />`

É o básico, mas precisa estar no mapa mental.

```jsx
<Link to="/dashboard">Dashboard</Link>
```

Use quando:

* o usuário **clica**
* a navegação faz parte da **UI**
* você quer algo sem efeitos colaterais

Ponto forte:

* sem recarregar a página
* acessível
* simples

Opinião direta:
se existe um botão ou texto clicável, **comece por `<Link />`**.

---

## 2. Navegação imperativa — `useNavigate()`

Aqui você **manda o app ir pra outro lugar**.

```jsx
import { useNavigate } from "react-router-dom";

const navigate = useNavigate();

navigate("/login");
```

Use quando:

* algo acontece **como consequência de uma ação**
* não existe clique direto
* você precisa decidir a rota **por lógica**

### Exemplo real: submit de formulário

```jsx
function Login() {
  const navigate = useNavigate();

  function handleSubmit(e) {
    e.preventDefault();

    // validação, request, etc
    navigate("/dashboard");
  }

  return <form onSubmit={handleSubmit}>...</form>;
}
```

Aqui `<Link />` seria errado.
Não tem link, tem **fluxo**.

---

## 3. Navegação condicional — `<Navigate />`

Esse componente é um **redirecionamento declarativo**.

```jsx
import { Navigate } from "react-router-dom";

if (!isAuthenticated) {
  return <Navigate to="/login" replace />;
}
```

Use quando:

* a navegação depende do **estado atual**
* você quer **bloquear renderização**
* você está protegendo rotas

Ele funciona como:

> “Se isso for verdade, você **não pode** estar aqui”

---

## 4. `<Navigate />` vs `useNavigate()` (diferença mental)

Essa parte é crucial.

### `<Navigate />`

* acontece **durante o render**
* é **declarativo**
* substitui o componente

Pense como:

> “Essa tela não deveria existir nesse estado”

### `useNavigate()`

* acontece **por evento**
* é **imperativo**
* executa uma ação

Pense como:

> “Algo aconteceu, agora vá pra outra página”

---

## 5. Proteção de rotas: jeito certo

### Errado (com `useNavigate` dentro do render)

```jsx
if (!isAuth) {
  navigate("/login"); // anti-pattern
}
```

Isso causa loop e warnings.

---

### Certo (com `<Navigate />`)

```jsx
function PrivateRoute({ children }) {
  if (!isAuth) {
    return <Navigate to="/login" replace />;
  }

  return children;
}
```

Esse é o uso **clássico** e correto do `<Navigate />`.

---

## 6. Navegação com histórico (replace vs push)

Tanto `<Navigate />` quanto `navigate()` aceitam `replace`.

```jsx
navigate("/login", { replace: true });
```

ou

```jsx
<Navigate to="/login" replace />
```

Diferença prática:

* `replace`: usuário **não volta** com o botão “voltar”
* sem `replace`: empilha no histórico

Use `replace` quando:

* login
* logout
* redirecionamento automático

---

## 7. Navegação relativa

Com `useNavigate`:

```jsx
navigate("../");
```

Útil em rotas aninhadas:

* voltar um nível
* navegar dentro do contexto atual

Evita hardcode de URL.

---

## 8. Passando estado na navegação

```jsx
navigate("/checkout", {
  state: { total: 199 }
});
```

E depois:

```jsx
import { useLocation } from "react-router-dom";

const { state } = useLocation();
```

Use isso:

* para dados temporários
* não persistentes
* que não merecem URL

Não use pra:

* dados críticos
* reload
* deep linking

---
