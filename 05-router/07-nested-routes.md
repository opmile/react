# Rotas Aninhadas

---

## O problema que nested routes resolvem

Sem nested routes, você acaba repetindo layout:

* menu
* sidebar
* header
* estrutura de página

Toda rota renderiza **tudo de novo**.

Mas, conceitualmente, muitas telas são assim:

```
Dashboard
 ├── Visão geral
 ├── Tarefas
 └── Configurações
```

A URL muda **dentro de um mesmo contexto visual**.

Nested routes existem para isso:

> permitir que **uma rota tenha sub-rotas que compartilham layout**.

---

## A ideia central 

Uma **rota pai**:

* define um layout
* permanece renderizada

As **rotas filhas**:

* mudam apenas uma parte da tela
* entram em um “espaço reservado”

Esse espaço reservado é o `<Outlet />`.

---

## Código: rota pai com layout

`pages/Dashboard.jsx`

```jsx
import { Outlet, Link } from "react-router-dom";

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>

      <nav>
        <Link to="overview">Overview</Link>
        <Link to="tasks">Tasks</Link>
        <Link to="settings">Settings</Link>
      </nav>

      <hr />

      <Outlet />
    </div>
  );
}
```

`<Outlet />` é onde a rota filha aparece.

---

## Declarando as rotas aninhadas

`App.jsx`

```jsx
import { Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import Dashboard from "./pages/Dashboard";
import Overview from "./pages/Overview";
import Tasks from "./pages/Tasks";
import Settings from "./pages/Settings";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />

      <Route path="/dashboard" element={<Dashboard />}>
        <Route path="overview" element={<Overview />} />
        <Route path="tasks" element={<Tasks />} />
        <Route path="settings" element={<Settings />} />
      </Route>
    </Routes>
  );
}
```

Repara:

* rotas filhas **não começam com `/`**
* elas são relativas ao pai

---

## Páginas filhas (simples)

`pages/Overview.jsx`

```jsx
export default function Overview() {
  return <p>Overview</p>;
}
```

`pages/Tasks.jsx`

```jsx
export default function Tasks() {
  return <p>Tasks</p>;
}
```

---

## Como a navegação funciona na prática

* `/dashboard` → renderiza `Dashboard`
* `/dashboard/overview` → renderiza `Dashboard` + `Overview`
* `/dashboard/tasks` → renderiza `Dashboard` + `Tasks`

O layout **não some**, só o conteúdo do `<Outlet />` muda.

---

## Link relativo

Dentro de `Dashboard`:

```jsx
<Link to="tasks">Tasks</Link>
```

Isso vira automaticamente:

```
/dashboard/tasks
```

Sem precisar repetir o caminho inteiro.

---

## Erro clássico

Esquecer o `<Outlet />`.

Resultado:

* URL muda
* nada aparece
* bug confuso

Regra simples:

> rota pai com filhos **precisa** de `<Outlet />`.

---

## Quando usar nested routes

Use quando:

* telas compartilham layout
* existe hierarquia clara de navegação
* URLs refletem essa hierarquia

Não use quando:

* telas são independentes
* layout muda completamente

---

> Nested routes não são sobre URL.
> São sobre **layout compartilhado**.

---

## Sobre `index`

O `index` define a **rota filha padrão** do `dashboard`, é o “estado inicial” de uma rota pai.

> é o que aparece **quando você acessa exatamente a rota pai**, sem nada depois.

```jsx
<Route path="dashboard" element={<Dashboard />}>
  <Route index element={<Home />} />
  <Route path="settings" element={<Settings />} />
</Route>
```

O comportamento é:

* `/dashboard`
  → renderiza `Dashboard`
  → no `<Outlet />`, renderiza **`Home`**

* `/dashboard/settings`
  → renderiza `Dashboard`
  → no `<Outlet />`, renderiza **`Settings`**

O `index` existe porque:

* a rota pai precisa saber **qual filho renderizar por padrão**
* ele evita ter que criar algo como `path=""`
* deixa explícito que aquela é a **entrada do layout**

Sem `index`:

* `/dashboard` renderiza só `Dashboard`
* o `<Outlet />` fica vazio

---

## Com ou Sem `index`

No código de explicação de rotas aninhadas **não existe index route**.

* `/dashboard`
  → renderiza **Dashboard**
  → `<Outlet />` fica **vazio**

* `/dashboard/overview`
  → renderiza `Dashboard + Overview`

* `/dashboard/tasks`
  → renderiza `Dashboard + Tasks`

Ou seja: o layout aparece, o conteúdo não.

Isso **não é errado**.
É só uma decisão de UX.

---

### Então quando o index é necessário?

O `index` só é necessário quando você quer que **alguma coisa apareça automaticamente** ao entrar na rota pai.

Se você espera que `/dashboard` renderize algo (ex: overview), você tem **duas opções**.

---

**Opção 1 — Index route (a forma correta)**

```jsx
<Route path="/dashboard" element={<Dashboard />}>
  <Route index element={<Overview />} />
  <Route path="tasks" element={<Tasks />} />
  <Route path="settings" element={<Settings />} />
</Route>
```

* `/dashboard` → mostra `Overview`
* `/dashboard/tasks` → mostra `Tasks`

---

**Opção 2 — Redirecionar manualmente (menos elegante)**

Você pode deixar o outlet vazio e forçar um redirect:

```jsx
import { Navigate } from "react-router-dom";

<Route path="/dashboard" element={<Dashboard />}>
  <Route index element={<Navigate to="overview" replace />} />
  <Route path="overview" element={<Overview />} />
</Route>
```

Aqui:

* `/dashboard` redireciona pra `/dashboard/overview`

Funciona, mas é outra intenção.

---

## Por que o exemplo “funciona” sem index?

Porque:

* o React Router **não exige** filho padrão
* o `<Outlet />` pode ficar vazio
* o layout continua válido

Mas UX costuma ficar estranha:

* tela “em branco”
* menu aparece, conteúdo não

---

* Rota pai **não é obrigada** a ter index
* Se `/rota` precisa mostrar algo → use index
* Se `/rota` é só um contêiner de layout → index é opcional

---