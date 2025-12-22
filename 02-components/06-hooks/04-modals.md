# Modais: a junção de uso de hooks

---

## 1. Componente Modal (com os 3 hooks)

```jsx
import { useState, useEffect, useRef } from "react";

export default function Modal({ title, children }) {
  const [isOpen, setIsOpen] = useState(false);
  const dialogRef = useRef(null);

  // EFEITO: sincroniza o estado React com o dialog do DOM
  useEffect(() => {
    const dialog = dialogRef.current;
    if (!dialog) return; // **

    if (isOpen) {
      dialog.showModal();
    } else {
      dialog.close();
    }
  }, [isOpen]);

  function openModal() {
    setIsOpen(true);
  }

  function closeModal() {
    setIsOpen(false);
  }

  return (
    <>
      <button onClick={openModal}>Abrir modal</button>

      <dialog
        ref={dialogRef}
        className="rounded-xl p-6 w-[400px]"
        onClose={() => setIsOpen(false)}
      >
        <h2 className="text-xl font-bold mb-4">{title}</h2>

        <div className="mb-6">
          {children}
        </div>

        <button onClick={closeModal}>Fechar</button>
      </dialog>
    </>
  );
}
```

* `if (!dialog) return;`: Essa linha garante que o efeito não tente acessar ou manipular o `<dialog>` antes dele existir no DOM, evitando erro ao chamar métodos em null.

---

## 2. Uso do Modal no App

```jsx
import Modal from "./Modal";

export default function App() {
  return (
    <div className="h-screen flex items-center justify-center">
      <Modal title="Cadastro">
        <form className="flex flex-col gap-3">
          <input type="email" placeholder="Email" />
          <input type="password" placeholder="Senha" />
          <button type="submit">Cadastrar</button>
        </form>
      </Modal>
    </div>
  );
}
```

---

## 3. Agora a explicação **de verdade**

### 1️⃣ useState → a verdade da interface

```js
const [isOpen, setIsOpen] = useState(false);
```

Esse estado responde a uma única pergunta:

> O modal está aberto ou fechado **do ponto de vista da UI**?

* Ele **não abre o modal diretamente**
* Ele representa a **intenção da interface**
* Ele dispara render quando muda

Sem `useState`, você teria um modal que abre “por fora”, sem o React saber.

---

### 2️⃣ useRef → acesso imperativo ao DOM

```js
const dialogRef = useRef(null);
```

Aqui está o ponto crucial:

* `<dialog>` **não é declarativo**
* Ele exige chamadas imperativas:

  * `showModal()`
  * `close()`

React **não controla isso automaticamente**.

Então:

* `useRef` guarda a referência ao elemento real do DOM
* `.current` aponta para o `<dialog>`

Importante:

* mudar `.current` **não causa render**
* perfeito para APIs externas

---

### 3️⃣ useEffect → a cola entre estado e mundo real

```js
useEffect(() => {
  const dialog = dialogRef.current;
  if (!dialog) return;

  if (isOpen) {
    dialog.showModal();
  } else {
    dialog.close();
  }
}, [isOpen]);
```

Esse efeito existe porque:

> O React mudou o estado, mas o DOM não sabe disso sozinho.

O `useEffect`:

* observa `isOpen`
* reage quando ele muda
* sincroniza o estado React com o `<dialog>`

Sem esse efeito:

* o estado mudaria
* mas o modal não abriria nem fecharia

---

## 4. Por que não abrir direto no onClick?

Isso aqui seria um erro conceitual:

```js
dialogRef.current.showModal();
```

Por quê?

* React perde o controle
* estado e UI ficam dessincronizados
* você cria bugs difíceis de rastrear
* não dá para reagir a mudanças externas

O fluxo correto é sempre:

```
evento → setState → render → effect → mundo externo
```

---

## 5. O papel de cada hook (bem direto)

| Hook      | Função real                 |
| --------- | --------------------------- |
| useState  | Fonte da verdade da UI      |
| useRef    | Ponte para APIs imperativas |
| useEffect | Sincronização reativa       |

Eles **não competem**, eles **cooperam**.

---

## 6. Por que esse padrão é forte

Esse modal:

* é previsível
* não depende de hacks
* respeita o modelo mental do React
* escala para casos complexos
* permite animações, ESC, backdrop, etc

É o mesmo padrão usado em:

* modais
* players de vídeo
* gráficos
* WebSockets
* integrações com libs externas

---

* sem `useState` → React não entende o estado
* sem `useEffect` → o DOM não acompanha
* sem `useRef` → você não controla o `<dialog>`

---

# Motivações de uso `useState` e `useEffect` para modal

---

## 1. “Funcionar” não é o mesmo que “estar certo”

Você conseguiu abrir e fechar o modal sem `useState` e `useEffect` porque:

* o browser aceita chamadas imperativas (`showModal`, `style.display = "block"`, etc.)
* React **não impede** você de manipular o DOM
* para um caso simples, isso parece suficiente

Isso é igual dirigir sem cinto numa rua vazia:
chega ao destino, mas não prova que é uma boa prática.

---

## 2. O problema não aparece no começo — aparece depois

O que você fez foi algo assim (conceitualmente):

```
evento → mexe direto no DOM → fim
```

Funciona **até o momento em que**:

* o componente re-renderiza
* o estado visual precisa ser conhecido por outro componente
* algo externo tenta fechar o modal
* você adiciona animação
* você adiciona ESC, backdrop, foco
* o React decide reaproveitar ou desmontar o componente

E aí surgem bugs que parecem “aleatórios”.

---

## 3. O ponto central: React não é um wrapper de DOM

React tem uma premissa muito clara:

> **A UI é uma função do estado.**

Quando você abre um modal sem `useState`, você está dizendo:

> “A UI mudou, mas o React não precisa saber.”

Isso quebra o contrato do framework.

Não agora.
Depois.

---

## 4. O que `useState` te dá que você não tinha

Sem `useState`:

* React não sabe se o modal está aberto
* você não consegue responder a esse estado
* não consegue sincronizar com outras partes da UI
* não consegue testar facilmente
* não consegue prever re-renderizações

Com `useState`:

```
isOpen === true → modal aberto
isOpen === false → modal fechado
```

Isso é **verdade única**, não efeito colateral.

---

## 5. E o `useEffect` não é enfeite

Quando você usa algo como `<dialog>`, você está lidando com uma **API imperativa externa ao React**.

O `useEffect` existe para isso:

> “Quando o estado mudar, execute um efeito no mundo real.”

Sem ele, você mistura:

* lógica de estado
* manipulação de DOM
* eventos
  tudo no mesmo lugar

Isso não quebra hoje, mas **quebra a previsibilidade**.

---

## 6. O argumento mais forte (e mais ignorado)

Você não usa `useState` e `useEffect` **para abrir modal**.
Você usa porque:

> **Eles tornam o comportamento explicável.**

Código correto é código que:

* outra pessoa entende
* você entende daqui 3 meses
* não depende de ordem mágica
* não quebra quando cresce

O seu modal funcionou porque era pequeno.
Hooks existem porque sistemas **não permanecem pequenos**.

---