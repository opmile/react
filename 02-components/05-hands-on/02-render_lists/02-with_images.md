# Adicionando Imagem Dinâmica

---

## 1) Primeiro: como os dados precisam vir

Para renderizar imagens dinamicamente, **o produto PRECISA trazer a URL da imagem**.
Não existe mágica no React aqui.

Exemplo realista de dados:

```js
const produtos = [
  {
    id: 1,
    nome: "Notebook Gamer",
    preco: 4500,
    imagem: "https://via.placeholder.com/300x200",
    emEstoque: true
  },
  {
    id: 2,
    nome: "Mouse Wireless",
    preco: 180,
    imagem: "https://via.placeholder.com/300x200",
    emEstoque: false
  }
];
```

Mentalidade importante:

* React **não carrega imagem**
* Ele **só renderiza uma tag `<img>`**
* Quem busca a imagem é o navegador

---

## 2) Componente de card com imagem (ProdutoCard)

Agora vamos aplicar **separação de seções**, como antes:

```jsx
export function ProdutoCard({ produto }) {
  const { nome, preco, imagem, emEstoque } = produto;

  return (
    <section className="border rounded overflow-hidden">
      
      {/* imagem */}
      <header>
        <img
          src={imagem}
          alt={nome}
          className="w-full h-48 object-cover"
        />
      </header>

      {/* conteúdo */}
      <main className="p-4 space-y-2">
        <h2 className="font-semibold">{nome}</h2>
        <p>R$ {preco}</p>
      </main>

      {/* ações / status */}
      <footer className="p-4">
        {emEstoque ? (
          <button className="bg-blue-600 text-white p-2 w-full rounded">
            Comprar
          </button>
        ) : (
          <span className="text-gray-400">Fora de estoque</span>
        )}
      </footer>

    </section>
  );
}
```

Aqui já está tudo certo:

* Imagem dinâmica
* Alt acessível
* Separação semântica clara
* UI declarativa

---

## 3) Renderizando a lista de produtos

Exatamente como antes, usando `map`:

```jsx
import { ProdutoCard } from "./ProdutoCard";

export default function ListaDeProdutos() {
  return (
    <div className="grid grid-cols-3 gap-4">
      {produtos.map(produto => (
        <ProdutoCard key={produto.id} produto={produto} />
      ))}
    </div>
  );
}
```

O que acontece:

* Cada item do array vira um card
* Cada card recebe sua própria imagem
* O React atualiza apenas os cards necessários


O componente **conhece `produtos` porque ele está no mesmo escopo** (arquivo/módulo) onde `produtos` foi declarado ou importado.

Quando você escreve algo assim:

```jsx
const produtos = [
  { id: 1, nome: "Notebook" },
  { id: 2, nome: "Mouse" }
];

export default function ListaDeProdutos() {
  return (
    <div>
      {produtos.map(produto => (
        <ProdutoCard key={produto.id} produto={produto} />
      ))}
    </div>
  );
}
```

O que permite isso funcionar é:

* `produtos` está **no mesmo arquivo**
* Logo, está **no mesmo escopo léxico**
* A função `ListaDeProdutos` consegue acessar variáveis definidas fora dela

Isso é **closure + escopo de módulo do JavaScript**.

---

Esse ponto é crucial:

```jsx
<ListaDeProdutos />
```

Aqui:

* Nenhuma prop foi passada
* Logo, `produtos` **não veio de fora**
* Ele já existia antes da função ser executada

Ou seja:

> O componente não “recebeu” os dados — ele **já tinha acesso a eles**.

---

**Quando isso é aceitável?**

Usar dados assim é ok quando:

* São dados mockados
* São dados temporários
* É um estudo
* O componente é dono desses dados

Mas **não é o padrão ideal** em aplicações reais.

---

**O padrão correto (e profissional) acontece quando:**

O componente **pai** conhece os dados
O componente **filho** recebe via props

Exemplo correto:

```jsx
export default function App() {
  return <ListaDeProdutos produtos={produtos} />;
}
```

```jsx
export function ListaDeProdutos({ produtos }) {
  return (
    <div>
      {produtos.map(produto => (
        <ProdutoCard key={produto.id} produto={produto} />
      ))}
    </div>
  );
}
```

* Dados fluem de cima para baixo
* O componente é reutilizável
* Você respeita o modelo mental do React

---

## 4) E se a imagem estiver local no projeto?

Situação comum em projetos iniciais.

### Opção 1: importar a imagem

```js
import notebookImg from "./assets/notebook.png";

const produtos = [
  {
    id: 1,
    nome: "Notebook",
    preco: 4500,
    imagem: notebookImg
  }
];
```

Funciona porque:

* Vite resolve o caminho da imagem no build
* `imagem` vira uma URL válida

---

### Opção 2: pasta `public` (mais simples)

```js
imagem: "/images/notebook.png"
```

E no projeto:

```
public/
 └─ images/
    └─ notebook.png
```

Opinião direta:

* **Para e-commerce real → URLs vindas da API**
* **Para estudo → public ou import local**

---

## 5) Boas práticas específicas para imagens

### Sempre:

* `alt={nome}`
* `object-cover` para evitar distorção
* Definir altura fixa no card

### Evite:

* lógica de imagem dentro da lista
* condicional demais no JSX principal

---

## 6) Tornando ainda mais dinâmico (fallback de imagem)

```jsx
<img
  src={imagem || "/images/placeholder.png"}
  alt={nome}
/>
```

Ou:

```jsx
function getImagem(produto) {
  return produto.imagem ?? "/images/placeholder.png";
}
```

E no JSX:

```jsx
<img src={getImagem(produto)} />
```

---


