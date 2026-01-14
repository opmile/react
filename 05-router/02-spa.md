# SPA - *Single Page Application*

---

## 1. O que *define* uma SPA (Single Page Application)

Uma SPA não é definida por framework.
Ela é definida por **comportamento**.

Uma aplicação é uma SPA quando:

* O navegador carrega **um único HTML inicial**
* A partir daí, **nenhuma navegação recarrega a página**
* As mudanças de tela acontecem via **JavaScript**
* O backend passa a fornecer **dados**, não páginas

O nome “single page” engana um pouco.
Não significa *uma tela*, significa *uma sessão contínua*.

Você pode ter dezenas de “páginas” visuais — mas tecnicamente, continua na mesma página.

---

## 2. Comparação direta: site tradicional vs SPA

### Site tradicional (MPA)

* Cada URL → requisição ao servidor
* Servidor retorna HTML completo
* Página inteira recarrega
* Estado do frontend é descartado

### SPA

* Primeira requisição retorna HTML + JS
* O JS controla tudo depois disso
* URLs mudam sem reload
* Estado permanece vivo na memória

O ponto-chave:

> Em SPA, o **servidor não controla a navegação**, o frontend controla.

---

## 3. Por que o React se encaixa *perfeitamente* nisso

React foi pensado para resolver um problema muito específico:

> “Como atualizar a interface quando o estado muda?”

Ele não nasceu para páginas, rotas ou navegação.
Ele nasceu para **UI reativa baseada em estado**.

E uma SPA é, essencialmente:

* Estado → Interface
* Mudança de estado → Re-render

A navegação em SPA vira só mais um estado:

* estado atual = “estou na rota X”
* React renderiza o componente correspondente

---

## 4. Por que o React **não força** SPA, mas incentiva

Importante:
React **não obriga** você a criar uma SPA.
Você pode usar React em:

* páginas isoladas
* múltiplos pontos de entrada
* sistemas híbridos

Mas ele incentiva SPA porque:

* O modelo de componentes funciona melhor com estado persistente
* Re-renderizações parciais são baratas
* A UX fica muito mais fluida

Em outras palavras:

> React brilha quando a aplicação não morre a cada clique.

---

## 5. A grande virada: o frontend vira uma aplicação de verdade

Antes das SPAs:

* Frontend = camada de apresentação
* Backend = dono da lógica
---

### Por que o papel do backend muda completamente

Antes das SPAs, o backend era **diretor da aplicação**:

* decidia qual página existia
* controlava o fluxo de navegação
* montava a UI no servidor
* o frontend só exibía

Com SPA + React, isso vira do avesso.

O backend passa a:

* **expor dados e operações**
* responder em JSON
* ser agnóstico à interface
* não “saber” mais como o usuário navega

Quem decide **o que é uma tela**, **quando ela muda** e **qual fluxo o usuário segue** é o frontend.

> o backend vira um **serviço**, não mais a aplicação.

---

### Por que isso explode a complexidade do front-end

Porque o front-end deixa de ser apresentação e vira **software de verdade**.

Agora o front-end precisa lidar com:

* estado persistente
* navegação
* permissões
* fluxos de usuário
* erros
* sincronização com APIs
* consistência visual

Tudo isso **antes** era resolvido “de graça” pelo reload de página.

---

* SPA empurra a responsabilidade da aplicação para o front-end
* Backend vira fornecedor de dados, não mais orquestrador de páginas
* Front-end moderno exige pensamento arquitetural
* React Router existe porque alguém precisa governar o caos da navegação

---

## 6. O custo escondido das SPAs

Nem tudo são flores, e isso é importante desde já:

* SEO precisa de cuidado
* Primeiro carregamento é mais pesado
* Arquitetura mal feita vira um caos
* Navegação vira responsabilidade sua (React Router)

Mas a troca é clara:

> Mais responsabilidade, mais poder.

---

* SPA = uma sessão contínua controlada por JS
* React = motor de atualização de interface baseado em estado
* Juntos, eles transformam navegação em mudança de estado
* React Router existe porque alguém precisa traduzir URL → componente

---

## O que acontece no primeiro carregamento de uma SPA

No **primeiro acesso**, a SPA age quase como um site tradicional — mas só uma vez.

O navegador:

1. Faz uma requisição HTTP para o servidor
2. Recebe um **HTML mínimo** (geralmente quase vazio)
3. Esse HTML referencia:

   * um ou mais arquivos JavaScript (bundle)
   * CSS
4. O navegador baixa esses arquivos
5. O JavaScript é executado
6. O React “nasce” dentro daquele HTML
7. A aplicação monta a interface inicial **em memória**

A partir desse momento, o navegador não pede mais páginas, apenas **dados**.

Esse instante inicial é o “custo de entrada” da SPA.

> depois que o React está carregado, **a aplicação já está viva**.

Isso quer dizer que:

* componentes estão na memória
* estados existem
* listeners estão ativos
* o histórico de navegação está sob controle do JS

Nada disso sobrevive a um reload completo.

Um reload mata tudo e começa do zero.

---

## Onde entra o Virtual DOM

O Virtual DOM é um **modelo em memória** da interface.

Em vez de o React:

* mexer direto no DOM real a cada mudança

Ele:

* atualiza o Virtual DOM
* compara com a versão anterior (diff)
* aplica **o mínimo de alterações necessárias** no DOM real

Resultado:

* menos operações caras
* atualizações previsíveis
* UI consistente com o estado

---

## Por que um reload completo é desperdício

Porque ele ignora tudo isso.

Ao recarregar a página:

* o estado é perdido
* o JS é reexecutado
* o React precisa reconstruir tudo
* o DOM inteiro é recriado

É como demolir um prédio só para trocar uma lâmpada.

Em uma SPA, trocar de tela:

* não muda a aplicação
* muda **parte do estado**
* muda **parte da UI**

O reload joga fora exatamente o que torna a SPA eficiente.

---

* O primeiro carregamento inicializa a aplicação
* O Virtual DOM torna atualizações parciais baratas
* Navegação vira mudança de estado
* Reload completo destrói esse modelo
