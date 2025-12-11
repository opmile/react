# A MENTALIDADE DO REACT

React muda a forma de pensar o frontend. Ele te tira daquela vibe de “ficar manipulando o DOM na munheca” (imperativo) e te coloca num esquema de “dizer *o que* você quer que a interface pareça e deixar o React garantir isso” (declarativo).

---

## 1. **Componentes: a unidade básica**

Um componente é só uma função que recebe dados e devolve UI.

```jsx
function Botao(props) {
  return <button>{props.texto}</button>;
}
```

**Como pensar:**

* Um componente é como uma função pura de interface (pelo menos idealmente).
* Ele recebe props → devolve JSX.
* Ele é *reutilizável* e *combinável*. Tudo na tela vira composição de componentes.

**Você não mexe no DOM diretamente.**
Você descreve a UI como *um estado ideal* e deixa o React renderizar para você.

---

## 2. **Estado (state): a memória reativa**

Estado é a “memória viva” do componente.

```jsx
const [count, setCount] = useState(0);
```

**Mentalidade:**

* Quando o estado muda, o React re-renderiza automaticamente o componente.
* Você NÃO precisa mandar o navegador alterar o texto, trocar cor, mudar tamanho.
* Basta mudar o estado → React recalcula a árvore e sincroniza com o DOM real.

Ou seja:
**Você controla dados, não pixels.**

---

# 3. **Renderização: a mágica invisível**

Quando algo muda (estado, props, contexto), o React:

1. Reexecuta o componente (ele é só uma função).
2. Gera um “JSX → árvore virtual” nova.
3. Compara com a árvore anterior (diff).
4. Atualiza no DOM só o indispensável.

Isso faz com que você foque na *lógica*, não nos passos de manipulação da interface.

---

# 4. **Imperativo vs Declarativo**

## Imperativo (tradicional):

Você diz passo a passo *como* alterar a interface.

```js
const el = document.querySelector("#contador");
el.textContent = contador;
el.style.color = "red";
```

Você pensa em termos de ações específicas no DOM.

## Declarativo (React):

Você descreve *o que* a interface deve representar a partir do estado.

```jsx
<p style={{ color: "red" }}>{contador}</p>
```

Se `contador` mudar, você não mexe no DOM.
**React faz isso.**

---

# 5. **JSX: a forma de escrever UI dentro do JavaScript**

JSX não é HTML.
É açúcar sintático: cada `<Coisa />` vira uma chamada JavaScript:

```jsx
<div className="box">Olá</div>
```

Vira:

```js
React.createElement("div", { className: "box" }, "Olá");
```

Ou, nas versões modernas:

```js
jsx("div", { className: "box", children: "Olá" });
```

### **Regras essenciais do JSX**

**1. Tem que ter um único elemento pai**

```jsx
return (
  <div>
    <p>Ok</p>
  </div>
);
```

**2. `class` vira `className`**
JSX usa camelCase porque é JavaScript.

**3. Atributos são expressões entre `{}`**

```jsx
<p style={{ color: cor }}>{texto}</p>
```

**4. Tags começam com maiúscula = componente**

```jsx
<MyButton />  // componente
<div />       // elemento HTML
```

**5. Expressões dentro de JSX funcionam, statements não**
Corre:

```jsx
{idade > 18 && <p>Maior de idade</p>}
```

Não corre:

```jsx
if (idade > 18) { return <p>Maior</p> } // não pode direto dentro do JSX
```

**6. Fragmentos para evitar divs desnecessárias**

```jsx
<>
  <p>A</p>
  <p>B</p>
</>
```

---

## A Mentalidade

* **Componente** = função que descreve a UI.
* **Estado** = dados que, quando mudam, disparam a re-renderização.
* **Renderização declarativa** = você descreve o “estado final”, não o passo a passo.
* **JSX** = forma de escrever a UI que vira chamadas JS.

React te pede uma mudança de mindset:
**Pare de pensar em manipular interface; comece a pensar em descrever interface.*