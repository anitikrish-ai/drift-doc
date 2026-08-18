# driftjs-vite-plugin

The `driftjs-vite-plugin` package provides Vite integration, compiling `.drift` single-file templates into ESM JavaScript modules.

---

## 🔧 Installation & Setup

Add the plugin to your development dependencies:

```bash
pnpm add -D driftjs-vite-plugin
```

Register the plugin in your project's `vite.config.ts`:

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

## ⚙️ How It Works (Vite Transform)

During local development or production bundling, the plugin intercepts imports of `.drift` files and runs a transformation pass:

```
import App from './App.drift'
           │
           ▼
[ Vite Interceptor ]
           │
           ▼
Calls compiler: interpret(sourceCode)
           │
           ▼
[ Wraps CompiledModule into ESM export + HMR runtime glue ]
           │
           ▼
Emitted Module (JavaScript file containing bytecode array & constant pool)
```

### Generated Module Example
A `.drift` file is compiled and resolved as a standard JavaScript module in the bundle:

```javascript
import { CompiledModule } from 'driftjs-dom'

// Pre-compiled bytecode and constants
const App = {
  constants: ["div", "Hello World"],
  bytecode: new Uint8Array([1, 0, 0, 2, 1, 1, 4, 0, 1, 0, 0]),
  reactiveBindings: []
};

export default App;

// Hot Module Replacement (HMR) registration during local development
if (import.meta.hot) {
  import.meta.hot.accept((newModule) => {
    if (newModule) {
      window.__DRIFT_DEV_RELOAD__?.(newModule.default);
    }
  });
}
```
*Because the browser receives pre-compiled bytecode rather than template strings, there is no need to load the compiler in the client bundle, keeping the runtime size small.*
