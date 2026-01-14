# Introdução ao React Router

---

## 1. O problema real que o React Router resolve

Em um site tradicional, quando você acessa:

* `/`
* `/login`
* `/profile`

O navegador faz **requisições diferentes ao servidor**, que responde com **páginas diferentes**.

Agora entra o React.

Em aplicações React modernas (SPA — *Single Page Application*):

* Existe **uma única página HTML**
* O React decide **o que aparece na tela** de maneira dinâmica
* O navegador **não recarrega a página** a cada navegação mas renderiza um componente específico

> Como mudar a “página” sem recarregar a página?

É exatamente aqui que o React Router entra.
Ele não é um detalhe de implementação — ele é **a cola entre URL e interface**.

---

## 2. O que o React Router é, em uma frase honesta

React Router é uma biblioteca que permite **associar partes da interface a URLs**, controlando qual componente React aparece de acordo com o caminho atual do navegador.

Não é:

* um gerenciador de estado
* um framework
* um substituto do backend

É **controle de navegação no front-end**.

---

## 3. A ideia central: URL como estado da aplicação

Esse ponto é crucial e muita gente ignora.

Quando você usa React Router, a URL deixa de ser só um endereço e vira:

> uma **fonte de estado**

Exemplo mental:

* URL = `/tasks` → renderiza `<Tasks />`
* URL = `/tasks/3` → renderiza `<TaskDetails />`
* URL = `/login` → renderiza `<Login />`

Você não está dizendo:

> “Renderize esse componente”

Você está dizendo:

> “Se a URL for X, a UI é Y”

Isso traz consequências importantes:

* Você pode **compartilhar links**
* Pode usar **botão de voltar do navegador**
* Pode **recarregar a página sem perder o contexto**

---

## 4. Navegação não é renderização condicional

Um erro comum de iniciante é pensar:

```jsx
if (page === "home") return <Home />
if (page === "login") return <Login />
```

React Router **não é isso**.

A diferença fundamental:

* Ele **sincroniza com o navegador**
* Ele lida com **histórico**
* Ele entende **URLs reais**

Ou seja: ele não só escolhe componentes, ele **coordena a experiência de navegação**.

**Histórico do navegador** é o mecanismo que registra cada mudança de URL durante a navegação (voltar, avançar, recarregar).

Isso importa porque, em uma SPA não existe reload de página, mas o usuário **espera** que o navegador funcione normalmente.

React Router conversa com o histórico para:

* mudar a URL sem recarregar
* permitir botão “voltar” e “avançar”
* manter navegação previsível
* permitir links compartilháveis

> SPAs só funcionam de verdade quando respeitam o histórico do navegador.

---

## 5. Pensar em rotas é pensar em arquitetura

Uma coisa que separa código amador de código profissional:

Quem usa React Router pensa:

* “Quais são as páginas do meu sistema?”
* “Quais URLs fazem sentido para esse domínio?”
* “O que é público? O que é privado?”

Quem não pensa nisso acaba com:

* rotas improvisadas
* lógica de navegação espalhada
* componentes acoplados

React Router força você a **dar nome às telas do sistema**.

Isso é arquitetura, não sintaxe.

---