# Fonte de Verdade Única 
*Single Source of Truth - SSOT*

---

É o princípio de design de software que garante que cada parte mutável dos dados em uma aplicação tenha um único local autorizado de armazenamento e gerenciamento.

* **Centralização de Dados:** Em vez de duplicar o mesmo dado em vários componentes ou locais, ele é armazenado em um único ponto central. Isso elimina inconsistências e a necessidade de reconciliar versões conflitantes dos mesmos dados.

* **Fluxo de Dados Unidirecional:** O padrão SSOT é frequentemente usado com o fluxo de dados unidirecional do React (Unidirectional Data Flow). O estado flui em uma direção (de componentes pai para componentes filho via props), e as ações ou eventos para modificar o estado fluem na direção oposta, de volta para a fonte de verdade.

---

Perfeito — essa confusão é **normal**, porque “única fonte de verdade” soa abstrato demais até alguém **desmontar o conceito no chão**. Vou fazer isso agora, com calma, exemplos concretos e sem jargão vazio.

---

## 1. Começando do zero: o que é “verdade” num programa?

Em um programa, **verdade** é simplesmente:

> Onde está a informação que o sistema considera correta.

Exemplos de “verdades”:

* O modal está aberto ou fechado
* A tarefa está completa ou não
* O checkbox está marcado ou não
* O usuário está logado ou não

Essas coisas **não são visuais**, são **fatos** do sistema.

---

## 2. O problema começa quando a mesma informação existe em mais de um lugar

Vamos pegar algo simples: um checkbox.

### Situação ruim (duas verdades)

* O checkbox está marcado no HTML
* A tarefa tem `completed = false` no estado

Pergunta importante:

> Qual deles está certo?

Não dá pra saber.
O sistema está **em contradição interna**.

Isso é exatamente o tipo de bug que enlouquece devs.

---

## 3. Então o que é “única fonte de verdade”?

É uma regra simples:

> **Para cada informação importante, existe UM lugar oficial onde ela vive.**

Esse lugar:

* decide
* manda
* os outros apenas refletem

---

## 4. Vamos usar o exemplo do checkbox (bem concreto)

### ❌ Sem fonte única de verdade

```jsx
<input type="checkbox" />
```

Aqui:

* o browser decide se está marcado
* o React não sabe
* o estado não representa isso

O checkbox tem vida própria.

---

### ✅ Com fonte única de verdade

```jsx
<input
  type="checkbox"
  checked={task.completed}
  onChange={toggleTask}
/>
```

Agora:

* `task.completed` é a verdade
* o checkbox apenas **mostra** essa verdade
* se `task.completed` mudar, o checkbox muda
* se o usuário clicar, você muda `task.completed`

👉 **Uma única verdade: `task.completed`**

---

## 5. Por que isso é tão importante?

Porque sistemas **crescem**.

Hoje:

* só um checkbox

Amanhã:

* duas listas
* contador de tarefas
* filtro
* persistência
* backend
* animações

Se a verdade estiver espalhada:

* tudo quebra
* nada sincroniza
* você perde controle

---

## 6. Exemplo das tarefas completas e incompletas

Aqui está o ponto onde todo mundo se perde.

### O erro comum

Pensar assim:

> “Tenho duas listas: completas e incompletas”

Isso cria **duas verdades**.

---

### A forma correta de pensar

Existe **uma lista**:

```js
tasks = [
  { id: 1, text: "Estudar", completed: false },
  { id: 2, text: "Correr", completed: true }
]
```

A verdade é:

* a tarefa
* com a propriedade `completed`

As seções são **apenas visões diferentes** dessa verdade.

---

## 7. A metáfora que geralmente destrava tudo

Imagine um prédio.

* A planta do prédio é a verdade
* As maquetes são representações

Você pode ter:

* maquete da fachada
* maquete do interior
* maquete elétrica

Mas **a planta é uma só**.

Se cada maquete inventar sua própria medida, o prédio cai.

---

## 8. Em React, onde essa “verdade” costuma viver?

Normalmente:

* no `useState`
* no componente mais alto que precisa dessa informação

Exemplo:

```jsx
const [tasks, setTasks] = useState([]);
```

Isso é a fonte da verdade.

Tudo abaixo:

* recebe `tasks`
* reage a mudanças
* não decide fatos globais

---

## 9. Por que React insiste tanto nisso?

Porque React funciona assim:

> **UI = função do estado**

Se a UI tem sua própria verdade:

* ela não é mais função do estado
* ela vira um sistema paralelo

React perde o controle.

---

## 10. O que acontece quando você quebra esse princípio

Você começa a escrever coisas como:

* “quando marcar aqui, também muda ali”
* “preciso sincronizar isso com aquilo”
* “isso só funciona se clicar nessa ordem”

Isso é sinal claro de **verdades múltiplas**.

---

> **Nunca deixe duas coisas decidirem o mesmo fato.**

Se algo pode estar:

* aberto ou fechado
* ativo ou inativo
* marcado ou desmarcado

Escolha **um dono** dessa informação.

---
