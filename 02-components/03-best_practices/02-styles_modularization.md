# Modularização de Estilos CSS

---

Quando se usa **Tailwind**, a história de *“pasta do componente + styles.css”* praticamente some. 

---

## O QUE ACONTECE COM A ESTRUTURA QUANDO VOCÊ USA TAILWIND?

Sem Tailwind, você normalmente teria:

```
Button/
  index.jsx
  styles.css
```

Porque:

* Você quer isolar o estilo do componente.
* Precisa importar o CSS dentro do componente ou em um arquivo global.

Com Tailwind, isso não é mais necessário.
**Você escreve os estilos direto no JSX, via classes utilitárias.**

Ou seja:

```jsx
export default function Button({ children }) {
  return (
    <button className="px-4 py-2 bg-blue-600 text-white rounded-lg">
      {children}
    </button>
  );
}
```

Pronto, acabou o styles.css.
A pasta fica só:

```
Button/
  index.jsx
```

---

## POR QUE ISSO SIMPLIFICA MUITO? 

### 1. **Estilos residem no próprio componente**

Não existe “procurar o CSS que afeta esse elemento”.
Tudo está no mesmo arquivo.

### 2. **Zero conflito de classes**

Como cada classe é utilitária, você não tem:

* `.button { }`
* `.button--outline { }`
* `.button--danger { }`

Você usa direto:

```jsx
className="bg-red-600 hover:bg-red-700"
```

### 3. **Nada de nomear classes ou fazer módulos CSS**

Sem Tailwind, você precisaria de:

* CSS Modules
* Styled Components
* Sass
* BEM
* etc.

Com Tailwind, nada disso.

### 4. **Componentização fica mais leve**

A pasta do componente não precisa mais ser um “mini projeto” com metade sendo CSS.

---

## VALE AINDA USAR PASTA POR COMPONENTE?

Sim, porque a organização de componentes não tem só a ver com estilo.

Ela continua útil para guardar:

* subcomponentes
* hooks privados
* assets específicos
* lógicas auxiliares

A diferença é que com Tailwind geralmente você fica só com:

```
Button/
  index.jsx
```

Ou, às vezes:

```
Button/
  index.jsx
  variants.js    // quando você quer gerar classes dinamicamente
```

Mas o grosso do styling mora **no próprio JSX**.

---

**Com Tailwind, você praticamente não precisa mais de styles.css.
O estilo fica no próprio componente (`index.jsx`), o que simplifica a estrutura e deixa tudo mais modular, rápido e fácil de manter.**