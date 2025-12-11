# Formulário 

---

Em React, **componente é qualquer pedaço da interface que você consegue isolar, reaproveitar e dar um nome próprio**. É literalmente isso. Se um trecho de UI tem “identidade” e pode viver sozinho (um botão, um card, um input, um modal, um formulário inteiro), ele pode virar componente. Pode ser bem pequeno (um ícone), médio (um bloco com título + texto), ou enorme (uma página inteira). O critério real é: *faz sentido encapsular? ajuda a organizar? pode ser reutilizado?* Se a resposta é sim pra qualquer um desses, vira componente sem medo.

---

Beleza, Milena — vamos montar um **mapa mental poderoso**, mas leve, pra você finalmente entender *como* componentizar formulários de um jeito inteligente.
Sem exagerar, sem padrões enterprise — só o nível perfeito pra quem está começando com React, mas já pensa como dev madura.

A ideia aqui é você aprender a **enxergar componentes**, **organizar responsabilidades**, e **reutilizar UI** sem se embolar.

---

# 1) Primeiro: o que *faz sentido* virar componente?

Um formulário qualquer tem várias partes:

* Estrutura (o “container” do formulário)
* Campos
* Labels
* Botão de submit
* Tratamento de estado (valores)
* Validação
* Exibição de erros

Mas você não separa tudo.
Você separa **coisas que se repetem** ou **coisas que têm lógica própria**.

## Regras simples:

### Você cria um componente quando:

1. **Aquele pedaço se repete**
2. **Ou tem lógica suficiente pra viver sozinho**
3. **Ou melhora a legibilidade do formulário**

### Você NÃO cria componente quando:

* O formulário já é pequeno e a divisão vai dificultar em vez de ajudar
* Não existe repetição
* Não há lógica isolável

---

# 2) Vamos fazer um exercício prático

Montar um formulário de cadastro simples:

* Nome
* Email
* Senha
* Botão de enviar

**Versão ingênua (monolítica):**

```jsx
export default function Formulario() {
  const [nome, setNome] = useState("");
  const [email, setEmail] = useState("");
  const [senha, setSenha] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    console.log({ nome, email, senha });
  }

  return (
    <form onSubmit={handleSubmit}>
      <label>Nome</label>
      <input value={nome} onChange={e => setNome(e.target.value)} />

      <label>Email</label>
      <input value={email} onChange={e => setEmail(e.target.value)} />

      <label>Senha</label>
      <input value={senha} onChange={e => setSenha(e.target.value)} />

      <button type="submit">Enviar</button>
    </form>
  );
}
```

Funciona, mas não é componentizado.

Vamos transformar isso numa estrutura **madura**, mas ainda simples.

---

# 3) Primeira componentização inteligente: Campo genérico

Criamos um componente `Campo` que encapsula:

* label
* input
* props genéricas
* lógica visual

```jsx
export function Campo({ label, ...props }) {
  return (
    <div className="flex flex-col gap-1">
      <label>{label}</label>
      <input className="border p-2 rounded" {...props} />
    </div>
  );
}
```

Repara que ele usa **spread props** — isso te permite passar qualquer propriedade do input normal.

---

# 4) Usando o Campo no formulário

```jsx
import { Campo } from "./Campo";

export default function Formulario() {
  const [nome, setNome] = useState("");
  const [email, setEmail] = useState("");
  const [senha, setSenha] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    console.log({ nome, email, senha });
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-3">
      <Campo
        label="Nome"
        value={nome}
        onChange={e => setNome(e.target.value)}
      />
      
      <Campo
        label="Email"
        type="email"
        value={email}
        onChange={e => setEmail(e.target.value)}
      />

      <Campo
        label="Senha"
        type="password"
        value={senha}
        onChange={e => setSenha(e.target.value)}
      />

      <button type="submit" className="bg-blue-600 text-white p-2 rounded">
        Enviar
      </button>
    </form>
  );
}
```

Agora você tem um componente **genérico, reutilizável e simples**.

---

# 5) Evolução natural: separar o botão também

```jsx
export function Botao({ children, ...props }) {
  return (
    <button
      className="bg-blue-600 text-white p-2 rounded"
      {...props}
    >
      {children}
    </button>
  );
}
```

E no formulário:

```jsx
<Botao type="submit">Enviar</Botao>
```

Simples, minimalista, elegante.

---

# 6) Próximo passo: FormSection (quando fizer sentido)

Se o form crescer, você pode criar agrupamentos:

```jsx
export function FormSection({ titulo, children }) {
  return (
    <fieldset className="border p-4 rounded space-y-3">
      <legend>{titulo}</legend>
      {children}
    </fieldset>
  );
}
```

E usar assim:

```jsx
<FormSection titulo="Dados pessoais">
  <Campo ... />
  <Campo ... />
</FormSection>
```

---

# 7) A lógica mental que você deve carregar pra sempre

Você cria componentes de formulário quando:

### 1. O campo se repete

Ex: input, text-area, select, switch, checkbox…

### 2. Você quer padronizar estilo

Um único componente `Campo` controla o visual de todos.

### 3. Você quer isolar lógica

Ex: um input que faz formatação de CPF, validações, máscaras…

### 4. Você quer melhorar a leitura do formulário

Para não ficar 300 linhas em um único componente.

---

# 8) Como chegar na mentalidade “profissional” sem complicar

Pensa assim:

* **Formulário** → responsável por:

  * estado
  * envio
  * validação geral

* **Campo** → responsável por:

  * label
  * input
  * UI
  * props genéricas

* **Botão** → responsável por:

  * ação
  * estilo

* **Section (opcional)** → agrupar visualmente

Cada parte tem uma responsabilidade clara.
Nada de inventar componente onde não precisa.

---
