# JavaScript Assíncrono

---

## 1. Por que JavaScript é assíncrono?

JavaScript é **single-threaded**. Uma única linha de execução. Nada de rodar duas coisas ao mesmo tempo na mesma thread.

Mas ao mesmo tempo ele precisa:

* fazer requisições HTTP
* acessar banco
* esperar timers
* ler arquivos

Se tudo isso fosse *bloqueante*, a aplicação congelaria.

A solução é simples e genial:
👉 **delegar tarefas demoradas** e continuar executando o resto do código.

É aqui que entra o modelo assíncrono.

---

## 2. Event Loop (a engrenagem central de tudo)

Opinião forte:

> Se você entende o event loop, o resto vira detalhe sintático.

### Componentes principais

* **Call Stack**
  Onde funções são empilhadas e executadas.

* **Web APIs / Node APIs**
  Onde tarefas assíncronas ficam aguardando (setTimeout, fetch, I/O).

* **Callback Queue (Task Queue)**
  Fila de callbacks “comuns”.

* **Microtask Queue**
  Fila **prioritária** (Promises).

* **Event Loop**
  O fiscal:

  > “A stack tá vazia? Então vou buscar algo da fila.”

### Regra de ouro

1. Executa tudo sincronamente
2. Quando a Call Stack fica vazia:

   * primeiro **Microtasks** (Promises)
   * depois **Tasks** (callbacks normais)

Isso explica comportamentos “estranhos” que parecem bugs, mas não são.

---

## 3. Callbacks: a primeira solução

### O que é callback?

Uma **função passada como argumento** para ser executada depois.

```js
setTimeout(() => {
  console.log("Executou depois");
}, 1000);
```

Simples, direto, funcional.

### Onde começam os problemas?

Quando você precisa encadear várias operações assíncronas:

```js
buscarUsuario(id, (usuario) => {
  buscarPedidos(usuario.id, (pedidos) => {
    buscarDetalhes(pedidos[0], (detalhes) => {
      console.log(detalhes);
    });
  });
});
```

Isso é o famoso **Callback Hell**.

### Limitações reais de callbacks

* Código **difícil de ler**
* Difícil de **tratar erros**
* Forte acoplamento
* Fluxo de execução pouco claro

Callback resolve o problema técnico, mas **quebra a legibilidade**.

---

## 4. Por que Promises existem

Promises não são “açúcar sintático”.
Elas **resolvem problemas estruturais**.

Uma Promise representa:

> “Um valor que ainda não existe, mas vai existir no futuro.”

Estados:

* `pending`
* `fulfilled`
* `rejected`

Exemplo básico:

```js
const promessa = new Promise((resolve, reject) => {
  resolve(42);
});
```

---

## 5. Promise chaining (encadeamento)

Aqui a leitura muda de patamar.

```js
buscarUsuario(id)
  .then(usuario => buscarPedidos(usuario.id))
  .then(pedidos => buscarDetalhes(pedidos[0]))
  .then(detalhes => {
    console.log(detalhes);
  })
  .catch(error => {
    console.error(error);
  });
```

### O que está acontecendo de verdade?

* Cada `.then()` retorna **outra Promise**
* O retorno vira a entrada do próximo `.then()`
* O fluxo fica **linear**
* Qualquer erro cai direto no `.catch()`

Isso é muito mais próximo de como pensamos.

---

## 6. Tratamento de erros com `.catch()`

Regra simples:

* Qualquer `throw`
* Qualquer `reject`

👉 desce direto pro `.catch()`

```js
.then(dado => {
  if (!dado) throw new Error("Inválido");
  return processar(dado);
})
.catch(err => {
  console.error(err.message);
});
```

Diferente de callbacks, aqui:

* erro não se perde
* erro não precisa ser tratado em mil lugares

---

## 7. Async / Await: leitura síncrona, execução assíncrona

* Usamos `assync` para escrever uma função assíncrona.

* Usamos `await` para chamar uma função assíncrona dentro de uma função também assíncrona.

