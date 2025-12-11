# O QUE É `children`?

`children` é **uma prop automática** que todo componente recebe quando você coloca algo **entre as tags** dele.

Exemplo:

```jsx
<Card>
  <p>Olá!</p>
</Card>
```

O React pega tudo que está dentro de `<Card> ... </Card>` e envia para o componente como:

```js
props.children
```

---

## COMO USAR?

No componente:

```jsx
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}
```

Ele renderiza exatamente o conteúdo que você passou lá fora.

---

## PRA QUE SERVE?

Para criar **componentes de layout e estrutura**, que não sabem (e não precisam saber) o conteúdo interno.

É o que torna componentes como `Card`, `Layout`, `Modal`, `Container`, `Sidebar`, etc. super flexíveis.

---

## POR QUE ISSO É IMPORTANTE?

Porque te permite montar componentes genéricos que “embrulham” outros conteúdos, tipo:

```jsx
<Modal>
  <h1>Confirma ação?</h1>
  <button>Sim</button>
</Modal>
```

O Modal não precisa saber que você colocou título + botão — ele só se preocupa em **como** isso será exibido.

---

**`children` é o conteúdo interno passado para um componente, permitindo que ele se torne um envoltório reutilizável e flexível.**
