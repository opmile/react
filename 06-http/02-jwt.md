# Autorização de Requisições

---

## 1. Por que algumas requisições precisam de autorização?

Porque uma API real não serve só para “listar dados públicos”.
Ela geralmente lida com:

* dados pessoais
* ações críticas (criar, alterar, deletar)
* recursos que pertencem a usuários específicos

Logo, a API precisa responder a duas perguntas em cada requisição:

1. **Quem está chamando?** (autenticação)
2. **O que essa pessoa pode fazer?** (autorização)

Sem isso, qualquer cliente poderia:

* deletar registros
* acessar dados privados
* simular outro usuário

---

## 2. O pilar: REST é Stateless

Uma API REST bem projetada é **stateless**.

Isso significa:

> O servidor **não guarda sessão** entre uma requisição e outra.

Cada requisição deve carregar **todas as informações necessárias** para ser processada.

Diferente de aplicações antigas que guardavam:

* sessão em memória
* id do usuário no servidor

Em REST:

* o servidor não “lembra” de você
* ele só confia no que vem na requisição

Isso muda tudo.

---

## 3. Então como o servidor sabe quem é o usuário?

Ele precisa de um **token**.

Fluxo real:

1. Usuário faz login (POST /auth/login)
2. Backend valida credenciais
3. Backend devolve um token
4. Front guarda esse token
5. Front envia esse token em **todas as requisições protegidas**

Esse token é a “identidade portátil” do usuário.

---

## 4. Bearer Token (o padrão do mundo real)

No HTTP, isso é feito via header:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

“Bearer” significa literalmente:

> “Quem porta esse token, é tratado como o dono da identidade.”

Não há sessão.
Não há estado.
Há confiança criptográfica.

---

## 5. O que é JWT?

JWT = **JSON Web Token**

É um formato padrão de token, composto por três partes:

```
HEADER.PAYLOAD.SIGNATURE
```

Exemplo visual:

```
eyJhbGciOi...  .  eyJzdWIiOiIxMjM0...  .  SflKxwRJSMeKKF2...
```

### 5.1 Header

Contém:

* algoritmo de assinatura (HS256, RS256, etc)
* tipo do token

### 5.2 Payload

Contém os **claims** (dados do usuário):

* id
* username
* roles
* exp (expiração)

Importante:

> Isso NÃO é criptografado.
> É apenas codificado (Base64).

Qualquer um pode ler.
Ninguém pode alterar sem invalidar a assinatura.

### 5.3 Signature

Garante:

* integridade
* autenticidade

É isso que o servidor valida.

---

## 6. Por que JWT combina perfeitamente com REST?

Porque:

* é auto-contido (o token já carrega a identidade)
* dispensa sessão no servidor
* escala facilmente
* funciona bem com múltiplos serviços
* mantém a API realmente stateless

O backend só:

* valida a assinatura
* lê os claims
* decide se autoriza a ação

---

## 7. Quais requisições precisam de autorização?

Regra prática:

| Tipo de operação         | Precisa de token? |
| ------------------------ | ----------------- |
| Login / Registro         | NÃO               |
| Listar dados públicos    | NÃO               |
| Buscar dados privados    | SIM               |
| Criar recurso            | SIM               |
| Atualizar recurso        | SIM               |
| Deletar recurso          | SIM               |
| Ações do próprio usuário | SIM               |

Em REST:

* GET nem sempre é público
* POST nem sempre é privado
  Depende do **domínio**, não do método.

---

## 8. Fluxo completo Front ↔ API

1. Login:

```http
POST /auth/login
```

2. Resposta:

```json
{ "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
```

3. Front salva (memory / localStorage)

4. Requisição protegida:

```http
GET /api/tasks
Authorization: Bearer TOKEN
```

5. Spring valida JWT
6. Libera ou bloqueia (401 / 403)

---

## 9. Códigos HTTP envolvidos

* `401 Unauthorized` → token ausente ou inválido
* `403 Forbidden` → token válido, mas sem permissão
* `200 OK` → autorizado

Esses status são fundamentais para o front reagir corretamente.

---