# Boas Práticas de Estruturação de Projeto

A organização de pastas no React usa o padrão de **componentes por pasta com `index.jsx`**. Esse padrão aparece muito em projetos profissionais porque deixa tudo limpo, escalável e fácil de importar.

---

## POR QUE ESSE PADRÃO EXISTE?

A ideia é simples: **cada componente ganha sua própria pasta**, e essa pasta guarda tudo que pertence exclusivamente a ele.

Isso evita aquela bagunça de “um monte de arquivos soltos” e facilita localizar, mover ou excluir componentes sem quebrar nada.

---

## COMO FUNCIONA A ESTRUTURA

O formato clássico é:

```
src/
  components/
    Button/
      index.jsx
      styles.css
      helpers.js
    Card/
      index.jsx
      card.module.css
      skeleton.jsx
```

Ou seja:

* O nome da pasta é o nome do componente.
* Dentro dela, o componente principal é o **index.jsx**.
* O index exporta o componente (às vezes é o próprio componente; às vezes importa de outro arquivo interno).
* Arquivos auxiliares ficam na mesma pasta.

---

## BENEFÍCIO DO `index.jsx`

Ao usar um `index.jsx`, você consegue importar *limpo*, assim:

```jsx
import Button from "@/components/Button";
```

Sem precisar:

```jsx
import Button from "@/components/Button/Button.jsx";
```

Isso faz o código ficar mais legível, mais curto e mais estável em refactors.

---

## O CONCEITO POR TRÁS

**A pasta vira a unidade lógica, não o arquivo.**

* O componente pode crescer à vontade (subcomponentes, hooks privados, estilos dedicados, testes).
* Mas, para quem importa esse componente, ele continua sendo **um único bloco organizado**.

---

## VARIAÇÕES COMUNS

### 1. **index.jsx contendo o componente direto**

Mais comum em projetos pequenos/médios:

```
Button/
  index.jsx   -> define e exporta o componente
  styles.css
```

### 2. **index.jsx apenas reexportando**

Bom quando o componente é grande:

```
Button/
  Button.jsx
  index.jsx   -> export { default } from './Button'
  hooks.js
  styles.css
```

---

# PADRÕES QUE COSTUMAM VIR JUNTO

### `/components` dividido por tipo

```
components/
  ui/
  layout/
  data/
```

Ou por domínio:

```
features/
  auth/
  dashboard/
  posts/
```

Depende do tamanho do projeto, mas a lógica do `index.jsx` por pasta continua igual.

---

**Criar uma pasta por componente e exportá-lo via `index.jsx` deixa o projeto modular, limpo e com imports simples — padrão profissional para React moderno.**