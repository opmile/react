# Importando e Usando Tailwind

---

## **1. Instalar dependências**

```bash
npm install -D tailwindcss @tailwindcss/vite
```

* **`-D` instala como dependência de desenvolvimento**, ou seja: a lib é necessária **no build**, não em produção.

**Build** = processo de transformar seu código fonte em algo otimizado para o navegador (minificado, empacotado, removendo classes não usadas, etc.).

**Produção** = o resultado final que o usuário recebe no navegador (arquivos prontos, leves e estáticos).

**Por que o Tailwind está no build?**
Porque o Tailwind **gera o CSS final durante a compilação**, removendo classes não utilizadas e montando apenas o necessário. Ele não é usado pelo navegador — só pelo processo que cria o CSS final.


---

## **2. Configurar o Vite com o plugin do Tailwind**

Abra seu **vite.config.js** (ou .ts) e adicione o plugin:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    react(),
    tailwindcss(),
  ],
})
```

Isso integra o Tailwind diretamente no processo de build do Vite.

---

## **3. Importar Tailwind no CSS**

No seu arquivo de CSS global — normalmente **`src/index.css`** — coloque:

```css
@import "tailwindcss";
```

O **`src/index.css`** é o **arquivo global de estilos do projeto**, onde o Vite/React carrega na aplicação inteira.

### Por que é o `index.css`?

Porque:

* Ele é importado no `main.jsx` logo no início da aplicação.
* Ele funciona como “base global” do seu CSS.
* O Tailwind precisa ser inicializado globalmente para gerar o CSS utilitário.

Seu `main.jsx` deve estar assim:

```jsx
import './index.css'
import App from './App'
import React from 'react'
import ReactDOM from 'react-dom/client'

ReactDOM.createRoot(document.getElementById('root')).render(<App />)
```

Ou seja: o `index.css` é garantido de ser carregado antes de qualquer componente.

---

## **4. Importar esse CSS no React**

No **`main.jsx`**, importe o CSS:

```jsx
import './index.css'
import App from './App.jsx'
import React from 'react'
import ReactDOM from 'react-dom/client'

ReactDOM.createRoot(document.getElementById('root')).render(<App />)
```

Assim o Tailwind entra globalmente na sua aplicação.

---

## **5. Componente principal: `src/App.jsx`**

```jsx
export default function App() {
  return (
    <div className="h-screen flex items-center justify-center bg-slate-900">
      <h1 className="text-4xl font-bold text-white">
        Tailwind v4 + Vite + React funcionando!
      </h1>
    </div>
  )
}
```

---

## **6. Rodar o servidor de desenvolvimento**

No terminal:

```bash
npm run dev
```

E aí suas classes utilitárias do Tailwind começam a funcionar no React.