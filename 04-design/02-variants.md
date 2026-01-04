# Usando `variant`

---

## Ideia central: reaproveitamento via variantes

Em vez de criar:

* `Title`
* `Subtitle`
* `Paragraph`
* `Caption`

você cria **um único componente** que varia **comportamento + semântica + estilo** via uma prop:

```jsx
<Typography variant="h1" />
<Typography variant="body" />
<Typography variant="caption" />
```

Isso:

* reduz componentes inúteis
* cria consistência visual
* vira base de um design system

---

## O papel da prop `variant`

A `variant` responde a três perguntas ao mesmo tempo:

1. **Qual tag HTML usar?** (`h1`, `p`, `span`)
2. **Qual estilo aplicar?** (Tailwind)
3. **Qual semântica o texto carrega?**

Um único ponto de decisão → **única fonte de verdade tipográfica**.

---

## Estrutura mental do componente

Você separa em dois mapas:

### 1. Mapa semântico (tags)

```js
const variantToTag = {
  h1: "h1",
  h2: "h2",
  h3: "h3",
  body: "p",
  caption: "span",
}
```

### 2. Mapa visual (estilos)

```js
const variantToClass = {
  h1: "text-4xl font-bold tracking-tight",
  h2: "text-3xl font-semibold",
  h3: "text-2xl font-semibold",
  body: "text-base text-gray-700",
  caption: "text-sm text-gray-500",
}
```

Separar isso é crucial:

* semântica ≠ estilo
* você pode mudar design sem quebrar HTML
* acessibilidade agradece

---

## Implementação do Typography

```jsx
export function Typography({
  variant = "body",
  children,
  className = "",
}) {
  const Component = variantToTag[variant] ?? "p"
  const styles = variantToClass[variant] ?? variantToClass.body

  return (
    <Component className={`${styles} ${className}`}>
      {children}
    </Component>
  )
}
```

---

## Uso prático

```jsx
<Typography variant="h1">
  Dashboard
</Typography>

<Typography variant="body">
  Bem-vinda de volta, Milena.
</Typography>

<Typography variant="caption" className="italic">
  Última atualização há 2 minutos
</Typography>
```

Resultado:

* HTML correto
* visual consistente
* flexibilidade controlada

---

## Por que isso escala bem

Se amanhã:

* o design mudar → muda só o objeto de estilos
* quiser modo dark → troca classes condicionalmente
* quiser internacionalizar → nada muda

Esse componente vira **infraestrutura**, não detalhe.

---

## Use Cases

A ideia é sempre a mesma:

* **1 componente**
* **mapas de variantes**
* **API previsível**
* **zero explosão de props**

---

## 1️⃣ Button (o caso mais clássico e mais malfeito por aí)

### Variantes comuns

* visual: `primary`, `secondary`, `ghost`, `danger`
* tamanho: `sm`, `md`, `lg`

### Implementação

```jsx
const variantStyles = {
  primary: "bg-blue-600 text-white hover:bg-blue-700",
  secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
  ghost: "bg-transparent hover:bg-gray-100",
  danger: "bg-red-600 text-white hover:bg-red-700",
}

const sizeStyles = {
  sm: "px-3 py-1 text-sm",
  md: "px-4 py-2 text-base",
  lg: "px-6 py-3 text-lg",
}

export function Button({
  variant = "primary",
  size = "md",
  className = "",
  ...props
}) {
  return (
    <button
      className={`
        rounded-md font-medium transition
        ${variantStyles[variant]}
        ${sizeStyles[size]}
        ${className}
      `}
      {...props}
    />
  )
}
```

### Uso

```jsx
<Button>Salvar</Button>
<Button variant="ghost">Cancelar</Button>
<Button variant="danger" size="sm">Excluir</Button>
```

---

## 2️⃣ Badge / Tag (estado visual puro)

Excelente exemplo de **variante sem semântica complexa**.

### Variantes

* `success`, `warning`, `error`, `info`

```jsx
const badgeVariants = {
  success: "bg-green-100 text-green-700",
  warning: "bg-yellow-100 text-yellow-800",
  error: "bg-red-100 text-red-700",
  info: "bg-blue-100 text-blue-700",
}

export function Badge({ variant = "info", children }) {
  return (
    <span
      className={`
        inline-flex items-center rounded-full px-2 py-1 text-xs font-medium
        ${badgeVariants[variant]}
      `}
    >
      {children}
    </span>
  )
}
```

### Uso

```jsx
<Badge variant="success">Ativo</Badge>
<Badge variant="error">Erro</Badge>
```

---

## 3️⃣ Alert (variante + estrutura fixa)

Aqui a variante **muda comportamento visual**, mas a estrutura é estável.

```jsx
const alertVariants = {
  success: "bg-green-50 text-green-800 border-green-300",
  error: "bg-red-50 text-red-800 border-red-300",
  warning: "bg-yellow-50 text-yellow-800 border-yellow-300",
}

export function Alert({ variant = "success", children }) {
  return (
    <div
      className={`
        border rounded-md p-4
        ${alertVariants[variant]}
      `}
    >
      {children}
    </div>
  )
}
```

Uso:

```jsx
<Alert variant="warning">
  Sua sessão está prestes a expirar.
</Alert>
```

---

## 4️⃣ Input (estado visual, não estrutura)

Input é ótimo exemplo de variante por **estado**, não por tipo.

### Variantes

* `default`
* `error`
* `disabled`

```jsx
const inputVariants = {
  default: "border-gray-300 focus:border-blue-500",
  error: "border-red-500 focus:border-red-500",
  disabled: "bg-gray-100 cursor-not-allowed",
}

export function Input({
  variant = "default",
  ...props
}) {
  return (
    <input
      className={`
        w-full rounded-md border px-3 py-2 outline-none
        ${inputVariants[variant]}
      `}
      {...props}
    />
  )
}
```

Uso:

```jsx
<Input placeholder="Email" />
<Input variant="error" />
<Input disabled />
```

---

## 5️⃣ Avatar (variante de tamanho + forma)

```jsx
const sizeVariants = {
  sm: "w-8 h-8",
  md: "w-10 h-10",
  lg: "w-14 h-14",
}

const shapeVariants = {
  circle: "rounded-full",
  square: "rounded-md",
}

export function Avatar({
  size = "md",
  shape = "circle",
  src,
}) {
  return (
    <img
      src={src}
      className={`
        object-cover
        ${sizeVariants[size]}
        ${shapeVariants[shape]}
      `}
    />
  )
}
```

Uso:

```jsx
<Avatar src="/me.png" />
<Avatar size="lg" shape="square" />
```

---

## 6️⃣ Card com variante (quando NÃO usar compound)

Aqui a variante resolve melhor que composição.

```jsx
const cardVariants = {
  default: "bg-white border",
  outlined: "border-2 bg-transparent",
  elevated: "bg-white shadow-md",
}

export function Card({ variant = "default", children }) {
  return (
    <div
      className={`
        rounded-lg p-4
        ${cardVariants[variant]}
      `}
    >
      {children}
    </div>
  )
}
```

Uso:

```jsx
<Card variant="elevated">Conteúdo</Card>
```

---

## Padrão mental que você deve guardar

Variants são ideais quando:

* **estrutura não muda**
* **layout é previsível**
* **diferença é visual ou comportamental**
* **API deve ser simples**

Compound Components são melhores quando:

* estrutura varia
* hierarquia importa
* leitura do JSX importa mais que props

---