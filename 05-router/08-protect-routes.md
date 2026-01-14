# Proteção de Rotas

---

> **Proteger rota = decidir se um componente pode ou não ser renderizado.**

Não tem criptografia, não tem segurança real aqui.
É **controle de acesso de UI**.

---

## 1. O problema real que estamos resolvendo

Você tem isso:

* `/login` → público
* `/dashboard` → só usuário logado

Em SPA:

* o backend **já não controla navegação**
* então o front **precisa decidir o que mostrar**

Se o usuário não pode ver uma tela:

* você **não renderiza**
* e **redireciona**

Simples assim.

---

## 2. A estratégia clássica (e correta)

Você cria um **componente intermediário**, algo como:

> “Antes de renderizar essa rota, verifica se pode.”

Esse componente é normalmente chamado de:

* `ProtectedRoute`
* `RequireAuth`
* `PrivateRoute`

Nome não importa. Papel sim.

---

## 3. Exemplo mínimo de estado de autenticação

Vamos assumir algo simples:

```jsx
function useAuth() {
  return {
    isAuthenticated: false // ou true
  };
}
```

(Depois isso pode vir de Context, Zustand, Redux, etc.)

---

## 4. Criando o componente de proteção

Aqui está o **núcleo da ideia**.

```jsx
import { Navigate, Outlet } from "react-router-dom";

function ProtectedRoute() {
  const { isAuthenticated } = useAuth();

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}
```

O que isso faz:

* se **não está logado** → redireciona
* se está logado → libera as rotas filhas

Nada mais.

---

## 5. Aplicando a proteção nas rotas (forma correta)

Você **não protege rota por rota**.
Você protege **um grupo**.

```jsx
<Routes>
  <Route path="/login" element={<Login />} />

  <Route element={<ProtectedRoute />}>
    <Route path="/dashboard" element={<Dashboard />}>
      <Route index element={<Overview />} />
      <Route path="settings" element={<Settings />} />
    </Route>
  </Route>
</Routes>
```

Aqui acontece o seguinte:

* tudo dentro de `<ProtectedRoute />` exige autenticação
* `ProtectedRoute` decide se o `<Outlet />` aparece
* se não aparecer, o redirecionamento acontece

---

## 6. Fluxo real quando o usuário acessa `/dashboard`

### Usuário NÃO logado

1. Router tenta renderizar `/dashboard`
2. Entra em `ProtectedRoute`
3. `isAuthenticated === false`
4. `<Navigate />` redireciona para `/login`
5. `Dashboard` **nem é montado**

### Usuário logado

1. Router passa pelo `ProtectedRoute`
2. `<Outlet />` renderiza `Dashboard`
3. Navegação segue normal

---

## 7. Por que isso funciona tão bem com nested routes

* proteção é **herdada**
* você protege o layout pai
* todas as rotas filhas ficam automaticamente protegidas

Isso escala muito bem.

---

> **Router decide o caminho
> Auth decide se pode renderizar**

---

## Próximos passos naturais

* proteger rota com base em **roles** (admin, user)
* salvar rota de origem e voltar após login
* esconder links de navegação
* diferença entre proteger UI e proteger API (Java backend)

Diz qual você quer e seguimos direto pro código.
