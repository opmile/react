# Desestruturação

---

## 1. O que é desestruturação (JavaScript, não React)

Desestruturação é apenas uma **forma de extrair valores** de uma estrutura (array ou objeto) e colocar em variáveis, de maneira declarativa.

### Exemplo sem desestruturação

```js
const user = { name: "Ana", age: 20 };

const name = user.name;
const age = user.age;
```

### Com desestruturação de objeto

```js
const { name, age } = user;
```

Nada mágico aconteceu.
Isso é só açúcar sintático.

### Regra mental

> o nome da variável precisa bater com a chave do objeto

---

## 2. Desestruturação de array (parece estranha, mas é simples)

Arrays **não têm nome de propriedade**, só **posição**.

```js
const numbers = [10, 20];
```

Sem desestruturação:

```js
const first = numbers[0];
const second = numbers[1];
```

Com desestruturação:

```js
const [first, second] = numbers;
```

Aqui:

* `first` → posição 0
* `second` → posição 1

Os nomes **quem escolhe é você**.

---

## 3. Então o que é isso aqui?

```js
const [user, setUser] = useState({ name: "Ana" });
```

Vamos desmontar isso **em partes reais**, linha por linha.

---

## 4. Antes de React: imagine essa função

```js
function fakeUseState(initialValue) {
  let value = initialValue;

  function setValue(newValue) {
    value = newValue;
  }

  return [value, setValue];
}
```

Essa função **retorna um array** com duas coisas:

1. um valor
2. uma função

Vamos usar:

```js
const result = fakeUseState(10);
```

`result` é:

```js
[10, setValue]
```

Sem desestruturação:

```js
const value = result[0];
const setValue = result[1];
```

Com desestruturação:

```js
const [value, setValue] = result;
```

Isso é EXATAMENTE o que você está vendo no React.

---

## 5. O que é `useState`

Em React:

```js
const [state, setState] = useState(initialValue);
```

* `useState` é uma **função**
* ela retorna um **array**
* esse array tem **dois elementos**

```js
[
  estadoAtual,
  funçãoParaAtualizarEstado
]
```

Nada além disso.

---

## 6. Ligando tudo ao exemplo real

```js
const [user, setUser] = useState({ name: "Ana" });
```

Significa:

* `user` → estado atual (objeto)
* `setUser` → função para trocar esse estado
* `{ name: "Ana" }` → valor inicial

Sem desestruturação, seria:

```js
const stateArray = useState({ name: "Ana" });
const user = stateArray[0];
const setUser = stateArray[1];
```

A versão curta existe só por legibilidade.

---

## 7. Por que React usa array e não objeto?

Pergunta ótima.

Porque com array:

* ordem é garantida
* nomes são livres (`count`, `setCount`)
* hooks não dependem de nomes

Se fosse objeto, ficaríamos presos a chaves fixas.

---

## 8. Por que `setUser({ ... })` e não `user = ...`?

Porque:

* `user` é só uma variável local
* React não observa atribuições diretas
* React observa **chamadas ao setter**

```js
setUser({ ...user, name: "Maria" });
```

Isso avisa ao React:

> “estado mudou, re-renderiza”

---

## 9. Conectando com imutabilidade

```js
// errado
user.name = "Maria";
setUser(user);

// certo
setUser({
  ...user,
  name: "Maria"
});
```

* nova referência
* React percebe
* render confiável

---
