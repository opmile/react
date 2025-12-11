# `npm`

---

## O que é o npm (na prática)

**npm** significa *Node Package Manager*, mas o mais importante é entender o que ele faz, não o nome.

O npm é o sistema que:

* gerencia dependências do projeto
* baixa bibliotecas de terceiros
* resolve versões compatíveis
* define como o projeto é executado

Frontend moderno depende disso. React, Axios, Vite, ESLint, tudo passa pelo npm.

---

## O que acontece quando você roda `npm install`

Ao executar:

```bash
npm install axios
```

O npm:

* busca o pacote no registry
* baixa o axios e suas dependências
* cria ou atualiza a pasta `node_modules`
* registra a dependência no `package.json`
* trava versões no `package-lock.json`

Você não trabalha com arquivos soltos, mas com pacotes versionados.

---

## O que é o `package.json`

O `package.json` é o arquivo central do projeto. Ele descreve o projeto e como ele funciona.

Ele define:

* nome e versão do projeto
* dependências
* scripts de execução
* tipo de módulo

Exemplo mínimo:

```json
{
  "name": "meu-projeto",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  },
  "dependencies": {
    "axios": "^1.6.0"
  }
}
```

Isso não é código executável, é configuração.

---

## Dependências vs devDependencies

**dependencies**
São bibliotecas usadas quando a aplicação roda. Exemplo: React, Axios.

**devDependencies**
São ferramentas usadas apenas no desenvolvimento. Exemplo: Vite, ESLint, Prettier.

Regra simples:

* roda no navegador ou na aplicação final: `dependencies`
* só ajuda o desenvolvedor: `devDependencies`

---

## Scripts npm

Dentro do `package.json`, os scripts definem como o projeto é executado.

```json
"scripts": {
  "dev": "vite",
  "build": "vite build"
}
```

Você executa com:

```bash
npm run dev
npm run build
```

O npm automaticamente encontra os executáveis dentro de `node_modules/.bin`.

---

## `node_modules` e versionamento

A pasta `node_modules` contém todo o código baixado.

Ela não deve ser versionada no Git, porque:

* é muito grande
* pode ser recriada com `npm install`

`node_modules` deve ficar no `.gitignore`. Isso **não vem automaticamente por padrão**.

* Ele é adicionado automaticamente apenas quando você cria o projeto com um **scaffolding** (Vite, Create React App, Next, etc.).

O que entra no repositório:

* `package.json`
* `package-lock.json`

Com isso, qualquer pessoa recria o ambiente local sem dor.

---

## O papel do `package-lock.json`

O `package-lock.json` registra:

* versões exatas das dependências
* árvore completa de pacotes
* consistência de build

Ele evita incompatibilidades entre ambientes diferentes. Não deve ser editado manualmente.

---

## Ligação com backend

No backend:

* Spring usa Maven ou Gradle para dependências

No frontend:

* Node usa npm

Ambos seguem a mesma ideia: dependências declaradas, ambiente reproduzível.

---

* npm gerencia pacotes e scripts
* package.json define o projeto
* node_modules contém dependências baixadas
* scripts padronizam execução
* package-lock.json garante previsibilidade

---

### Obs: Mais Sobre `devDependencies`

**Dev dependencies** são bibliotecas usadas **apenas durante o desenvolvimento**, não fazem parte do código que roda em produção. Elas existem para **aumentar qualidade, produtividade e padronização**, não para resolver regras de negócio da aplicação.

Exemplos clássicos:

* **ESLint**: analisa o código e aponta erros lógicos, más práticas e padrões inconsistentes antes mesmo de o código rodar.
* **Prettier**: formata código automaticamente, garantindo estilo consistente independentemente de quem escreveu.
* **Vite**: fornece servidor de desenvolvimento, hot reload e build.
* **Jest / Vitest**: executam testes automatizados.
* **TypeScript** (compiler): faz checagens de tipo em tempo de desenvolvimento.

Por que elas ficam em `devDependencies`:

* não são necessárias no navegador ou no servidor em produção
* só rodam na máquina do desenvolvedor ou no pipeline de CI
* reduzem bundle final e evitam dependências desnecessárias

Regra prática:
se a biblioteca **não é importada no código que roda para o usuário final**, ela é dev dependency.

Essas ferramentas não mudam o que sua aplicação faz, mudam **como você consegue escrever, manter e evoluir esse código**.
