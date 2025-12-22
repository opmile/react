# `useEffect`

---

## 1. O problema que o `useEffect` existe para resolver

React tem uma regra fundamental:

> **Renderizar deve ser uma operação pura.**

Isso significa:

* o render **não pode** causar efeitos no mundo externo
* dado o mesmo estado e props, o JSX deve ser sempre o mesmo
* nada de:

  * mexer no DOM
  * fazer chamadas de rede
  * registrar eventos
  * alterar variáveis globais

Mas aplicações reais **precisam** fazer tudo isso.

O `useEffect` existe para **separar duas coisas**:

* **o que a UI é**
* **o que acontece por causa dela**

---

## 2. O que é um “efeito colateral” (sem buzzword)

Efeito colateral é **qualquer coisa que acontece fora do React**.

Exemplos reais:

* buscar dados de uma API
* salvar algo no `localStorage`
* mudar o título da página
* registrar um `addEventListener`
* iniciar um timer
* integrar com uma biblioteca externa

Nada disso é renderização.
Logo, nada disso pode acontecer diretamente no JSX.

---

## 3. Definição clara de `useEffect`

> `useEffect` é o hook que permite executar **efeitos colaterais** em resposta ao ciclo de vida do componente e às mudanças de estado ou props.

Ou em português direto:

> “Quando algo mudar, faça isso fora do render.”

---

## 4. Quando o `useEffect` roda

Essa parte é essencial.

Um `useEffect` pode rodar em três momentos diferentes, dependendo da **lista de dependências**.

---

### Caso 1: sem array de dependências

```js
useEffect(() => {
  console.log("rodei");
});
```

* roda **depois de toda renderização**
* toda mudança de estado dispara
* quase nunca é o que você quer

Uso raro e geralmente ruim.

---

### Caso 2: array vazio `[]`

```js
useEffect(() => {
  console.log("rodei uma vez");
}, []);
```

* roda **uma única vez**
* depois do primeiro render
* equivalente a “componentDidMount”

Usado para:

* carregar dados iniciais
* inicializar libs
* configurar listeners

---

### Caso 3: com dependências

```js
useEffect(() => {
  console.log("rodei porque algo mudou");
}, [count, user]);
```

* roda quando `count` ou `user` mudam
* não roda se nada mudar
* é o caso mais comum

---

## 5. O que são dependências, de verdade

As dependências dizem ao React:

> “Esse efeito depende dessas variáveis para estar correto.”

Se uma delas muda:

* o efeito pode estar “desatualizado”
* então o React roda de novo

Regra simples:

> **Tudo que você usa dentro do efeito e vem de fora, deveria estar nas dependências.**

---

## 6. O que é o cleanup (parte ignorada, mas crucial)

Um `useEffect` pode retornar uma função:

```js
useEffect(() => {
  console.log("efeito ativo");

  return () => {
    console.log("limpeza");
  };
}, []);
```

Essa função:

* roda antes do efeito ser reexecutado
* roda quando o componente desmonta
* serve para **desfazer efeitos**

Exemplos de uso:

* remover event listeners
* limpar timers
* cancelar subscriptions
* evitar vazamentos de memória

Sem cleanup, você cria bugs silenciosos.

---

## 7. O que `useEffect` NÃO deve fazer

Isso é tão importante quanto saber usar.

Não use `useEffect` para:

* calcular valores derivados
* sincronizar estado com estado
* reagir a eventos de clique
* substituir lógica de render

Exemplo errado:

```js
useEffect(() => {
  setTotal(price * quantity);
}, [price, quantity]);
```

Isso deveria ser:

```js
const total = price * quantity;
```

---

## 8. Relação entre `useEffect` e `useState`

Eles não competem.
Eles cooperam.

* `useState` guarda **dados**
* `useEffect` reage a mudanças nesses dados

Fluxo mental correto:

1. Estado muda
2. Componente renderiza
3. Efeitos rodam

Nunca:

* efeito decide estado sem necessidade
* estado existe só para disparar efeito

---

## 9. Por que `useEffect` existe e não é automático

Pergunta importante:

> “Por que o React não faz isso sozinho?”

Resposta:

* porque nem todo render precisa de efeito
* porque efeitos têm custo
* porque efeitos precisam de controle
* porque misturar render com efeito quebra previsibilidade

`useEffect` é uma **fronteira explícita** entre React e o mundo externo.

---

## 10. Modelo mental final (memoriza isso)

Pensa assim:

* Render → descreve a UI
* State → descreve dados
* Effect → sincroniza com o mundo externo

Ou numa frase:

> **`useEffect` é onde o React deixa você sair do universo dele e tocar o mundo real.**

---

## Conclusão

`useEffect`:

* não é um “gancho mágico”
* não é obrigatório
* não é substituto de lógica
* não é solução universal

Ele existe para **efeitos colaterais**, ponto final.

Quando você entende isso:

* seu código fica mais simples
* você usa menos `useEffect`
* seus componentes ficam previsíveis
* seus bugs diminuem drasticamente

Essa pergunta foi excelente.
Se quiser, o próximo passo natural é:

* padrões errados mais comuns com `useEffect`
* diferenças entre `useEffect`, `useLayoutEffect` e `useInsertionEffect`
* ou analisar código real e dizer onde `useEffect` está sendo mal usado
