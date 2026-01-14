# Criando Rotas com React Router

---

## 1. A ideia central: rotas como descrição de telas

No modo declarativo, você não “configura” rotas.
Você **descreve** a navegação da aplicação.

> “Dada a URL atual, qual interface deve aparecer?”

Não é sobre código ainda.
É sobre **mapear URLs para componentes visuais**.

---

## 2. O ponto de entrada: dar contexto de navegação à aplicação

Para o React Router funcionar, a aplicação precisa saber:

* onde está (URL atual) 
* quando a URL muda 

Isso acontece quando você “envolve” a aplicação com um **Router**.

**BrowserRouter** é o tipo de router do React Router que integra a aplicação React ao **histórico real do navegador**.

Ele usa a **History API** (`pushState`, `replaceState`) para:

* mudar a URL sem recarregar a página
* permitir uso normal de **voltar** e **avançar**
* manter URLs limpas (sem `#`)
* sincronizar navegação com o estado da aplicação

Na prática, o `BrowserRouter` é o componente que:

> observa a URL atual e informa ao React Router quando ela muda.

Por isso ele precisa envolver a aplicação inteira:
ele fornece o **contexto de navegação** para `<Routes>`, `<Route>`, `<Link>`, etc.

Ele é o router mais usado em SPAs clássicas porque:

* se comporta como a web “de verdade”
* funciona bem com backends separados (ex: React + Java)
* não exige hacks de URL

A única exigência é que o servidor esteja configurado para sempre devolver o `index.html`, permitindo que o React Router resolva as rotas no front.

---

## 3. O coração do sistema: rotas e correspondência

Depois de ter um Router, você declara **rotas**.

Cada rota define:

* um caminho (`/`, `/login`, `/tasks`) ==`path`
* um componente associado == `element`

Quando a URL bate com o caminho:

* o componente daquela rota é renderizado

> URL não chama componente, mas o **seleciona** componente

Você não navega para um componente, mas navega para uma **URL**.

---

## 4. Agrupando rotas: pensar em páginas, não em componentes

Rotas representam:

* páginas
* visões principais
* estados relevantes da aplicação

Componentes internos continuam sendo só componentes.

Exemplo conceitual:

* `/login` → página
* `/dashboard` → página
* botão, card, modal → **não são rotas**

Rotas são fronteiras de navegação, não de UI.

---

## 5. Navegação: como o usuário muda de rota

Criar rotas não basta.
O usuário precisa **navegar** entre elas.

Em SPA, isso não pode causar reload.

Por isso:

* você não usa `<a>`
* você usa um componente de navegação do router

Esse componente:

* muda a URL
* atualiza o histórico
* dispara o re-render correto

Tudo sem sair da aplicação.

Gancho forte:
→ por que `<a>` quebra SPA e `<Link>` não

---

## 6. O fluxo completo

1. Usuário acessa ou navega para uma URL
2. O Router detecta a mudança (pela consulta ao histórico)
3. O conjunto de rotas é avaliado
4. O componente correspondente é renderizado
5. O resto da aplicação permanece vivo

Nada é destruído desnecessariamente.
Nada é recarregado.

---

* rotas com parâmetros (ex: `/tasks/3`)
* layout compartilhado entre rotas
* rotas públicas vs privadas
* organização de rotas em projetos médios

---

# Prática

---

## 1. Instalação (uma vez só)

```bash
npm install react-router-dom
```

---

## 2. Envolvendo a aplicação com o Router

`main.jsx`:

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

Isso dá **contexto de navegação** pra aplicação inteira.

---

## 3. Declarando as rotas (o coração)

`App.jsx`

```jsx
import { Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import Login from "./pages/Login";
import Dashboard from "./pages/Dashboard";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/login" element={<Login />} />
      <Route path="/dashboard" element={<Dashboard />} />
    </Routes>
  );
}
```

**URL seleciona componente**.

---

## 4. Criando as páginas 

`pages/Home.jsx`

```jsx
export default function Home() {
  return <h1>Home</h1>;
}
```

`pages/Login.jsx`

```jsx
export default function Login() {
  return <h1>Login</h1>;
}
```

`pages/Dashboard.jsx`

```jsx
export default function Dashboard() {
  return <h1>Dashboard</h1>;
}
```

---

## 5. Navegação SEM reload (isso importa)

```jsx
import { Link } from "react-router-dom";

export default function Menu() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/login">Login</Link>
      <Link to="/dashboard">Dashboard</Link>
    </nav>
  );
}
```

**NUNCA** use `<a href="/">` em SPA.

E em `App.jsx`:

```jsx
import { Routes, Route } from "react-router-dom";
import Menu from "./components/Menu";
import Home from "./pages/Home";
import Login from "./pages/Login";
import Dashboard from "./pages/Dashboard";

export default function App() {
  return (
    <>
      <Menu />

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/dashboard" element={<Dashboard />} />
      </Routes>
    </>
  );
}
```

---

1. Usuário clica no `Link`
2. URL muda
3. Router detecta
4. Componente correspondente renderiza
5. App continua viva

Sem reload. Sem perda de estado.

---
