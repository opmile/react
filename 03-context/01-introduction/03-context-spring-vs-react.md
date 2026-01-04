# IoC e DI do Spring vs Context API React

---

## 1. A ideia central que conecta os dois

Tanto **Context API (React)** quanto **IoC + DI (Spring)** existem para resolver o **mesmo problema estrutural**:

> Como desacoplar quem **usa** uma dependência de quem **cria** essa dependência?

Essa pergunta é o coração dos dois mundos.

O que muda é:

* **escala**
* **tempo de vida**
* **modelo de execução**

---

## 2. Context API ≈ Injeção de Dependência *manual e explícita*

No React com Context, você faz algo como:

```jsx
<TodoProvider>
  <TodoList />
</TodoProvider>
```

Isso é conceitualmente equivalente a:

```java
@Component
public class TodoList {
  private final TodoService todoService;

  public TodoList(TodoService todoService) {
    this.todoService = todoService;
  }
}
```

Nos dois casos:

* `TodoList` **não cria** a dependência
* ela apenas **declara que precisa**
* alguém acima fornece

Isso já é **Inversão de Controle**.

---

## 3. Onde exatamente está a Inversão de Controle

### No Spring

* Você **não chama** `new TodoService()`
* O container decide:

  * quando criar
  * quanto tempo vive
  * como injetar

Você perde controle **intencionalmente** para ganhar:

* desacoplamento
* testabilidade
* consistência

---

### No React com Context

* O componente **não cria estado global**
* Ele apenas consome via `useContext`
* Quem controla o ciclo de vida é o Provider

O controle foi invertido:

* o componente deixa de ser responsável pela criação
* passa a ser apenas consumidor

Isso é IoC em estado puro.

---

## 4. Provider ≈ Container (com ressalvas importantes)

Aqui está o paralelo mais interessante:

| Spring                      | React                     |
| --------------------------- | ------------------------- |
| ApplicationContext          | Provider                  |
| Bean                        | value do Provider         |
| @Autowired                  | useContext                |
| Escopo (singleton, request) | Escopo da árvore          |
| Configuração                | Composição de componentes |

Mas atenção:
**o Provider não é um container completo**.

Ele:

* não resolve dependências automaticamente
* não faz scanning
* não gerencia ciclos
* não cria instâncias sob demanda

Ele só **expõe algo já criado**.

---

## 5. Diferença crucial: tempo de vida

### Spring

* beans podem ser:

  * singleton
  * request-scoped
  * session-scoped
* vivem no servidor
* existem fora da UI

### React Context

* vive enquanto o Provider está montado
* o escopo é **estrutural**, não conceitual
* desmontou a árvore → morreu o estado

Isso muda completamente a forma de pensar arquitetura.

---

## 6. DI no Spring é declarativa

Context no React é composicional

No Spring:

```java
@Service
public class TodoService {}
```

Você **declara intenção**.

No React:

```jsx
<TodoProvider>
  <TodoList />
</TodoProvider>
```

Você **compõe comportamento**.

Essa diferença é enorme.

React não tem container mágico.
Tudo é explícito.
Tudo é visível na árvore.

Na minha opinião, isso é até mais honesto.

---

## 7. Custom Hook ≈ Interface

```jsx
function useTodos() { ... }
```

Isso funciona como:

* uma interface
* um contrato de acesso

Os componentes:

* não sabem se isso vem de Context
* não sabem se amanhã vem de API
* não sabem se vem de Zustand

Isso é o mesmo princípio de programar contra abstrações.

---

## 8. Onde o paralelo quebra (importante)

Agora, honestidade intelectual.

### Context API **não** faz:

* auto-wiring
* lazy injection
* resolução de dependências complexas
* interceptors
* proxies
* AOP
* lifecycle hooks ricos

Spring é um **container de infraestrutura**.
Context é um **mecanismo de compartilhamento reativo**.

Comparar os dois como “iguais” é erro.

---

## 9. A analogia correta

A analogia mais precisa é:

> Context API é para React o que **injeção manual bem arquitetada** é para Java **antes** do Spring.

Você decide:

* onde nasce
* quem recebe
* quanto tempo vive

Spring automatiza isso.
React te obriga a pensar.

E isso molda desenvolvedores melhores, na minha opinião.

---

* Provider define **fronteira de domínio**
* Hook define **contrato**
* Componente é **consumidor burro**
* Estado vive fora da UI

---
