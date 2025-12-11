# Formulários HTML

---

**Formulários HTML** são a forma padrão de coletar dados do usuário e enviá-los para algum backend. Tudo começa com a tag `<form>`, que **encapsula os campos** e define **como** e **para onde** os dados serão enviados.

### Elementos essenciais

* **`<form>`**
  Onde tudo vive.
  Atributos principais:

  * `action`: URL de destino.
  * `method`: GET ou POST.
  * `enctype`: tipo de codificação (ex: `multipart/form-data` para upload de arquivos).

* **`<input>`**
  Campo de entrada mais básico.
  Tipos comuns (`type`): `text`, `password`, `email`, `number`, `checkbox`, `radio`, `file`, `submit`.
  Atributos principais:

  * `name`: chave que o backend usa para ler o dado.
  * `value`: valor inicial.
  * `placeholder`: texto guia.
  * `required`: torna obrigatório.
  * `min`, `max`, `maxlength`, `pattern`: validação.

* **`<label>`**
  Texto associado ao input. Melhora acessibilidade.
  Atributo principal: `for` (ligando ao `id` do input).

* **`<textarea>`**
  Campo de texto longo.
  Atributos: `rows`, `cols`, `placeholder`, `name`.

* **`<select>` e `<option>`**
  Listas de seleção.
  Atributos:

  * `multiple` (para selecionar mais de um),
  * `value` dentro de `<option>`.

* **`<button>`**
  Pode enviar o formulário ou apenas acionar algo.
  Atributo: `type` (`submit`, `reset`, `button`).

### Como tudo se junta

O formulário coleta valores de campos que possuem **atributo `name`**, empacota e envia conforme o `method` e o `action`. Sem `name`, o backend nem vê o dado. E validações básicas podem ser feitas direto nos atributos HTML, sem JavaScript.

---

Tá, vou montar um exemplo bem completo, mas ainda limpo o suficiente pra você enxergar claramente cada atributo importante em ação. Algo que parece “form real de aplicação”, mas sem firula.

```html
<form 
  action="/enviar-dados" 
  method="POST" 
  enctype="multipart/form-data"
  autocomplete="on"
  target="_self"
>
  <!-- Nome -->
  <label for="nome">Nome completo:</label>
  <input 
    type="text" 
    id="nome" 
    name="nome" 
    placeholder="Seu nome"
    required 
    minlength="3"
    maxlength="50"
  >

  <!-- Email -->
  <label for="email">Email:</label>
  <input 
    type="email" 
    id="email" 
    name="email" 
    placeholder="seuemail@exemplo.com"
    required
  >

  <!-- Senha -->
  <label for="senha">Senha:</label>
  <input 
    type="password" 
    id="senha" 
    name="senha" 
    required
    minlength="6"
  >

  <!-- Idade -->
  <label for="idade">Idade:</label>
  <input 
    type="number" 
    id="idade" 
    name="idade"
    min="1"
    max="120"
  >

  <!-- Data de nascimento -->
  <label for="nascimento">Data de nascimento:</label>
  <input 
    type="date" 
    id="nascimento" 
    name="nascimento"
  >

  <!-- Sexo (radio) -->
  <p>Sexo:</p>
  <label>
    <input type="radio" name="sexo" value="feminino" required>
    Feminino
  </label>
  <label>
    <input type="radio" name="sexo" value="masculino">
    Masculino
  </label>
  <label>
    <input type="radio" name="sexo" value="outro">
    Outro
  </label>

  <!-- Interesses (checkbox) -->
  <p>Interesses:</p>
  <label>
    <input type="checkbox" name="interesses" value="tecnologia">
    Tecnologia
  </label>
  <label>
    <input type="checkbox" name="interesses" value="esportes">
    Esportes
  </label>
  <label>
    <input type="checkbox" name="interesses" value="musica">
    Música
  </label>

  <!-- Estado (select) -->
  <label for="estado">Estado:</label>
  <select id="estado" name="estado" required>
    <option value="">Selecione...</option>
    <option value="sp">São Paulo</option>
    <option value="rj">Rio de Janeiro</option>
    <option value="mg">Minas Gerais</option>
  </select>

  <!-- Texto longo -->
  <label for="bio">Biografia:</label>
  <textarea 
    id="bio" 
    name="bio"
    rows="4" 
    cols="40"
    placeholder="Conte um pouco sobre você..."
  ></textarea>

  <!-- Upload de arquivo -->
  <label for="arquivo">Envie um arquivo:</label>
  <input 
    type="file" 
    id="arquivo" 
    name="arquivo"
    accept=".pdf,.png,.jpg"
  >

  <!-- Botões -->
  <button type="submit">Enviar</button>
  <button type="reset">Resetar</button>
</form>
```

---