```js
// promise chaining
buscarUsuario(idUsuario)
  .then(usuario => buscarPedidosPorUsuario(usuario.id))
  .then(pedidos => buscarDetalhesDoPedido(pedidos[0].id))
  .then(detalhes => console.log(detalhes))
  .catch(err => console.error(err));

// assync/await
async function fluxo(idUsuario) {
  try {
    // 1. Busca o usuário a partir de um id
    const usuario = await buscarUsuario(idUsuario);
    // usuario = { id: 10, nome: "Milena" }

    // 2. Usa o id do usuário para buscar os pedidos DELE
    const pedidos = await buscarPedidosPorUsuario(usuario.id);
    // pedidos = [{ id: 77, total: 120 }, { id: 81, total: 65 }]

    // 3. Usa o id de um pedido para buscar os detalhes desse pedido
    const detalhes = await buscarDetalhesDoPedido(pedidos[0].id);
    // detalhes = { itens: [...], endereco: "...", pagamento: "PIX" }

    console.log(detalhes);
  } catch (erro) {
    console.error(erro);
  }
}
```

### O que `await` faz?

* **pausa a execução da função**
* **não bloqueia** o event loop
* espera a Promise resolver ou rejeitar

Importante:

* `await` **só funciona** dentro de funções `async`

---

**`await` elimina a necessidade de promise chaining **dentro** da função**

Dentro de uma `async function`, você **não precisa** (nem deveria) usar:

```js
.then(...)
.catch(...)
```

Você escreve assim:

```js
async function fluxo() {
  const user = await buscarUsuario();
  const pedidos = await buscarPedidos(user.id);
  const detalhes = await buscarDetalhes(pedidos[0].id);

  return detalhes;
}
```

Isso **já é** promise chaining — só que reescrito de forma legível.

Regra prática:

> `await` é promise chaining com roupa de código síncrono.

---

### Promise chaining virou inútil? Não. Ele só mudou de lugar

Você usa **promise chaining fora do escopo da função async**, no ponto de consumo.

Exemplo clássico:

```js
async function fetchProducts() {
  const response = await fetch(url);
  if (!response.ok) throw new Error("Erro HTTP");
  return response.json();
}

// fora da função
fetchProducts()
  .then(products => {
    console.log(products[0].name);
  })
  .catch(err => {
    console.error(err);
  });
```

Aqui:

* a função usa `await`
* quem chama usa `.then()` / `.catch()`

Isso é **normal**, limpo e comum.

Outra alternativa: await também fora (se o contexto permitir). Se você estiver **em outro escopo async**:

```js
async function main() {
  try {
    const products = await fetchProducts();
    console.log(products[0].name);
  } catch (e) {
    console.error(e);
  }
}

main();
```

Agora não há promise chaining **em lugar nenhum**, só:

* `await`
* `try/catch`

Mas isso só é possível se o escopo permitir `async`.

Evite isso:

```js
async function errado() {
  return fetch(url)
    .then(res => res.json())
    .then(data => {
      return data;
    });
}
```

Tecnicamente funciona.
Arquiteturalmente é ruim porque:

* mistura dois estilos
* confunde leitura
* não ganha nada com isso

**Escolha um estilo por escopo.**

---

## 8. Onde tratar erros com async/await

Sempre com `try/catch`.

```js
try {
  await algoAssincrono();
} catch (e) {
  // trata aqui
}
```

Sem `try/catch`:

* o erro vira uma Promise rejeitada
* pode estourar fora do controle esperado

Em backend isso é crítico:

* exceção não tratada = request quebrada
* pode derrubar worker, log maluco, retry errado

`try/catch` dentro da função
→ transforma erro em resultado controlado

`throw` sem `try/catch`
→ erro vira rejeição da Promise

`.catch()` ou `try/catch` no consumo
→ lugar certo em código sério

em 90% dos casos, trate o erro fora da função async.

Tratar erros com `.catch()` **fora** da `async` function é mais saudável arquiteturalmente porque preserva a separação de responsabilidades: 

* a função assíncrona foca em **produzir um resultado ou falhar**.

* enquanto quem a consome decide o que fazer com a falha (logar, retry, fallback, resposta HTTP, UI). 

Isso mantém a função reutilizável, previsível e composável, evita que erros sejam “engolidos” ou logados duas vezes, e espelha o modelo clássico de exceções em backend (lança aqui, trata acima), o que escala muito melhor conforme a aplicação cresce.

---



