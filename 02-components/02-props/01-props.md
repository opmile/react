# O QUE SÃO `PROPS`?

Pensa em props como **argumentos de uma função**, só que aplicados a componentes.
No fim do dia, um componente é basicamente isso:

```jsx
function MeuComponente(props) {
  return <div>{props.algumaCoisa}</div>;
}
```

Ou seja, **props são dados externos que entram no componente para ele produzir uma UI**.
O componente não deveria alterar essas props — elas são *imutáveis* do ponto de vista dele.

É como se o componente dissesse:
“Me diga quais dados você quer que eu use, e eu te devolvo a interface correspondente.”

---

## COMO AS PROPS CHEGAM A UM COMPONENTE

A regra é simples: **você passa props da mesma forma que passa atributos em HTML.**

```jsx
<Cabecalho titulo="Dashboard" usuario="Milena" />
```

Isso vira um objeto:

```js
{
  titulo: "Dashboard",
  usuario: "Milena"
}
```

E esse objeto cai como parâmetro na função:

```jsx
function Cabecalho(props) {
  return <h1>{props.titulo}</h1>;
}
```

Cada atributo passado no JSX vira uma propriedade no objeto `props`.

---

## DESESTRUTURAÇÃO: O JEITO MODERNO E LIMPO

Como props nada mais são que um objeto, desestruturar vira uma boa prática:

```jsx
function Cabecalho({ titulo, usuario }) {
  return (
    <header>
      <h1>{titulo}</h1>
      <p>Bem-vinda, {usuario}</p>
    </header>
  );
}
```

Fica legível, direto, e mais expressivo.

---

## PROPS NÃO SÃO “APENAS STRINGS”

Você pode passar **qualquer coisa** como prop:

### 1. Strings, números, booleanos

```jsx
<Card idade={19} ativo={true} nome="Milena" />
```

### 2. Arrays

```jsx
<Lista itens={['Java', 'React', 'C']}/>
```

### 3. Objetos

```jsx
<User data={{ nome: 'Milena', curso: 'CC' }} />
```

### 4. Funções (importantíssimo)

É assim que você “comunica para cima”.

```jsx
<Button onClick={handleDelete} />
```

Recebido assim:

```jsx
function Button({ onClick }) {
  return <button onClick={onClick}>Apagar</button>;
}
```

### 5. Componentes ou elementos

```jsx
<Modal footer={<Button texto="Ok" />} />
```

---

## PROPS SÃO IMUTÁVEIS

Isso é essencial para entender o fluxo de dados do React.

Quando você tenta fazer:

```js
props.idade = 20; // errado
```

…não dá. A ideia é:
**um componente só LÊ props; quem ENVIA controla o valor.**

Essa imutabilidade ajuda o React:

* a manter o fluxo de dados previsível;
* a facilitar re-renderizações;
* a simplificar a lógica.

---

## PROPS vs STATE (pra evitar confusão clássica)

**Props** = dados vindos de fora. Imutáveis.
**State** = dados internos ao componente. Mutáveis via `setState` / `useState`.

Exemplo:

```jsx
function Contador({ passo }) {
  const [valor, setValor] = useState(0);

  return (
    <button onClick={() => setValor(valor + passo)}>
      {valor}
    </button>
  );
}
```

Aqui:

* `passo` vem de fora → prop.
* `valor` muda internamente → state.

---

## O FLUXO DE DADOS NO REACT É DE CIMA PARA BAIXO

Isso é chamado de **unidirectional data flow**.

* O pai passa props para os filhos.
* Filhos não alteram props, mas podem *solicitar* coisas chamando funções recebidas como props.
* Isso evita bagunça mental e efeitos colaterais.

Exemplo:

```jsx
function Pai() {
  const [count, setCount] = useState(0);

  return (
    <Filho count={count} incrementar={() => setCount(count + 1)} />
  );
}

function Filho({ count, incrementar }) {
  return <button onClick={incrementar}>{count}</button>;
}
```

O filho não muda diretamente o estado, mas pede para o pai mudar.

---

## CHILDREN: UM TIPO ESPECIAL DE PROP

Muita gente esquece disso: **o conteúdo dentro de uma tag é passado como prop `children`.**

```jsx
<Card>
  <p>Texto dentro do cartão!</p>
</Card>
```

Internamente:

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}
```

Children é poderoso pra criar layouts reutilizáveis.

---

## “ESPALHAMENTO” (`...props`)?

O operador `...` no JSX pega **todas as propriedades do objeto** e as distribui como atributos do elemento.

```jsx
function CampoDeEntrada(props) {
  return <input {...props} />
}
```

Se você usar o componente assim:

```jsx
<CampoDeEntrada type="text" placeholder="Nome" maxLength={30} />
```

O React converte para:

```html
<input
  type="text"
  placeholder="Nome"
  maxLength="30"
/>
```

Ou seja:

* `...props` pega todas as props recebidas
* e passa cada uma delas diretamente para o `<input>`.

Isso transforma o componente em um **“wrapper” totalmente configurável**, sem você precisar mapear prop por prop manualmente.

É como dizer:
“Tudo que eu receber como prop, eu repasso pro meu input.”

**`...props` espalha todas as props recebidas para o elemento, tornando o componente flexível e configurável sem boilerplate.**

---

## VALIDAÇÃO DE PROPS (conceito importante)

React em si não valida props, mas o ecossistema criou o `prop-types`.

E em projetos reais, muita gente usa TypeScript pra isso.
Mas a ideia geral é: *você sabe quais props o componente espera, e se o pai errar, dá erro.*

Isso ajuda na manutenção a longo prazo.

---

## QUANDO USAR PROPS?

Sempre que:

* o valor vem de outro componente;
* você quer deixar o componente reaproveitável;
* ele precisa ser configurado para se comportar de formas diferentes;
* você quer transformar componentes em blocos parametrizáveis.

Props dão à UI a flexibilidade da programação funcional:
**mesma função, resultados diferentes dependendo dos argumentos.**

---

* Props são parâmetros de componente.
* Caem como objeto.
* Componentes recebem props, não mudam props.
* Podem ser strings, números, arrays, objetos, funções, outros componentes.
* Children é uma prop especial.
* Props descem, eventos sobem.
