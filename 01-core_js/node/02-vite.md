# Vite

---

## 1) O que é “scaffolding”

Scaffolding é **criar a estrutura inicial de um projeto automaticamente**.

Em vez de você:

* criar pastas manualmente
* configurar build
* configurar servidor de desenvolvimento
* integrar ferramentas uma a uma

um scaffolding faz tudo isso **em um único comando**, usando boas práticas já embutidas.

Vite é exatamente isso: **uma ferramenta de scaffolding + build + dev server**.

---

## 2) O que é o Vite

Vite é uma ferramenta moderna para criar projetos frontend.

Ele resolve três coisas fundamentais:

1. cria a estrutura inicial do projeto
2. fornece um servidor de desenvolvimento rápido
3. gera o build otimizado para produção

Importante:
Vite **não é React**.
Ele funciona com React, Vue, Vanilla JS, Svelte, etc.

---

## 3) Para que o Vite serve na prática

Quando você usa Vite, você ganha:

* hot reload instantâneo
* configuração mínima
* build rápido
* suporte nativo a ES Modules
* integração automática com npm

Você passa a focar em **escrever código**, não configurar ambiente.

---

## 4) Como criar um projeto com Vite (do zero)

Passos completos:

### 1. Criar o projeto

```bash
npm create vite@latest
```

O terminal vai perguntar:

* nome do projeto
* framework (React, Vanilla, etc.)
* linguagem (JS ou TS)

### 2. Entrar na pasta

```bash
cd nome-do-projeto
```

### 3. Instalar dependências

```bash
npm install
```

### 4. Rodar o servidor de desenvolvimento

```bash
npm run dev
```

Pronto.
Você tem um projeto funcional rodando no navegador.

---

## 5) Estrutura típica de um projeto Vite (React)

```
meu-projeto/
├─ index.html
├─ package.json
├─ node_modules/
├─ src/
│  ├─ main.jsx
│  └─ App.jsx
└─ vite.config.js
```

O Vite:

* já configurou o build
* já configurou o servidor
* já criou scripts npm
* já ajustou o `.gitignore`

---

## 6) Criar projeto “cru” com `npm init`

Se você rodar:

```bash
npm init
```

Você ganha apenas:

* um `package.json` vazio
* nenhum bundler
* nenhum dev server
* nenhuma estrutura

A partir daí você teria que:

* escolher e configurar bundler (Vite, Webpack, etc.)
* configurar scripts manualmente
* integrar hot reload
* configurar build de produção

Funciona, mas **exige muito mais conhecimento e tempo**.

---

## 7) Diferença clara: Vite vs projeto cru

**Projeto cru (`npm init`)**

* você controla tudo
* ideal para aprendizado interno
* muito trabalho manual
* fácil errar configurações

**Projeto com Vite**

* pronto para desenvolvimento real
* segue padrões do mercado
* menos boilerplate
* mais produtividade

---

# Ciclo de Adição de Nova Dependência

---

## 1) Como adicionar dependências a um projeto npm (regra geral)

Adicionar dependências é sempre feito via **npm**, nunca copiando arquivos manualmente.

Comando base:

```bash
npm install nome-do-pacote
```

O que esse comando faz:

* baixa a biblioteca
* adiciona no `package.json`
* atualiza o `package-lock.json`
* coloca o código em `node_modules`

Se for uma dependência usada pela aplicação:

```bash
npm install axios
```

Se for uma ferramenta de desenvolvimento:

```bash
npm install -D eslint
```

Nunca edite dependências “na mão” no `package.json` esperando que o projeto funcione.

---

## 2) Cenário 1: dev server rodando (`npm run dev`)

Você **não roda npm install com o servidor ativo**.

Fluxo correto:

1. pare o servidor (`Ctrl + C`)
2. instale a dependência
3. suba o servidor novamente

Exemplo:

```bash
Ctrl + C
npm install axios
npm run dev
```

Por quê?

* o Vite precisa reiniciar para reconhecer novas dependências
* evitar estados inconsistentes na bundle

Algumas ferramentas até detectam, mas **não confie nisso**.

---

## 3) Cenário 2: ainda não rodei o primeiro `npm install`

Esse cenário é comum e importante.

Você tem o `package.json`, mas ainda não instalou nada.

### Opção recomendada

Simplesmente:

```bash
npm install
```

O npm vai:

* ler o `package.json`
* baixar todas as dependências listadas
* gerar o `package-lock.json`
* preencher `node_modules`

Se você já sabe de dependências extras, **você não adiciona antes**.

Regra profissional:

> o `package.json` é fonte da verdade, o `npm install` executa o plano

---

## 4) Posso declarar dependências antes do npm install?

Tecnicamente, sim, mas **não é recomendado**.

Você até poderia editar manualmente:

```json
"dependencies": {
  "react": "^18.2.0",
  "axios": "^1.6.0"
}
```

E depois rodar:

```bash
npm install
```

Mas isso:

* quebra o fluxo padrão
* aumenta risco de erro de versão
* não gera histórico correto no lock

Use sempre o comando, não o teclado.

---

## 5) Resumo mental rápido

* adicionar dependência: `npm install pacote`
* devDependency: `npm install -D pacote`
* nunca editar `node_modules`
* pare o dev server antes de instalar
* rode `npm install` só depois de criar o projeto
* o npm é quem mantém tudo consistente

---

