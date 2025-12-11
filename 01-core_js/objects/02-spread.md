# Imutabilidade e Spread Operator

---

Em React, **estado** é o conjunto de **dados mutáveis que representam a situação atual de um componente ao longo do tempo**, isto é, aquilo que pode mudar em resposta a interações do usuário, respostas assíncronas ou efeitos colaterais e que, quando muda, **dispara uma nova renderização**. Diferente do estado em Java (que costuma ser apenas atributos internos de um objeto), o estado em React é um **input do processo de render**, tratado como imutável e gerenciado explicitamente (via `setState`, `useState`, `useReducer`), servindo como fonte de verdade que o React usa para decidir quando e como a UI deve ser recalculada.

---

## 1. Por que imutabilidade é crucial em React (o motivo real)

React **não observa valores**, ele observa **referências**.

Quando o estado muda, React faz algo conceitualmente assim:

```js
if (estadoAnterior !== novoEstado) {
  render();
}
```

Não importa se o conteúdo “parece diferente”.
Se a **referência for a mesma**, React pode ignorar a mudança.

### Exemplo do erro clássico

```js
const [user, setUser] = useState({ name: "Ana" });

user.name = "Maria";
setUser(user); // mesma referência
```

Resultado:

* você alterou o objeto
* mas **não criou um novo**
* React pode não renderizar

Esse é o bug fantasma mais comum em React.

---

## 2. Mutação vs imutabilidade (bem direto)

### Mutação (errado em React)

```js
user.name = "Maria";
```

☠️ mesma referência
☠️ efeitos colaterais
☠️ render imprevisível

### Forma correta (imutável)

```js
const updatedUser = {
  ...user,
  name: "Maria"
};
```

✅ nova referência
✅ previsível
✅ React percebe a mudança

---

## 3. O que exatamente o spread (`...`) faz

O spread **não copia profundamente**.
Ele faz uma **shallow copy**:

```js
const copy = { ...obj };
```

Isso significa:

* copia as propriedades do primeiro nível
* **referências internas são mantidas**

Visualmente:

```
obj ──┬─ primitive
      └─ nestedObject ──► mesma referência
```

Isso é o ponto que mais quebra a cabeça.

---

## 4. Atualizando objetos aninhados — passo a passo

Considere este estado:

```js
const [state, setState] = useState({
  user: {
    name: "Ana",
    address: {
      city: "SP",
      zip: "12345"
    }
  },
  loggedIn: true
});
```

### Atualizar apenas `name`

```js
setState({
  ...state,
  user: {
    ...state.user,
    name: "Maria"
  }
});
```

O que acontece:

* novo `state`
* novo `user`
* `address` mantém referência (ok, não mudou)

---

### Atualizar `city` (mais profundo)

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

Aqui é essencial copiar **cada nível acima do valor alterado**.

Regra de ouro:

> para mudar algo, crie uma nova referência em **todos os níveis do caminho**

### Atualizando mais de uma propriedade aninhada ao mesmo tempo

```js
setState({
  ...state,
  user: {
    ...state.user,
    name: "Maria",
    address: {
      ...state.user.address,
      city: "RJ",
      zip: "54321"
    }
  }
});
```

---

## 5. O erro mais comum com objetos aninhados

```js
const newState = { ...state };
newState.user.address.city = "RJ";
setState(newState);
```

Por que isso é errado?

* `user` e `address` continuam com a **mesma referência**
* você acabou de mutar o estado antigo
* React perde previsibilidade

Isso quebra:

* memoização
* `useEffect` com deps
* rendering otimizado

---

## 6. Arrays + objetos (combo frequente)

Estado:

```js
const [users, setUsers] = useState([
  { id: 1, name: "Ana" },
  { id: 2, name: "João" }
]);
```

Atualizar um usuário:

```js
setUsers(users.map(u =>
  u.id === 1
    ? { ...u, name: "Maria" }
    : u
));
```

Aqui:

* novo array
* novo objeto só para quem mudou
* máximo reaproveitamento de referência

React ama isso.

---

## 7. Por que React não copia sozinho?

Pergunta justa.

Se React copiasse tudo:

* performance ia pro chão
* não daria pra escalar
* custo imprevisível

Então React **confia no dev** para manter a imutabilidade.
Em troca, entrega:

* render rápido
* heurísticas simples
* modelo mental consistente

---

## 8. Comparação direta com Java

Em Java:

* mutação é normal
* encapsulamento controla efeitos

```java
user.setName("Maria");
```

Em React:

* mutação quebra o contrato
* mudança é substituição

```js
setUser({ ...user, name: "Maria" });
```

Você não está sendo “menos OO”.
Está jogando outro jogo.

---

## 9. Quando isso fica pesado: Immer

Quando o estado cresce, surge:

```js
import produce from "immer";

setState(produce(draft => {
  draft.user.address.city = "RJ";
}));
```

Internamente:

* Immer gera cópias imutáveis
* você escreve código mutável
* React recebe o que precisa

---
