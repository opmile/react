# Elevar Estado do Componente

---

Você está **certíssima** — e essa percepção já é nível avançado.
Isso não é detalhe de estilo, é **arquitetura de fluxo de dados**.

Vou ser direto e honesto.

---

## Sim: o estado **deveria** ser elevado

Quando um componente **controla o próprio estado de aberto/fechado**, ele vira:

* difícil de orquestrar
* difícil de integrar
* impossível de controlar de fora
* parcialmente “caixa-preta”

Um modal **não deveria decidir quando existe**.
Ele deveria apenas **obedecer**.

---

## O problema do modal “autocontrolado”

Esse padrão:

```jsx
function Modal() {
  const [isOpen, setIsOpen] = useState(false);
}
```

Funciona, mas cria um componente que:

* não pode ser aberto externamente
* não pode reagir a rotas
* não pode reagir a erros
* não pode ser fechado por outro evento
* não pode ser sincronizado com URL, Redux, contexto, etc.

Ou seja: **ele funciona isolado, mas não compõe bem**.

---

## O princípio real por trás disso

Isso é o famoso conceito de **“single source of truth”**.

A pergunta correta não é:

> “Quem abre o modal?”

É:

> **“Quem sabe se o modal deve estar aberto?”**

Na maioria dos casos:
👉 quem sabe é **o componente pai**.

---

## O padrão arquitetural correto: modal controlado

### App.jsx (quem manda)

```jsx
export default function App() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsModalOpen(true)}>
        Abrir modal
      </button>

      <Modal
        open={isModalOpen}
        onClose={() => setIsModalOpen(false)}
      >
        Conteúdo
      </Modal>
    </>
  );
}
```

---

### Modal.jsx (quem obedece)

```jsx
import { useEffect, useRef } from "react";

export default function Modal({ open, onClose, children }) {
  const dialogRef = useRef(null);

  useEffect(() => {
    const dialog = dialogRef.current;
    if (!dialog) return;

    open ? dialog.showModal() : dialog.close();
  }, [open]);

  return (
    <dialog ref={dialogRef} onClose={onClose}>
      {children}
      <button onClick={onClose}>Fechar</button>
    </dialog>
  );
}
```

Agora:

* o modal **não decide nada**
* ele é puramente reativo
* ele é previsível
* ele é reutilizável
* ele se comporta como um componente React legítimo

---

## Quando faz sentido o modal se autocontrolar?

Poucos casos, mas existem:

* modais extremamente locais
* UI descartável
* protótipos
* componentes que nunca serão reutilizados
* quando nenhum outro componente precisa saber do estado

Mesmo assim, **elevação de estado escala melhor**.

---

> **Estado é poder.
> Quem controla o estado controla o comportamento.**

E no React:

* quem usa → controla
* quem renderiza → obedece

Isso é exatamente o motivo de:

* controlled inputs
* controlled modals
* controlled accordions

---
