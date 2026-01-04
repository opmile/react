# `@layer` 

---

## 1. O problema que o `@layer` resolve

Tailwind gera **milhares de classes utilitárias** automaticamente.
Sem um sistema de camadas, o CSS viraria:

* imprevisível
* cheio de conflitos
* dependente de ordem de importação

O `@layer` existe para **controlar prioridade e previsibilidade no CSS final**.

Em resumo:

> `@layer` define **quem vence quando duas regras competem**.

---

## 2. As três camadas do Tailwind (ordem importa)

Tailwind define **exatamente três camadas**, nesta ordem fixa:

```
base → components → utilities
```

E isso **nunca muda**.

Regra de ouro:

> quanto mais à direita, **maior a prioridade**.

Então:

* utilities sempre vencem components
* components sempre vencem base

---

## 3. `@layer base` — o alicerce do CSS

### O que é

`base` é onde ficam:

* resets
* normalizações
* estilos globais
* comportamento padrão de elementos HTML

Exemplo mental:

> “Como um `<button>` ou `<h1>` deve se comportar por padrão?”

### Exemplo real

```css
@layer base {
  body {
    @apply bg-zinc-900 text-zinc-100;
  }

  h1 {
    @apply text-3xl font-bold;
  }
}
```

Características:

* aplica em **tags HTML**
* não usa classes
* raramente muda
* é a fundação do design system

Você **não disputa** com utilitários aqui.

---

## 4. `@layer components` — padrões reutilizáveis

### O que é

`components` é onde você define:

* abstrações visuais
* padrões reutilizáveis
* componentes “estilísticos”

Eles ainda são classes CSS, mas:

* mais semânticas
* menos granulares

Exemplo mental:

> “Esse botão sempre segue esse visual.”

### Exemplo

```css
@layer components {
  .btn {
    @apply px-4 py-2 rounded-lg font-medium;
  }

  .btn-primary {
    @apply btn bg-blue-600 text-white hover:bg-blue-500;
  }
}
```

Características:

* criam **vocabulário de design**
* facilitam consistência
* ainda podem ser sobrescritas por utilities

---

## 5. `@layer utilities` — poder máximo

### O que é

`utilities` são:

* classes atômicas
* responsabilidade única
* prioridade máxima

Exemplo mental:

> “Eu quero exatamente isso aqui, agora.”

Exemplo:

```css
@layer utilities {
  .text-shadow {
    text-shadow: 0 2px 10px rgba(0,0,0,0.5);
  }
}
```

Características:

* vencem tudo
* sobrescrevem components e base
* devem ser usadas com parcimônia quando customizadas

---

# `@layer` no Tailwind CSS moderno (v4+)

## Spoiler importante

> **`@layer` continua existindo e funcionando igual**
> O que mudou foi: **você precisa usar menos CSS customizado do que antes**

Tailwind v4 empurra forte para:

* utilities no JSX
* tokens via CSS variables
* menos abstrações prematuras

Mas quando você precisa de CSS… `@layer` ainda é a ferramenta certa.

---

## As camadas continuam sendo exatamente estas

A ordem **não mudou**:

```
base → components → utilities
```

E isso é **regra estrutural do Tailwind**, não convenção.

Quanto mais à direita:

* maior prioridade
* mais poder de sobrescrita

---

## Onde o Tailwind moderno entra no jogo

No Tailwind v4, o arquivo CSS costuma ser **mínimo**, algo assim:

```css
@import "tailwindcss";
```

Isso já injeta:

* base
* components
* utilities

Se você quiser intervir, você **declara `@layer` explicitamente**.

---

## `@layer base` — ainda existe, mas é mais focado

### O papel atual do `base`

Hoje, `base` é usado principalmente para:

* tokens globais
* CSS variables
* ajustes estruturais

Exemplo moderno:

```css
@layer base {
  :root {
    --radius: 0.75rem;
  }

  body {
    font-family: system-ui, sans-serif;
  }
}
```

O mindset mudou:

* menos “reset”
* mais **fundação semântica**

Você não cria componentes aqui.
Você define **o chão da aplicação**.

---

## `@layer components` — use com parcimônia (isso mudou)

Essa é a camada que **mais mudou em filosofia**.

Antes:

* comum criar `.btn`, `.card`, `.badge`

Agora:

* Tailwind recomenda **criar componentes só quando houver repetição real**
* ou quando fizer sentido semântico forte

Exemplo ainda válido:

```css
@layer components {
  .btn {
    @apply inline-flex items-center gap-2 rounded-lg px-4 py-2 font-medium;
  }

  .btn-primary {
    @apply btn bg-blue-600 text-white hover:bg-blue-500;
  }
}
```

Mas a recomendação moderna é:

* prefira **composição de utilities no JSX**
* use `components` para **design system**, não conveniência

---

## `@layer utilities` — continua sendo a camada mais poderosa

Essa camada praticamente **não mudou**.

Ela serve para:

* utilities customizadas
* extensões pontuais
* comportamentos atômicos que não existem no core

Exemplo moderno e comum:

```css
@layer utilities {
  .content-auto {
    content-visibility: auto;
  }
}
```

Importante:

* utilities **sempre vencem**
* inclusive classes de `components`
* sem depender da ordem no HTML

---

## Regra moderna de prioridade (continua valendo)

Se você tiver:

```html
<div class="card bg-black"></div>
```

E:

* `.card` foi criado em `@layer components`
* `bg-black` é utility

Resultado:

> **fundo preto**, sempre

Isso **não mudou** no Tailwind v4.

---

## O que o Tailwind v4 mudou no uso do `@layer`

### 1. Menos CSS, mais JSX

Hoje, o fluxo recomendado é:

* primeiro: utilities inline
* depois: extrair padrões reais
* só então: usar `@layer components`

Se você começa criando classes CSS, você está usando Tailwind **como Bootstrap** — erro conceitual.

---

### 2. Tokens via CSS variables + utilities

O Tailwind moderno espera algo assim:

```css
@layer base {
  :root {
    --color-bg: oklch(0.15 0 0);
    --color-text: oklch(0.95 0 0);
  }
}
```

E depois:

```html
<div class="bg-[var(--color-bg)] text-[var(--color-text)]">
```

Isso reduz drasticamente a necessidade de `components`.

---

### 3. `@apply` continua existindo, mas não é o centro

No v4:

* `@apply` é aceitável
* mas não deve ser o padrão automático

Use quando:

* há repetição real
* há ganho de legibilidade
* há semântica clara

---

## Quando você REALMENTE precisa de `@layer` hoje

Use `@layer` quando:

* precisa definir tokens globais → `base`
* está criando um design system → `components`
* quer estender Tailwind com algo atômico → `utilities`

Não use quando:

* dá pra resolver com utilities inline
* é apenas conveniência
* é abstração prematura

---

* **base** → variáveis, estrutura, fundamentos
* **components** → semântica e padrões reais
* **utilities** → poder local absoluto

E acima de tudo:

> **Utilities no JSX são o default.
> `@layer` é a exceção consciente.**

---