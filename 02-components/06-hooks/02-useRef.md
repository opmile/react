# Criando Modais

---

## 1. O que é a tag `<dialog>` no HTML

`<dialog>` é uma **tag HTML nativa** feita exatamente para representar **modais e caixas de diálogo**.

Ela já resolve coisas que antes exigiam muita gambiarra:

* sobreposição de conteúdo
* foco preso dentro do modal
* comportamento semântico correto
* métodos nativos para abrir e fechar

Exemplo puro em HTML:

```html
<dialog>
  <p>Olá, eu sou um modal</p>
</dialog>
```

Mas só isso não faz nada ainda. O diferencial do `<dialog>` são **seus métodos nativos**:

* `show()` → abre como janela flutuante
* `showModal()` → abre como modal (bloqueia o fundo)
* `close()` → fecha

Esses métodos **não são declarativos**, eles são **imperativos**.
E é exatamente aí que entra o React + `useRef`.

---

## 2. Por que usar `<dialog>` com React

React é declarativo.
`<dialog>` funciona de forma imperativa.

Isso cria um pequeno conflito conceitual:

* Em React, você pensa: “se o estado for true, renderiza”
* No `<dialog>`, você pensa: “chama um método no elemento”

Resultado: **não faz sentido controlar `<dialog>` só com estado**.

O caminho correto é:

* React controla **quando** abrir ou fechar
* `<dialog>` executa **como** abrir ou fechar

E a ponte entre esses dois mundos é o `useRef`.

---

## 3. O papel do `useRef` (ponto central)

### O que é `useRef`, de verdade

`useRef` cria uma **referência mutável** para algo que:

* persiste entre renders
* não causa re-render quando muda
* geralmente aponta para um **elemento real do DOM**

Exemplo simples:

```js
const dialogRef = useRef(null);
```

Depois disso, React vai colocar o **elemento HTML real** dentro de:

```js
dialogRef.current
```

Ou seja:

* `dialogRef.current` === `<dialog>...</dialog>` no DOM

Isso permite chamar métodos nativos diretamente:

```js
dialogRef.current.showModal();
dialogRef.current.close();
```

Sem `useRef`, você **não consegue** chamar esses métodos de forma limpa.

---

## 4. Criando um componente `Dialog` em React

Agora vamos ao componente base, bem feito.

### Estrutura mínima do componente

```jsx
import { useRef } from "react";

export function Dialog({ title, children }) {
  const dialogRef = useRef(null);

  function openDialog() {
    dialogRef.current.showModal();
  }

  function closeDialog() {
    dialogRef.current.close();
  }

  return (
    <>
      <button onClick={openDialog}>
        Abrir modal
      </button>

      <dialog ref={dialogRef}>
        <h2>{title}</h2>

        <div>
          {children}
        </div>

        <button onClick={closeDialog}>
          Fechar
        </button>
      </dialog>
    </>
  );
}
```

Aqui já tem várias ideias importantes acontecendo.

---

## 5. O que está acontecendo nesse componente

### 1️⃣ `ref={dialogRef}`

Isso conecta:

* o `<dialog>` do JSX
* com o objeto criado pelo `useRef`

Quando o componente monta:

```js
dialogRef.current → <dialog> real do DOM
```

---

### 2️⃣ Abertura do modal

```js
dialogRef.current.showModal();
```

Isso:

* abre o modal
* bloqueia o fundo
* prende o foco automaticamente (acessibilidade)

Nada de `display: none`, nada de CSS hack.

---

### 3️⃣ Fechamento do modal

```js
dialogRef.current.close();
```

Fecha corretamente, devolve o foco e limpa o estado interno do `<dialog>`.

---

### 4️⃣ `children`

Isso transforma o `Dialog` em um **componente reutilizável**.

Você pode colocar:

* formulários
* textos
* confirmações
* qualquer coisa dentro do modal

---

## 6. Usando o `Dialog` em outro componente

Exemplo realista: modal com formulário.

```jsx
import { Dialog } from "./Dialog";

export function Page() {
  return (
    <Dialog title="Criar conta">
      <form>
        <input type="email" placeholder="Email" />
        <input type="password" placeholder="Senha" />

        <button type="submit">
          Cadastrar
        </button>
      </form>
    </Dialog>
  );
}
```

Repara:

* o formulário **vive dentro do modal**
* o `Dialog` não precisa saber o que tem dentro
* responsabilidade bem separada

---

## 7. Por que `useRef` é melhor que `useState` aqui

Muita gente tenta fazer isso:

```js
const [open, setOpen] = useState(false);
```

E depois controlar o modal via renderização condicional.

Com `<dialog>`, isso é **conceitualmente errado**, porque:

* o elemento já existe
* o controle real é via métodos
* esconder/remover o elemento quebra foco e acessibilidade

Resumo direto:

* `useState` → controla **renderização**
* `useRef` → controla **comportamento imperativo**

Para `<dialog>`, `useRef` é a escolha certa.

---

## 8. Boas práticas importantes

### Use `showModal()`, não `show()`

* `show()` não bloqueia o fundo
* `showModal()` é o comportamento esperado de um modal

---

### Sempre tenha uma forma clara de fechar

* botão “Fechar”
* botão “Cancelar”
* ou ação explícita do usuário