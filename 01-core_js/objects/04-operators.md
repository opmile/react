# Operadores

---

## 1. Optional chaining (`?.`)

Em JavaScript, acessar algo que **não existe** quebra o programa:

```js
user.address.city
// ❌ TypeError se address for undefined
```

Isso é absurdamente comum em React porque:

* dados vêm de API
* o primeiro render acontece **antes** da resposta chegar
* estados começam vazios

---

### Como era feito antes (feio e frágil)

```js
user &&
user.address &&
user.address.city
```

Ou ainda pior:

```js
if (user && user.address && user.address.city) {
  ...
}
```

Código:

* verboso
* repetitivo
* difícil de manter

---

### O que `?.` faz

Optional chaining diz:

> “Se isso existir, continue.
> Se não existir, **pare e devolva `undefined`**.”

```js
user?.address?.city
```

Comportamento:

* se `user` existir → continua
* se `user` for `null` ou `undefined` → retorna `undefined`
* **não lança erro**

Isso é tudo.

---

### Exemplo com API

```js
const [user, setUser] = useState(null);
```

Primeiro render:

```js
user === null
```

Render seguro:

```jsx
<p>{user?.name}</p>
```

Sem `?.`, esse componente quebraria.

---

### Optional chaining com arrays

```js
users?.[0]?.name
```

Lê como:

* users existe?
* tem índice 0?
* tem name?

Extremamente útil em dados dinâmicos.

---

### Optional chaining com funções

```js
props.onClose?.()
```

Significa:

* se `onClose` existir, chama
* se não existir, ignora

Isso evita:

```js
if (props.onClose) {
  props.onClose();
}
```

---

### O que `?.` **não** faz

* não substitui validação de regras
* não corrige dados mal estruturados
* é apenas **segurança de acesso**

Pense nele como:

> “airbag”, não “freio”.

---

## 2. Nullish coalescing (`??`)

Agora vem a outra metade do combo.

---

### O problema clássico dos valores “falsy”

Em JavaScript:

```js
0
""
false
null
undefined
```

Todos são considerados **falsy** em `||`.

Exemplo:

```js
const value = count || 10;
```

Se `count === 0` → resultado = `10` ❌
Mas 0 pode ser um valor **válido**.

---

### O que `??` resolve

Nullish coalescing diz:

> “Use o valor da direita **apenas se** o da esquerda for
> `null` ou `undefined`.”

```js
const value = count ?? 10;
```

Agora:

* `0` → mantém
* `false` → mantém
* `""` → mantém
* `null` → usa 10
* `undefined` → usa 10

Isso é um **salto de qualidade**.

---

### Exemplo real em React

```js
function Counter({ initialValue }) {
  const value = initialValue ?? 0;
}
```

Aqui:

* se `initialValue` não for passado → começa em 0
* se for `0` → respeita
* com `||`, isso quebraria

---

### Outro exemplo clássico (formulários)

```js
const username = user.name ?? "Anônimo";
```

* nome vazio (`""`) → mantém
* nome inexistente (`undefined`) → fallback

---

## 3. Optional chaining + Nullish coalescing (o combo perfeito)

Esses dois operadores **nasceram para andar juntos**.

```js
const city = user?.address?.city ?? "Não informado";
```

Leitura humana:

* tenta pegar a cidade
* se não existir em nenhum nível
* usa um valor padrão

Isso substitui facilmente 6–7 linhas antigas.

---

## 4. Comparação direta com Java (importante)

### Java clássico

```java
if (user != null && user.getAddress() != null) {
  city = user.getAddress().getCity();
}
```

### Java moderno (`Optional`)

```java
city = Optional.ofNullable(user)
  .map(User::getAddress)
  .map(Address::getCity)
  .orElse("Não informado");
```

### JavaScript moderno

```js
const city = user?.address?.city ?? "Não informado";
```

Mesma ideia.
JS resolveu isso **de forma muito mais leve**.

---

# 5️⃣ O que NÃO confundir

❌ Optional chaining não testa valor lógico
✅ só testa existência (`null` / `undefined`)

❌ Nullish coalescing não testa “falsy”
✅ só testa nulo ou indefinido

Esse detalhe evita bugs sutis.

---

## 6. Quando usar cada um (regra mental)

### Use `?.` quando:

* acessar dados vindo de API
* estado inicial for `null`
* props opcionais
* callbacks opcionais

### Use `??` quando:

* valor pode ser `0`, `false` ou `""`
* você quer fallback **somente** para ausência real

### Use os dois juntos quando:

* o dado é incerto
* mas você precisa garantir um valor final

---

## 7. Anti-pattern clássico (evite)

```js
const name = user && user.name || "Anônimo";
```

Isso mistura:

* short-circuit
* falsy
* fallback

Hoje isso é considerado código ruim.

Prefira sempre:

```js
const name = user?.name ?? "Anônimo";
```

---

* `?.` → acesso seguro
* `??` → fallback inteligente
* juntos → código seguro, limpo e previsível
* React depende fortemente disso por causa do render inicial

---
