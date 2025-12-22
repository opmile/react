# Regra de Exibição (UI) x Regra de Negócio

---

## 1. Primeiro: o que é o quê (sem confusão)

### Regra de negócio

É tudo que responde a perguntas como:

* quando algo pode acontecer?
* em que condições algo muda?
* qual é a fonte da verdade?
* quem decide?

Exemplos:

* “o modal pode abrir?”
* “o formulário é válido?”
* “o usuário está autenticado?”
* “o submit pode acontecer agora?”

👉 **Estado + decisões**

---

### Regra de exibição (UI)

É tudo que responde a:

* como isso aparece?
* onde fica?
* qual animação?
* qual classe CSS?
* qual HTML?

Exemplos:

* `<dialog>`
* `<button>`
* classes Tailwind
* layout
* alinhamento

👉 **Visual + interação local**

---

## 2. O papel do `App.jsx` (cérebro da aplicação)

O `App` **não é um container qualquer**.
Ele é o **orquestrador**.

O `App` deve:

* possuir os estados importantes
* decidir quando algo acontece
* passar dados e intenções pros filhos

Exemplo claro no seu caso:

```jsx
function App() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  function openLogin() {
    setIsModalOpen(true);
  }

  function closeLogin() {
    setIsModalOpen(false);
  }

  return (
    <>
      <OpenLoginButton onClick={openLogin} />

      <Dialog open={isModalOpen} onClose={closeLogin}>
        <LoginForm />
      </Dialog>
    </>
  );
}
```

Perceba:

* o App **não sabe como o modal é feito**
* ele só sabe **quando** ele deve estar aberto

Isso é regra de negócio.

---

## 3. O papel dos componentes filhos (mãos e olhos)

Componentes como `Dialog`, `Button`, `Form`:

* **não tomam decisões globais**
* não sabem *por que* algo acontece
* apenas reagem a props

Eles devem ser capazes de dizer:

> “Me diga o estado que eu mostro e o que faço quando clicam em mim.”

Exemplo:

```jsx
function Dialog({ open, onClose, children }) {
  // apenas reflete open
}
```

Isso é UI pura.

---

## 4. A regra de ouro (anota essa)

> **Quem possui o estado, possui a regra.
> Quem recebe props, apenas exibe.**

Se um componente:

* chama `useState` para algo que impacta outros componentes
* decide sozinho quando algo abre/fecha
* coordena múltiplas partes da UI

Ele **provavelmente não deveria**.

---

## 5. Como organizar mentalmente seus componentes

### Padrão saudável

```
App (negócio, estado, decisões)
 ├── Dialog (UI)
 │     └── Form (UI)
 ├── Button (UI)
 └── ...
```

Fluxo sempre **de cima para baixo**:

* dados descem
* eventos sobem

---

## 6. Onde entram os hooks nessa história

### `useState`

* pertence a quem decide
* quase sempre nos níveis mais altos

### `useEffect`

* pertence a quem integra com o mundo externo
* geralmente nos componentes de UI (como `Dialog`)

### `useRef`

* pertence a quem toca o DOM
* nunca carrega regra de negócio

---

## 7. Sinais de alerta (muito importantes)

Você está misturando regras quando:

* um componente de UI tem muitos `if`
* um modal decide quando abrir
* um botão sabe demais
* você precisa “avisar” vários componentes manualmente

Isso indica que o estado está no lugar errado.

---