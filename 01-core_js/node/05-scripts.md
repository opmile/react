# Scripts

---

Vamos direto ao ponto, porque **scripts no `package.json` são peça-chave do fluxo de trabalho frontend**.

---

## O que são scripts no package.json

Scripts são **atalhos padrão de comandos** definidos no `package.json`.
Eles dizem **como o projeto é rodado, testado e buildado**, sem você precisar lembrar comandos longos ou dependentes do sistema.

Exemplo:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

---

## Como scripts funcionam

Quando você roda:

```bash
npm run dev
```

O npm faz três coisas:

1. procura o script `dev` no `package.json`
2. acha o comando `vite`
3. executa o `vite` **da versão instalada no projeto**, em `node_modules/.bin`

Isso evita:

* dependência global
* conflito de versões
* “na minha máquina funciona”

---

## Por que scripts são importantes

Scripts:

* padronizam comandos para todo o time
* escondem complexidade
* garantem mesma versão de ferramentas
* documentam como o projeto funciona

Quem olha os scripts entende rápido:

* como iniciar o projeto
* como gerar build
* como rodar testes

---

## Exemplos comuns de scripts

### Servidor de desenvolvimento

```json
"dev": "vite"
```

### Build de produção

```json
"build": "vite build"
```

### Testes

```json
"test": "vitest"
```

### Lint

```json
"lint": "eslint ."
```

---

## Scripts podem encadear comandos

```json
"scripts": {
  "build": "npm run lint && vite build"
}
```

Significa:

* roda lint
* se não falhar, gera o build

---

## Scripts com variáveis de ambiente

```json
"scripts": {
  "dev": "NODE_ENV=development vite"
}
```

O script controla o ambiente sem mexer no código.

---

## Quando usar scripts

Use scripts sempre que:

* o comando será usado mais de uma vez
* outra pessoa vai rodar o projeto
* a ferramenta é uma dependência local

Não use scripts para:

* comandos únicos e descartáveis

---