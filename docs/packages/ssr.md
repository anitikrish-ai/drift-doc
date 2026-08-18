# driftjs-ssr

The `driftjs-ssr` package provides server-side rendering support, compiling component templates into static HTML strings on the server.

---

## 🔧 Core API Methods

### 1. `renderToString`
Executes a compiled module in a headless VM and returns the resulting static HTML string.

```typescript
import { renderToString } from 'driftjs-ssr'
import App from './App.drift' // Pre-compiled module object

// Runs bytecode on server and outputs HTML string
const html = renderToString(App);

console.log(html);
// Output: "<div class=\"app-container\"><h1>Hello Server!</h1></div>"
```

---

## 🏛️ How It Works (Headless VM)

Server-side environments (like Node.js, Deno, or Cloudflare Workers) do not have access to the browser's Document Object Model (DOM).

To render components on the server:
1. `driftjs-ssr` instantiates a headless Virtual Machine instance.
2. Instead of calling browser APIs (like `document.createElement`), the headless VM executes instructions against mock DOM nodes in memory.
3. The VM executes the component's script block to resolve the initial state scope.
4. It processes the bytecode instruction stream, building an in-memory mock DOM tree.
5. Finally, the headless VM walks the mock tree and serializes it into a static HTML string.

---

## 🔄 Hydration Flow

To load server-rendered applications in the browser without re-rendering elements:
1. The server renders the component to a static HTML string using `renderToString` and serves it to the client.
2. The browser loads the static HTML instantly.
3. The client-side runtime calls `hydrate` with the compiled bytecode module.
4. The VM matches existing DOM elements against its registers, registers event listeners, and attaches reactivity bindings in-place, enabling interactivity without resetting the DOM.
