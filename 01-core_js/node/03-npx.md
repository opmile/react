# `npx` vs. `npm`

**npm** é usado para **instalar e gerenciar pacotes** (local ou globalmente).
Quando você instala algo globalmente com `npm install -g`, você está colocando um executável na sua máquina inteira, disponível em qualquer projeto. Isso é útil para ferramentas que você usa o tempo todo, mas traz risco de conflito de versões entre projetos diferentes.

**npx** não instala ferramentas globalmente. Ele **executa um pacote diretamente**, baixando-o temporariamente (ou reutilizando o cache) e garantindo a versão correta para aquela execução. Por isso ele é ideal para scaffolding e ferramentas pontuais, como `npm create vite@latest`.

Resumo mental:

* `npm` → instalar, atualizar, versionar dependências
* `npm -g` → instalar ferramentas permanentes na máquina (use com cuidado)
* `npx` → executar ferramentas sem poluir o ambiente global

Regra prática:
use **npx** para comandos ocasionais e **npm** para dependências do projeto.

---

Boa — isso fecha de vez o mapa mental entre **npm** e **npx**. Vou separar por cenários reais e explicar o porquê em cada um.

---

## Cenários em que **npm é a escolha certa**

### 1) Bibliotecas usadas pelo código da aplicação

**Exemplo:** React, Axios, React Router.

```bash
npm install react axios react-router-dom
```

Por quê:

* o código precisa estar disponível no projeto
* entra no `package.json`
* é versionado junto com a aplicação
* faz parte do build final

Aqui **npx não faz sentido**.

---

### 2) Ferramentas usadas durante o desenvolvimento

**Exemplo:** Vite, ESLint, Prettier.

```bash
npm install -D vite eslint prettier
```

Por quê:

* não vão para produção
* precisam de versão fixa por projeto
* são usadas repetidamente via scripts npm

---

### 3) Comandos recorrentes do projeto

**Exemplo:** subir servidor, buildar, testar.

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "lint": "eslint ."
}
```

Executados com:

```bash
npm run dev
```

Por quê:

* usam dependências locais
* garantem consistência entre máquinas
* não dependem do ambiente global

---

### 4) Instalação global (uso com moderação)

```bash
npm install -g nodemon
```

Use quando:

* a ferramenta é genérica
* você usa em muitos projetos
* versão não precisa ser travada por projeto

Mesmo assim, hoje isso é cada vez menos necessário.

---

## Cenários em que **npx é a escolha certa**

### 1) Scaffolding de projetos

**Exemplo:** Vite, Create React App.

```bash
npx create-vite my-project
```

Por quê:

* você executa uma única vez
* não quer instalar permanentemente
* sempre quer a versão correta/atual
* não polui o ambiente global

Esse é o uso mais clássico do npx.

---

### 2) Executar ferramentas raras ou pontuais

**Exemplo:** gerar algo uma única vez.

```bash
npx degit user/repo
```

Por quê:

* uso único ou muito raro
* não faz sentido instalar globalmente
* menos lixo no sistema

---

### 3) Testar um pacote rapidamente

**Exemplo:** rodar algo sem compromisso.

```bash
npx serve .
```

Por quê:

* você quer testar comportamento
* não quer dependência permanente
* execução efêmera

---

### 4) Evitar conflitos de versão

**Exemplo:** rodar exatamente uma versão específica.

```bash
npx vite@5.0.0
```

Por quê:

* garante versão exata
* ignora instalações globais
* comportamento previsível

---

## Mapa mental final

Use **npm** quando:

* a dependência faz parte do projeto
* precisa ser versionada
* será usada repetidamente

Use **npx** quando:

* o comando é pontual
* é scaffolding
* você não quer instalar nada globalmente
* quer a versão certa sem dor de cabeça
