# Installation

While the `create-drift` CLI is the recommended way to start a new application, you can also install the DriftJS packages manually into an existing Vite-powered codebase.

---

## 📦 Installing Monorepo Packages

To manually integrate DriftJS, you need to install the browser DOM runtime and the compiler plugins as dependencies:

::: code-group

```bash [pnpm]
pnpm add driftjs-dom
pnpm add -D driftjs-compiler driftjs-vite-plugin
```

```bash [npm]
npm install driftjs-dom
npm install -D driftjs-compiler driftjs-vite-plugin
```

```bash [yarn]
yarn add driftjs-dom
yarn add -D driftjs-compiler driftjs-vite-plugin
```

```bash [bun]
bun add driftjs-dom
bun add -d driftjs-compiler driftjs-vite-plugin
```

:::

### Core Dependency Details
* **`driftjs-dom`**: Contains the register-based Virtual Machine runtime, the evaluation engine, and LIS reconciliation code. Required in the browser bundle.
* **`driftjs-compiler`**: Parses `.drift` templates into AST and emits VM bytecode. Required only during build/dev time.
* **`driftjs-vite-plugin`**: Connects the compiler to Vite, letting you import `.drift` single-file templates in your TypeScript/JavaScript files.

---

## ⚙️ Configuring Vite

To compile and load `.drift` files, you must register the `driftjs-vite-plugin` in your project's `vite.config.ts` or `vite.config.js`:

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import { driftPlugin } from 'driftjs-vite-plugin'

export default defineConfig({
  plugins: [
    driftPlugin()
  ]
})
```

---

## 📇 TypeScript Support (Declarations)

If you are using TypeScript, you need to tell the compiler how to handle imports of `.drift` single-file components. 

Create a file named `drift-env.d.ts` in your source directory (usually `src/`):

```typescript
// src/drift-env.d.ts
declare module '*.drift' {
  import { CompiledModule } from 'driftjs-dom'
  const component: CompiledModule
  export default component
}
```

Make sure this file is included in your `tsconfig.json`'s `include` array:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.drift"]
}
```

---

## 🎯 Mounting Your First Component

With everything configured, you can import and mount a `.drift` component.

Create a template file named `App.drift` in your `src` directory:

```html
<!-- src/App.drift -->
<script>
  let heading = "Hello from DriftJS!";
  let count = 0;

  function increment() {
    count = count + 1;
  }
</script>

<div class="hello-container">
  <h1>{heading}</h1>
  <button onclick={increment}>Clicks: {count}</button>
</div>
```

Then, import the template and mount it to the DOM from your application's entrypoint, `src/main.ts` or `src/main.js`:

```typescript
// src/main.ts
import { mount } from 'driftjs-dom'
import App from './App.drift'

const container = document.getElementById('app')

if (container) {
  // mount evaluates App's script block, creates the DOM nodes,
  // attaches event handlers, and appends the final output to the container.
  mount(App, container)
}
```

Ensure your `index.html` file contains a target container element and references your entry script:

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My DriftJS App</title>
</head>
<body>
  <div id="app"></div>
  <script type="module" src="/src/main.ts"></script>
</body>
</html>
```
