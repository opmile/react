# Por que React Router não vem "embutido" no React?

---

O motivo principal: **React não é um framework**

React é uma **biblioteca de UI**, não um framework completo.

Ele resolve **uma coisa muito bem**:

> dado um estado, renderizar uma interface

Roteamento não é parte obrigatória desse problema.

Nem toda aplicação React:

* tem múltiplas telas
* usa URLs
* é uma SPA completa

Se o React viesse com roteamento embutido, ele estaria **impondo arquitetura**.

E essa sempre foi uma decisão consciente que o time do React evitou.

---

## Roteamento é um problema “de fora” da UI

Roteamento envolve:

* histórico do navegador
* URLs
* comportamento do botão voltar
* integração com ambiente (web, mobile, desktop)

Isso não é UI pura.
É **integração com a plataforma**.

O React prefere:

* expor primitives (componentes, estado, efeitos)
* deixar decisões estruturais para o ecossistema

Por isso o roteador vive fora do core.

---

## O ecossistema como escolha, não imposição

React foi desenhado para ser:

* pequeno
* flexível
* composável

Se o React tivesse um router oficial embutido:

* toda app teria que usá-lo
* evoluções ficariam acopladas ao core
* casos não-web ficariam estranhos

Separar React e React Router permite:

* evolução independente
* múltiplas abordagens de navegação
* adaptação a contextos diferentes

Esse modelo é intencional, não omissão.

---

## Comparação rápida com frameworks

Frameworks como Angular e Next:

* já assumem aplicação completa
* controlam navegação, build, estrutura
* tomam decisões por você

React não quer fazer isso.

Ele te dá as peças, não o manual de montagem.

---

* React resolve UI, não navegação
* Roteamento é uma escolha arquitetural
* Separar router do core mantém o React genérico
* React Router existe porque alguém precisa resolver esse problema no nível da aplicação

---

## Biblioteca x Framework

---

A diferença não é tamanho, nem popularidade, nem “opinião”.
É **quem manda no fluxo da aplicação**.

### Biblioteca

Você chama a biblioteca quando quer.
Ela é uma **ferramenta**.

### Framework

O framework chama o seu código.
Ele é a **estrutura**.

O ponto central da diferença é a Inversão de Controle (IoC): no framework, o código dele te chama; na biblioteca, seu código chama as funções dela.

---

## React como biblioteca

React é uma biblioteca porque:

* ele **não decide quando sua aplicação começa**
* ele **não define estrutura de pastas**
* ele **não controla navegação**
* ele **não dita como você busca dados**

Você escolhe:

* como montar o projeto
* quais libs usar (router, estado, fetch)
* quando e onde renderizar componentes

O React só entra quando você diz:

> “React, renderiza isso aqui”

Isso é comportamento clássico de biblioteca.

---

## Framework no mundo React (exemplo prático)

Pensa no Next.js.

Com Next:

* a estrutura de pastas já define rotas
* o ciclo de vida da aplicação é imposto
* build, renderização e deploy seguem regras
* você encaixa código **dentro** do sistema

Você não pergunta “como faço rota?”
A resposta já está decidida.

Isso é framework.

---

## Por que essa diferença importa na prática

Usar uma biblioteca te dá:

* liberdade
* flexibilidade
* mais decisões

Usar um framework te dá:

* velocidade inicial
* convenções
* menos escolhas

Nenhum é melhor por definição.
Eles servem a momentos diferentes.

React puro é ótimo para:

* aprender fundamentos
* entender arquitetura
* ter controle fino

Frameworks são ótimos quando:

* o projeto cresce
* o time aumenta
* decisões precisam ser padronizadas

---
