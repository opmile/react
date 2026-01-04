# Compound Components

---

boa pergunta — isso aqui é **design de componente bem feito**, não só sintaxe bonita.

Vou explicar **por quê**, **como funciona**, **quando usar** e **como fica o código e a estrutura de arquivos**, tudo com clareza.

---

## Ideia central: componentes compostos (Compound Components)

O padrão `Card`, `Card.Header`, `Card.Body` é chamado de **Compound Components**.

A ideia é simples e poderosa:

> Um componente pai define o contexto e o comportamento geral,
> e componentes filhos especializados se organizam semanticamente dentro dele.

Você não passa tudo via props.
Você **compõe a interface como se fosse HTML semântico**, só que com lógica.

---

## Por que isso é reaproveitamento de verdade

Compare:

### ❌ abordagem fraca (props demais)

```jsx
<Card
  title="Meu título"
  subtitle="Descrição"
  footer={<Button />}
/>
```

Problemas:

* Card fica inchado
* difícil estender
* layout engessado
* muda uma coisa → quebra tudo

---

### ✅ abordagem forte (composição)

```jsx
<Card>
  <Card.Header>Meu título</Card.Header>
  <Card.Body>Descrição</Card.Body>
</Card>
```

Vantagens:

* leitura natural
* flexível
* extensível
* cada parte tem responsabilidade clara

Isso é **arquitetura**, não só React.

---

## Como funciona tecnicamente

`Card.Header` e `Card.Body` **não são mágicos**.

Eles são **propriedades de uma função**.

```js
Card.Header = Header
Card.Body = Body
```

React só enxerga componentes.
O “namespace” é só organização mental e semântica.

---

## Estrutura de arquivos recomendada

```
components/
└── Card/
    ├── Card.jsx
    ├── CardHeader.jsx
    ├── CardBody.jsx
    └── index.js
```

Isso escala muito bem.

---

## Implementação passo a passo

### Card.jsx (componente raiz)

```jsx
export function Card({ children }) {
  return (
    <div className="rounded-lg border shadow-sm p-4 bg-white">
      {children}
    </div>
  )
}
```

Responsabilidade:

* layout
* container
* estilo geral

---

### CardHeader.jsx

```jsx
export function CardHeader({ children }) {
  return (
    <div className="mb-2 border-b pb-2">
      <h2 className="text-lg font-semibold">{children}</h2>
    </div>
  )
}
```

Responsabilidade:

* topo do card
* título, ações, etc

---

### CardBody.jsx

```jsx
export function CardBody({ children }) {
  return (
    <div className="text-sm text-gray-700">
      {children}
    </div>
  )
}
```

Responsabilidade:

* conteúdo principal

---

### index.js (onde a mágica acontece)

```jsx
import { Card } from "./Card"
import { CardHeader } from "./CardHeader"
import { CardBody } from "./CardBody"

Card.Header = CardHeader
Card.Body = CardBody

export { Card }
```

Isso cria a API:

```jsx
<Card>
  <Card.Header />
  <Card.Body />
</Card>
```

---

## Uso real em um componente

```jsx
<Card>
  <Card.Header>Perfil do usuário</Card.Header>
  <Card.Body>
    <p>Email: user@email.com</p>
    <p>Status: ativo</p>
  </Card.Body>
</Card>
```

Se amanhã você quiser:

* `Card.Footer`
* `Card.Actions`
* `Card.Image`

é só adicionar — **sem quebrar nada**.

---

## Quando esse padrão vale a pena

Use quando:

* o componente tem **partes bem definidas**
* o layout pode variar
* você quer **API limpa e expressiva**
* o componente vai ser reutilizado muito

Não use quando:

* é algo simples e descartável
* não há estrutura interna clara

---

## Use Cases

> Use **Compound Components** quando **estrutura importa mais do que configuração**.

Se você precisa **organizar partes**, não só passar props → é um bom candidato.
Se você só quer variar aparência → provavelmente **variantes** resolvem melhor.

---

## 1️⃣ Modal / Dialog (caso clássico e legítimo)

