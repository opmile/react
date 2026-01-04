# `ProgressBar`

---

boa — isso é um componente simples por fora, mas ótimo pra entender **fluxo de dados**, **responsabilidade** e **integração com backend**.

Vou dividir em quatro partes:

1. ideia e contrato do componente
2. implementação via `style` inline (do jeito que você pediu)
3. forma alternativa de fazer a mesma coisa
4. como o `percent` normalmente vem do backend

---

## 1️⃣ Ideia central da ProgressBar

A barra de progresso **não calcula nada**.
Ela **só renderiza** com base em um número que recebe.

Contrato do componente:

```txt
entrada: percent (0 a 100)
saída: largura visual proporcional
```

Isso é importante:

* cálculo → fora
* visualização → dentro

---

## 2️⃣ Implementação clássica usando inline style (width)

### Componente ProgressBar

```jsx
export function ProgressBar({ percent }) {
  return (
    <div className="w-full h-3 bg-gray-200 rounded">
      <div
        className="h-full bg-blue-600 rounded transition-all"
        style={{ width: `${percent}%` }}
      />
    </div>
  )
}
```

Aqui está o ponto-chave:

```js
style={{ width: `${percent}%` }}
```

O React:

* recebe `percent`
* converte em string com `%`
* injeta direto no CSS inline
* o browser recalcula o layout

Sem estado interno, sem efeito colateral.

---

### Uso do componente

```jsx
<ProgressBar percent={40} />
```

Se `percent` mudar:

* o componente re-renderiza
* o `width` muda
* a barra cresce ou diminui

Simples e correto.

---

## 3️⃣ Forma alternativa: usando transform (scaleX)

Essa abordagem é menos comum, mas **mais performática** em animações.

### Por quê?

* `width` causa recalculo de layout
* `transform` usa a GPU

### Implementação

```jsx
export function ProgressBar({ percent }) {
  return (
    <div className="w-full h-3 bg-gray-200 rounded overflow-hidden">
      <div
        className="h-full bg-blue-600 origin-left transition-transform"
        style={{ transform: `scaleX(${percent / 100})` }}
      />
    </div>
  )
}
```

Aqui:

* largura continua 100%
* escala horizontal varia de `0` a `1`

---

### Quando usar cada uma

| Técnica  | Quando usar                              |
| -------- | ---------------------------------------- |
| `width`  | simples, leitura clara                   |
| `scaleX` | animações frequentes, progresso contínuo |

---

## 4️⃣ Como o `percent` vem do backend

Na prática, o backend **não manda “percent pronto”** sempre.

### Caso comum: progresso calculado

Backend envia algo como:

```json
{
  "completed": 30,
  "total": 50
}
```

No frontend, você calcula:

```js
const percent = Math.round((completed / total) * 100)
```

E passa:

```jsx
<ProgressBar percent={percent} />
```

---

### Caso alternativo: backend já envia percent

```json
{
  "progress": 72
}
```

Aí o fluxo fica direto:

```jsx
<ProgressBar percent={data.progress} />
```

---

## Fluxo completo (mental)

```txt
Backend
  ↓
fetch / axios
  ↓
estado no React
  ↓
percent calculado
  ↓
ProgressBar recebe via props
  ↓
style.width ou transform atualizado
```

A ProgressBar **não sabe de API**, **não sabe de cálculo**, **não sabe de estado global**.

Ela só reflete o dado.

---

## Erros comuns que você deve evitar

* barra calcular o percent internamente
* usar `useEffect` sem necessidade
* aceitar valores fora de 0–100 sem normalizar
* misturar lógica visual com lógica de negócio

---