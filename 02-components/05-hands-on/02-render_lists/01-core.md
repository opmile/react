# Renderização de Lista Dinâmica

A ideia central é:

> **Você não “cria elementos”. Você descreve como cada item de um dado vira UI.**

---

## 1) O cenário real

Imagine que você tem dados vindos de uma API ou mock:

```js
const produtos = [
  { id: 1, nome: "Notebook", preco: 3500, emEstoque: true },
  { id: 2, nome: "Mouse", preco: 150, emEstoque: false },
  { id: 3, nome: "Teclado", preco: 300, emEstoque: true }
];
```

Seu objetivo:

* Renderizar **uma lista**
* Cada item virar um **componente**
* Cada parte do objeto ir para uma **section visual diferente**

---

## 2) Primeiro erro comum (importante entender)

Isso **não funciona**:

```jsx
produtos.forEach(produto => {
  return <div>{produto.nome}</div>
});
```

Por quê?
Porque **React precisa de um array de JSX**, e `forEach` não retorna nada.

Você SEMPRE usa `map`.

---

## 3) Renderização básica com `map`

```jsx
export default function ListaDeProdutos() {
  return (
    <div>
      {produtos.map(produto => (
        <div key={produto.id}>
          {produto.nome}
        </div>
      ))}
    </div>
  );
}
```

Mentalidade:

* `map` transforma **dados → UI**
* Cada item retorna JSX
* `key` identifica o elemento para o React

---

## 4) Agora do jeito certo: quebrando em seções

Vamos estruturar visualmente cada item:

```jsx
{produtos.map(produto => (
  <section key={produto.id} className="border p-4 rounded">
    
    <header>
      <h2>{produto.nome}</h2>
    </header>

    <main>
      <p>Preço: R$ {produto.preco}</p>
    </main>

    <footer>
      {produto.emEstoque ? "Disponível" : "Fora de estoque"}
    </footer>

  </section>
))}
```

Aqui já tem um conceito forte:

* **Cada item da lista vira um bloco semântico**
* Cada parte do objeto vai para uma **section específica**
* O JSX reflete a estrutura mental do componente

---

## 5) Evolução natural: transformar cada item em um componente

Agora entra o React de verdade.

### Componente `ProdutoCard`

```jsx
export function ProdutoCard({ nome, preco, emEstoque }) {
  return (
    <section className="border p-4 rounded">
      <header>
        <h2>{nome}</h2>
      </header>

      <main>
        <p>Preço: R$ {preco}</p>
      </main>

      <footer>
        {emEstoque ? "Disponível" : "Fora de estoque"}
      </footer>
    </section>
  );
}
```

---

## 6) Mapeando e renderizando o componente

```jsx
import { ProdutoCard } from "./ProdutoCard";

export default function ListaDeProdutos() {
  return (
    <div className="space-y-3">
      {produtos.map(produto => (
        <ProdutoCard
          key={produto.id}
          nome={produto.nome}
          preco={produto.preco}
          emEstoque={produto.emEstoque}
        />
      ))}
    </div>
  );
}
```

Agora você tem:

* Dados separados de UI
* Um componente reutilizável
* Uma lista declarativa e limpa

---

## 7) Forma ainda mais limpa (padrão real do mercado)

Passar o objeto inteiro:

```jsx
<ProdutoCard key={produto.id} produto={produto} />
```

E no componente:

```jsx
export function ProdutoCard({ produto }) {
  const { nome, preco, emEstoque } = produto;

  return (
    <section>
      <h2>{nome}</h2>
      <p>R$ {preco}</p>
      <span>{emEstoque ? "Disponível" : "Indisponível"}</span>
    </section>
  );
}
```

Opinião forte:
**Esse é o padrão mais legível quando o objeto cresce.**

---

## 8) Como o React pensa isso internamente (resumo mental)

1. `map` cria um **array de elementos JSX**
2. Cada JSX vira um nó no **Virtual DOM**
3. `key` ajuda o React a saber:

   * qual item mudou
   * qual entrou
   * qual saiu
4. O React atualiza **somente os nós afetados**

---

## 9) Erros comuns que você deve evitar

### 1. Usar index como key

```jsx
key={index} // errado na maioria dos casos
```

Use **id estável**.

### 2. Colocar lógica pesada dentro do JSX

Prefira preparar os dados antes.

### 3. Mapear sem componente quando cresce

Se passou de 10–15 linhas, **vire componente**.

---

