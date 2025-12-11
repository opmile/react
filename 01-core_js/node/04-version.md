# Versionamento de Pacotes 

---

## O que é versionamento (SemVer)

O `package.json` usa **Semantic Versioning (SemVer)**:

```
MAJOR.MINOR.PATCH
```

Exemplo:

```
2.4.1
```

* **MAJOR (2)** → mudou algo que **pode quebrar seu código**
  Atualizou? Talvez o jeito antigo de usar **não funcione mais**.

* **MINOR (4)** → adicionou coisas novas, mas **sem quebrar o que já existia**
  Seu código antigo continua funcionando.

* **PATCH (1)** → só consertou erros
  Nada muda no jeito de usar, só fica mais estável.

**MAJOR quebra, MINOR adiciona, PATCH corrige.**

Regra básica:

* MAJOR muda → cuidado
* MINOR muda → seguro na teoria
* PATCH muda → deveria ser totalmente seguro

---

## Versão fixa (sem símbolo)

```json
"axios": "1.6.2"
```

Significa:

* só essa versão
* nem mais nova, nem mais velha

Quando usar:

* ambientes críticos
* máxima previsibilidade

Downside:

* você não recebe correções automaticamente

---

## Caret `^` (mais comum)

```json
"react": "^18.2.0"
```

Significa:

* aceita atualizações **MINOR e PATCH**
* não aceita mudança de MAJOR

Equivale a:

```
>=18.2.0 <19.0.0
```

Por que é padrão:

* recebe melhorias
* evita breaking changes

Quase todo projeto usa isso.

---

## Tilde `~`

```json
"vite": "~5.0.1"
```

Significa:

* aceita só PATCH
* mantém MINOR travado

Equivale a:

```
>=5.0.1 <5.1.0
```

Uso típico:

* quando você quer estabilidade extra
* mas ainda aceita correções

---

## Maior ou igual / intervalos

```json
"axios": ">=1.6.0 <2.0.0"
```

Mais explícito, menos comum no dia a dia.

Usado quando:

* você quer controle fino
* projetos mais complexos

---

## `*` ou `latest` (quase nunca use)

```json
"lodash": "*"
```

Ou:

```json
"lodash": "latest"
```

Significa:

* qualquer versão
* inclusive breaking changes

Isso é pedir por bugs imprevisíveis. Evite.

---

## package-lock.json entra onde?

Muito importante.

* `package.json` → diz **o que é aceitável**
* `package-lock.json` → diz **o que foi instalado de fato**

Mesmo usando `^`, o lock garante:

* mesmas versões para todo mundo
* builds reproduzíveis

Nunca delete o lock sem motivo.

---

## Regra prática profissional

* `^` → padrão para maioria dos projetos
* `~` → se precisa de mais estabilidade
* versão fixa → sistemas sensíveis
* nunca `*` em projeto sério
