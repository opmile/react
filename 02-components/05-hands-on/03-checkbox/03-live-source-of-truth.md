# Identificação de Onde a Verdade Deve Morar

---

## A regra-mãe (guarda isso)

> **A verdade deve morar no componente mais alto que precisa tomar decisões com base nela.**

Não é “o mais alto da árvore” sempre.
É o mais alto **que realmente usa aquela informação**.

---

## Checklist rápido para decidir onde o estado mora

Sempre que você estiver em dúvida, faça essas perguntas **na ordem**:

---

### 1️⃣ Mais de um componente precisa saber disso?

* Sim → **suba o estado**
* Não → pode ficar local

Exemplo:

* só o `Checkbox` precisa saber se está marcado → local
* lista, contador e filtro precisam saber → sobe

---

### 2️⃣ Alguém além de quem renderiza precisa mudar isso?

* Sim → **não pode ser local**
* Não → pode ser local

Exemplo:

* modal abre só pelo próprio botão → local ok
* modal abre por botão, erro, rota → sobe

---

### 3️⃣ Essa informação define comportamento ou só aparência?

* Define comportamento → **estado global/local elevado**
* Só aparência → estado local ou até CSS

Exemplo:

* “tarefa está completa” → comportamento
* “input está com foco” → aparência

---

### 4️⃣ Se esse valor mudar, quem precisa reagir?

Liste mentalmente.

* Se a resposta for “vários lugares” → sobe
* Se for “só aqui” → fica

---

### 5️⃣ Se eu esconder esse componente, a informação ainda importa?

* Sim → não pode morar nele
* Não → pode morar nele

Exemplo:

* usuário logado → importa mesmo sem UI
* dropdown aberto → só importa se visível

---

## Regra prática em forma de frase

> **Se eu destruir esse componente, perco uma informação importante?**

Se a resposta for **sim**, o estado está no lugar errado.

---

## Exemplos rápidos (bem concretos)

### Checkbox simples

* Só ele usa → local

### Tarefa completa

* Lista muda
* Seção muda
* Contador muda
  → sobe

---

### Modal local (tooltip, ajuda)

→ local

### Modal de login

* pode abrir por erro
* pode abrir por botão
* pode fechar por rota
  → sobe

---

### Input de formulário

* valor → local
* validação geral → sobe

---

## O erro mais comum (e mais caro)

Colocar estado em componentes “bonitos”:

* `Modal`
* `Card`
* `Button`

Eles **não devem decidir nada**.

São executores, não juízes.

---

## Um padrão mental poderoso

Pense assim:

* **Estado é autoridade**
* **Props são ordens**
* **Eventos são pedidos**

Quem recebe ordens não decide regras.

---

## Um mapa visual rápido

```
[ App / Page ]
      ↑
   decisões
      ↓
[ Componentes de UI ]
```

Estado sobe.
Eventos sobem.
Dados descem.

---

## Quando você pode quebrar essa regra (importante)

Você pode manter estado local quando:

* é puramente visual
* não afeta outros componentes
* não define comportamento global
* não precisa ser sincronizado

Exemplos:

* hover
* foco
* animação local
* toggle interno

---

## Sinal definitivo de que a verdade está mal colocada

Quando você pensa:

> “Preciso avisar outro componente que isso mudou”

Isso é cheiro de estado no lugar errado.

---

Identificar onde a verdade mora não é adivinhação.

É responder honestamente:

* quem usa
* quem decide
* quem depende
* quem sobrevive sem o outro

Quando você faz isso, a arquitetura **se organiza sozinha**.