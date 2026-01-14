# Modes React Router

---

## Os “modos” do React Router (visão conceitual)

Esses modos não são versões diferentes da lib, são **formas de usar o React Router**, com níveis crescentes de responsabilidade.

### 1. Declarative Mode (o clássico)

É o React Router como ele sempre foi entendido:

* você declara rotas
* cada rota aponta para um componente
* o componente decide quando e como buscar dados
* navegação é responsabilidade da UI

Aqui o router faz **uma coisa só**:

> mapear URL → componente

Todo o resto (fetch, loading, erro, auth) fica no React.

Esse modo combina perfeitamente com:

* SPAs clássicas
* backends separados (API REST em Java, por exemplo)
* controle total no front

---

### 2. Data Mode

Aqui o router sobe de nível.

Além de rotas, ele passa a:

* buscar dados antes da tela renderizar
* tratar erros de forma centralizada
* lidar com submissão de formulários
* coordenar loading states

O router vira um **orquestrador de fluxo de dados**, não só de navegação.

Isso começa a parecer um mini-framework dentro da lib.

É poderoso, mas:

* aumenta acoplamento
* exige mudar o jeito de pensar componentes
* faz mais sentido quando o router controla o app inteiro

---

### 3. Framework Mode

Esse modo aparece quando o React Router:

* assume renderização no servidor
* controla build e deploy
* define ciclo de vida da aplicação

Aqui ele não é mais “só uma lib”.
Ele vira base de framework (ex: Remix).

Você não escolhe quando usar — você **entra** nesse modo.

---

## Caso (React + Java)

### É melhor seguir **só com Declarative Mode**.

**É o mais recomendado**.

* backend Java (Spring, provavelmente)
* frontend React separado
* API clara entre os dois

O Data Mode:

* não te traz vantagem real
* duplica responsabilidades que já estão no front
* adiciona complexidade cedo demais

Declarative Mode te dá:

* clareza
* controle
* arquitetura limpa
* acoplamento mínimo com o backend

É exatamente o tipo de stack onde o React Router nasceu.

---

## Quando você *pensaria* em Data Mode

Só quando:

* o front começa a virar o centro da aplicação
* você quer padronizar fetch + erro + loading
* ou está migrando para algo estilo Remix

Antes disso, é ruído.

---

* Declarative Mode = roteamento puro (URL → componente)
* Data Mode = roteamento + dados + fluxo
* Framework Mode = aplicação inteira sob o router
* React + Java funciona perfeitamente sem Data Mode
* Usar só Declarative Mode não é limitação, é maturidade arquitetural

---