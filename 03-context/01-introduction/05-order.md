# Aninhamento de Providers

---

## 1. Quando é necessário aninhar Providers

Você aninha providers quando **contextos diferentes**:

* representam **domínios distintos**
* têm **escopos de vida diferentes**
* ou **dependem uns dos outros**

Em outras palavras:
quando um componente precisa **de mais de uma fonte de verdade**, cada uma responsável por um aspecto diferente do sistema.

### Exemplos típicos

* `AuthContext` → identidade do usuário
* `ThemeContext` → aparência
* `CartContext` → estado de compra
* `DashboardContext` → estado específico da tela

Esses estados:

* não devem se misturar
* não devem viver no mesmo Context
* não têm o mesmo ciclo de vida

Logo: providers separados, aninhados.

---

## 2. O caso do Dashboard (seu exemplo)

Você disse que o `Dashboard` precisa de:

* `UserContext`
* `ThemeContext`
* `CartContext`

Isso é **absolutamente legítimo**.

Mas repare no detalhe importante:

> o Dashboard **não cria esses estados**, ele apenas define **o escopo em que eles são válidos**

Exemplo:

```jsx
<UserProvider>
  <ThemeProvider>
    <CartProvider>
      <Dashboard />
    </CartProvider>
  </ThemeProvider>
</UserProvider>
```

Isso significa:

* fora do Dashboard, o carrinho pode nem existir
* o tema pode ser específico daquela área
* o usuário já está autenticado

Esse aninhamento **define fronteiras de domínio**.

---

## 3. Quando NÃO é necessário aninhar Providers

Você **não** deve aninhar quando:

* os contexts são totalmente independentes
* e todos devem existir para a aplicação inteira

Nesse caso, o lugar correto é o topo:

```jsx
<AppProviders>
  <App />
</AppProviders>
```

Onde `AppProviders` só organiza:

```jsx
function AppProviders({ children }) {
  return (
    <AuthProvider>
      <ThemeProvider>
        <CartProvider>
          {children}
        </CartProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}
```

Isso evita poluir cada rota com providers repetidos.

---

## 4. A ordem de aninhamento IMPORTA?

Resposta curta: **sim, às vezes**.
Resposta correta: **importa quando há dependência entre contexts**.

---

### Caso 1 — contexts independentes

Se nenhum Context consome outro:

```jsx
<ThemeProvider>
  <CartProvider>
    <Dashboard />
  </CartProvider>
</ThemeProvider>
```

ou

```jsx
<CartProvider>
  <ThemeProvider>
    <Dashboard />
  </ThemeProvider>
</CartProvider>
```

Não muda nada.

A ordem é irrelevante.

---

### Caso 2 — um Context depende de outro (comum e perigoso)

Exemplo realista:

* `CartContext` precisa do usuário logado
* ele consome `useUser()`

Então isso **é obrigatório**:

```jsx
<UserProvider>
  <CartProvider>
    <Dashboard />
  </CartProvider>
</UserProvider>
```

E isso **quebra**:

```jsx
<CartProvider>
  <UserProvider>
    <Dashboard />
  </UserProvider>
</CartProvider>
```

Por quê?

Porque:

* `CartProvider` tenta acessar `UserContext`
* mas ele ainda não existe na árvore

Resultado:

* erro
* ou comportamento inconsistente

---

## 5. Regra de ouro para ordem de Providers

> Um Provider que **consome outro Context** deve estar **dentro** dele.

Ou dito de outra forma:

* dependências **ficam fora**
* dependentes **ficam dentro**

Isso é exatamente o mesmo raciocínio de DI no back-end.

---

## 6. Anti-pattern clássico: Provider dependente escondido

Erro comum:

```jsx
function CartProvider({ children }) {
  const { user } = useUser(); // depende de UserContext
  ...
}
```

Mas alguém usa assim:

```jsx
<CartProvider>
  <Dashboard />
</CartProvider>
```

Sem `UserProvider` acima.

Isso é bug arquitetural.

A solução correta:

* documentar dependências
* ou compor providers explicitamente

---

## 7. Boa prática: Providers por camada da aplicação

Arquitetura limpa costuma separar:

### Providers globais (app inteiro)

* Auth
* Theme
* Feature flags

### Providers por área/rota

* Dashboard
* Admin
* Checkout

### Providers ultra locais

* Modals complexos
* Wizards
* Formulários grandes

Você aninha conforme **escopo e dependência**, não por acaso.

---

## 8. Como eu organizaria o caso do Dashboard

Minha escolha opinativa:

```jsx
<AuthProvider>
  <ThemeProvider>
    <DashboardProviders>
      <Dashboard />
    </DashboardProviders>
  </ThemeProvider>
</AuthProvider>
```

Onde:

```jsx
function DashboardProviders({ children }) {
  return (
    <CartProvider>
      {children}
    </CartProvider>
  );
}
```

Isso:

* deixa dependências explícitas
* evita árvores ilegíveis
* escala bem

---

Você aninha Providers quando:

* estados são distintos
* escopos são diferentes
* ou há dependência entre eles

A ordem:

* **não importa** se forem independentes
* **importa muito** se houver dependência

Se você trata Providers como **fronteiras de domínio**, o design se organiza sozinho.