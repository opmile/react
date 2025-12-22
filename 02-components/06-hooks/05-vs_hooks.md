# `useState` x `useEffect` x `useRef`

---

Ótima pergunta — e aqui vai uma resposta **de arquitetura mental**, não de receita.
Quando você entende **como esses três se conectam**, o React deixa de parecer um amontoado de hooks e vira um sistema coerente.

Vou direto ao ponto, mas com profundidade.

---

## A ideia central (guarda isso)

Os três hooks existem para **resolver problemas diferentes** e **complementares**:

* **useState** → dados que afetam a renderização
* **useEffect** → reagir a mudanças e falar com o mundo externo
* **useRef** → guardar algo que **não deve causar render**

Eles se conectam porque uma aplicação real **precisa dos três tipos de comportamento ao mesmo tempo**.

---

## 1. useState: o que muda e redesenha a UI

`useState` responde à pergunta:

> “Isso faz parte da interface visível?”

Se a resposta for sim → estado.

Exemplos:

* usuário logado
* formulário preenchido
* item selecionado
* modal aberto

Características:

* quando muda → **renderiza novamente**
* é assíncrono
* é imutável (conceitualmente)

---

## 2. useEffect: reagir a mudanças de estado/props

`useEffect` responde à pergunta:

> “Quando isso mudar, preciso fazer algo fora do render?”

Ele **observa** mudanças de:

* estado
* props

E reage com:

* efeitos colaterais
* sincronizações externas
* inicializações

Importante:

* efeito **não controla render**
* ele roda **depois** do render

---

## 3. useRef: memória persistente sem render

`useRef` responde à pergunta:

> “Preciso guardar algo entre renders, mas isso não é UI?”

Exemplos:

* referência a um elemento do DOM
* id de um timer
* valor anterior de um estado
* flag interna
* integração com API imperativa

Características:

* `.current` pode mudar à vontade
* **não dispara render**
* persiste entre renders

---

## 4. Como eles se conectam de verdade

Agora o ponto-chave.

### Fluxo clássico de conexão

1. **useState** guarda algo que o usuário vê ou controla
2. **useEffect** observa esse estado
3. **useRef** guarda coisas auxiliares para o efeito funcionar

Eles não são equivalentes.
Eles são **camadas diferentes** do mesmo comportamento.

---

## 5. Exemplo mental (sem modal, sem DOM)

Pensa num contador que salva no `localStorage`.

* O número exibido → `useState`
* A escrita no `localStorage` → `useEffect`
* O valor anterior para comparação → `useRef`

Papéis bem definidos:

* estado → UI
* efeito → sincronização
* ref → memória auxiliar

---

## 6. useRef como “ponte” entre renders e efeitos

Um ponto avançado que faz muita diferença:

`useRef` é frequentemente usado **dentro de efeitos** para:

* evitar loops
* guardar valores antigos
* manter identidade estável

Exemplo conceitual:

* você quer saber se um estado mudou *de verdade*
* o efeito roda
* você compara com o valor salvo em um ref

Sem `useRef`, você teria:

* estados desnecessários
* renders extras
* código confuso

---

## 7. Erro comum: usar o hook errado para o problema errado

### ❌ Usar `useState` no lugar de `useRef`

* causa renders inúteis
* cria dependências falsas
* deixa a UI instável

### ❌ Usar `useEffect` para lógica pura

* complica código
* cria efeitos desnecessários
* mascara problemas de modelagem

### ❌ Usar `useRef` para dados de UI

* a tela não atualiza
* bugs silenciosos aparecem

---

Antes de escolher o hook, se faça **uma** dessas perguntas:

1. **Isso aparece na tela ou influencia o JSX?**
   → `useState`

2. **Isso precisa reagir a mudanças ou falar com algo externo?**
   → `useEffect`

3. **Isso é só uma referência ou memória interna?**
   → `useRef`

Se você responde “sim” para mais de uma pergunta, os hooks **vão se combinar**.

---

