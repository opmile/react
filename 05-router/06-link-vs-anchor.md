# `<Link/>` vs `<a/>`

---

## `<a />` (âncora HTML)

A tag `<a>` é **do navegador**, não do React.

```html
<a href="/login">Login</a>
```

O que ela faz:

* altera a URL
* dispara uma **requisição HTTP**
* **recarrega a página inteira**
* reinicia o JavaScript
* perde todo o estado da SPA

Ela delega tudo ao navegador e ao servidor.

Funciona perfeitamente em sites tradicionais.
É **hostil** a SPAs.

---

## `<Link />` (React Router)

`<Link />` é um **componente React**.

```jsx
<Link to="/login">Login</Link>
```

O que ele faz:

* intercepta o clique
* **impede o reload**
* altera a URL via History API
* notifica o React Router
* renderiza a rota correta

Tudo isso mantendo:

* estado em memória
* aplicação viva
* histórico funcional

Visualmente parece um `<a>`, mas o comportamento é outro.

---

## Diferença conceitual (a que importa)

* `<a>` diz:
  **“Navegador, vá para outro documento.”**

* `<Link>` diz:
  **“Router, mude o estado de navegação.”**

Essa diferença define se você está em uma SPA de verdade ou não.

Lembrando que SPAs são sempre mais rápidas que MPAs (sites tradicionais), e um dos motivos se justifica pelo uso de `<Link/>`, justamente porque o navegador não precisa regarregar a cada nova renderização de página disparada por esse componente.

---

## Atributos principais

### `<a />`

* `href` → endereço absoluto ou relativo
* pode apontar pra fora do site
* comportamento fixo, padrão do browser

### `<Link />`

* `to` → caminho interno da aplicação
* só funciona **dentro de um Router**
* integra com histórico e rotas
* aceita objetos (path + query + state)

Exemplo comum:

```jsx
<Link to="/dashboard">Dashboard</Link>
```

---

## Quando usar cada um (regra prática)

Use `<Link />` quando:

* a navegação é **interna**
* a **rota pertence à SPA**
* você quer manter estado

Use `<a />` quando:

* o link é externo
* você quer sair da SPA
* **vai para outro domínio**
* ou quer forçar reload de propósito

---

## Erro clássico de iniciante

Misturar os dois sem critério.

Resultado:

* algumas navegações fluem
* outras resetam tudo
* bugs “fantasmas” de estado

---

> `<a>` troca de página.
> `<Link>` troca de estado.