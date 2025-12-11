# Objetos

---

## 1. O que é um objeto em JavaScript (sem enrolação)

Em JavaScript, objeto é:

> um **dicionário de pares chave–valor**

```js
const user = {
  id: 1,
  name: "Milena",
  active: true
};
```

* chaves são strings (mesmo sem aspas)
* valores podem ser **qualquer coisa**:

  * primitivos
  * funções
  * arrays
  * outros objetos

### Diferença direta para Java

* Java: objeto = instância de uma classe
* JS: objeto **pode existir sem classe alguma**

Esse detalhe muda tudo no React.

---

## 2. Acessando propriedades

```js
user.name
user["name"]
```

* dot notation: mais comum
* bracket: útil quando a chave é dinâmica

```js
const key = "name";
user[key];
```

Em React isso aparece direto com formulários e dados vindos de API.

---

## 3. Objetos são mutáveis (e isso é perigoso no React)

```js
const user = { name: "Ana" };
user.name = "Maria";
```

Funciona.
Mas em React isso é um **problema**.

### Por quê?

React compara **referência**, não valor.

```js
setUser(user); // mesma referência → React pode ignorar
```

### Regra mental

> Nunca mutar objetos que representam estado

Isso puxa naturalmente o próximo conceito.

---

## 4. Spread operator (`...`) — ferramenta essencial

```js
const updatedUser = {
  ...user,
  name: "Maria"
};
```

* cria novo objeto
* mantém imutabilidade
* React detecta a mudança

### Comparação com Java

Java geralmente:

* cria novo objeto
* ou usa setters (mutação controlada)

React **não quer setter**, quer **novo objeto**.

---

## 5. Objetos aninhados (ponto crítico)

```js
const state = {
  user: {
    name: "Ana",
    address: {
      city: "SP"
    }
  }
};
```

Atualizar corretamente:

```js
setState({
  ...state,
  user: {
    ...state.user,
    address: {
      ...state.user.address,
      city: "RJ"
    }
  }
});
```

Isso parece verboso — e **é mesmo**.
Por isso surgem bibliotecas como `Immer`.

Em Java, isso seria:

* setters encadeados
* mutação interna controlada

JS NÃO faz cópia profunda sozinho.

---

## 6. Métodos em objetos (formas modernas)

### Forma antiga

```js
const user = {
  name: "Ana",
  greet: function () {
    return `Oi, ${this.name}`;
  }
};
```

### Forma moderna

```js
const user = {
  name: "Ana",
  greet() {
    return `Oi, ${this.name}`;
  }
};
```

Funcionalmente idênticas.

---

## 7. `this` — diferença BRUTAL para Java

Em Java:

* `this` é sempre o objeto

Em JS:

* `this` depende de **como a função é chamada**

```js
const user = {
  name: "Ana",
  greet() {
    console.log(this.name);
  }
};

const greet = user.greet;
greet(); // undefined
```

Isso assusta Java dev — com razão.

### Em React moderno

Quase não se usa `this` porque:

* componentes são functions
* hooks substituem classes

🎉 Um problema a menos.

---

## 8. Desestruturação (React por todo lado)

```js
const { name, active } = user;
```

Ou em parâmetros:

```js
function UserCard({ name, active }) {
  ...
}
```

Isso é onipresente em React.

### Java não tem equivalente direto

O mais próximo:

* records
* pattern matching (ainda distante na prática)

---

## 9. Shorthand properties

```js
const name = "Ana";
const user = { name };
```

Muito comum em reducers e handlers React.

---

## 10. Optional chaining (`?.`) — salvador de nulls

```js
user.address?.city
```

Evita:

```js
user && user.address && user.address.city
```

Em React + dados de API isso é ABSOLUTAMENTE essencial.

Java só foi resolver isso recentemente com `Optional` — e ainda assim de forma mais pesada.

---

## 11. Nullish coalescing (`??`)

```js
const city = user.city ?? "Não informado";
```

Difere de `||`:

* só dispara se for `null` ou `undefined`
* não em `""`, `0`, `false`

Muito útil em props e render condicional.

---

## 12. Objetos vs Classes em React

Você **pode** usar classes em JS:

```js
class User {
  constructor(name) {
    this.name = name;
  }
}
```

Mas em React moderno:

* quase ninguém usa class-based components
* objetos literais + functions dominam

### Diferença filosófica

* Java = comportamento + dados
* React = dados fluindo + funções transformando

---

## 13. Imutabilidade: o ponto que mais importa

Resumo brutal:

* JS permite mutar
* React **depende** de não mutar
* você precisa se policiar

Se você respeita isso:

* render fica previsível
* bugs somem
* performance melhora

---