### Por que faz sentido

Um modal **tem partes obrigatórias e opcionais**:

* Header
* Body
* Footer / Actions

A ordem importa, o contexto é compartilhado, e o layout é previsível.

### Uso

```jsx
<Modal>
  <Modal.Header>Excluir item</Modal.Header>
  <Modal.Body>
    Essa ação não pode ser desfeita.
  </Modal.Body>
  <Modal.Footer>
    <Button variant="ghost">Cancelar</Button>
    <Button variant="danger">Excluir</Button>
  </Modal.Footer>
</Modal>
```

### Por que não é overengineering

* não vira um monstro de props
* cada parte tem responsabilidade clara
* leitura imediata do layout

---

## 2️⃣ Tabs (onde composição ganha de props)

### Problema real

Tabs envolvem:

* lista de gatilhos
* conteúdo associado
* estado compartilhado

### Uso com Compound Components

```jsx
<Tabs>
  <Tabs.List>
    <Tabs.Trigger value="profile">Perfil</Tabs.Trigger>
    <Tabs.Trigger value="settings">Configurações</Tabs.Trigger>
  </Tabs.List>

  <Tabs.Content value="profile">
    Conteúdo do perfil
  </Tabs.Content>

  <Tabs.Content value="settings">
    Conteúdo das configurações
  </Tabs.Content>
</Tabs>
```

### Por que funciona

* sem props mágicas
* relação visual = relação lógica
* estado vive no `Tabs`

Esse padrão é usado por Radix, Reach UI, Headless UI.

---

## 3️⃣ Dropdown / Menu

### Quando props começam a doer

```jsx
<Dropdown
  trigger={<Button />}
  items={[...]}
  align="right"
/>
```

Isso quebra rápido.

### Composição resolve

```jsx
<Dropdown>
  <Dropdown.Trigger>
    <Button>Opções</Button>
  </Dropdown.Trigger>

  <Dropdown.Content>
    <Dropdown.Item>Editar</Dropdown.Item>
    <Dropdown.Item>Excluir</Dropdown.Item>
  </Dropdown.Content>
</Dropdown>
```

### Por que é o fit certo

* hierarquia clara
* sem prop drilling
* leitura natural

---

## 4️⃣ Form (caso avançado, mas real)

### Contexto

Forms têm:

* estado compartilhado
* validação
* labels, errors, inputs

### Uso

```jsx
<Form>
  <Form.Field name="email">
    <Form.Label>Email</Form.Label>
    <Form.Input />
    <Form.Error />
  </Form.Field>
</Form>
```

### Quando vale a pena

* formulários grandes
* regras complexas
* reutilização de campos

### Quando NÃO vale

* formulários simples
* páginas isoladas

---

## 5️⃣ Listas ricas (não CRUD simples)

### Exemplo: lista com ações, header e item

```jsx
<List>
  <List.Header>Usuários</List.Header>

  <List.Item>
    <List.Title>Milena</List.Title>
    <List.Actions>
      <Button>Editar</Button>
    </List.Actions>
  </List.Item>
</List>
```

Isso só vale a pena quando:

* layout do item é consistente
* ações variam
* o padrão se repete muito

---

## ❌ Onde NÃO usar (para evitar overengineering)

### ❌ Botão simples

```jsx
<Button.Icon />
<Button.Label />
```

Péssimo. Variante resolve melhor.

---

### ❌ Typography

```jsx
<Typography>
  <Typography.H1 />
</Typography>
```

Isso é forçar padrão onde **variante já resolve**.

---

## Comparação rápida: quando usar o quê

| Situação          | Melhor escolha      |
| ----------------- | ------------------- |
| Variar aparência  | Variantes           |
| Variar estrutura  | Compound Components |
| Layout previsível | Compound            |
| API simples       | Props               |
| Design system     | Ambos               |

---

Compound Components:

* **não são padrão default**
* **não são sinal de senioridade sozinhos**
* são uma ferramenta de **clareza estrutural**

Use quando:

> ler o JSX já explica a interface

Se precisar justificar demais, provavelmente é overengineering.
