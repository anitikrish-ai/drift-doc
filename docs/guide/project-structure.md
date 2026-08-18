# Project Structure

This page describes the structural layouts of both standard DriftJS user applications (scaffolder output) and the core DriftJS framework monorepo workspace.

---

## 📂 User Application Layout

When you scaffold a project using `npm create drift`, your application workspace contains the following files and directories:

```
my-drift-app/
├── .gitignore
├── drift-env.d.ts      # TypeScript declarations for .drift files
├── index.html          # HTML entry point containing mounting div
├── package.json        # Project scripts and dependencies
├── tsconfig.json       # TypeScript compiler settings
├── vite.config.ts      # Vite configuration registering driftjs-vite-plugin
└── src/
    ├── main.ts         # Application entry point calling mount()
    ├── style.css       # Global stylesheet (vanilla CSS styles)
    └── App.drift       # Root .drift single-file component
```

### Key Configurations Explained

#### `vite.config.ts`
Vite acts as the builder. The configuration imports and registers the `driftjs-vite-plugin`:
```typescript
import { defineConfig } from 'vite'
import { driftPlugin } from 'driftjs-vite-plugin'

export default defineConfig({
  plugins: [driftPlugin()],
})
```

#### `drift-env.d.ts`
Enables VS Code and the TypeScript compiler to understand that importing a `.drift` file returns a compiled bytecode module (`CompiledModule`) conforming to the framework's runtime expectations:
```typescript
declare module '*.drift' {
  import { CompiledModule } from 'driftjs-dom'
  const component: CompiledModule
  export default component
}
```

#### `src/main.ts`
This file boots the runtime: imports the root component, targets a container in `index.html`, and calls `mount()`:
```typescript
import { mount } from 'driftjs-dom'
import App from './App.drift'

mount(App, document.getElementById('app')!)
```

---

## 🏛️ Monorepo Framework Layout

If you are developing features or writing patches for the DriftJS core codebase itself, the monorepo workspace has the following structure:

```
driftjs/
├── docs/               # Technical specs (ISA, testing inventories)
├── packages/           # Decoupled packages published to npm
│   ├── cli/            # Scaffolder source code (create-drift CLI tool)
│   ├── compiler/       # Compiler pipeline (Lexer, Parser, AST, Bytecode Generator)
│   ├── dom/            # Client VM, Expression engine, and Keyed list reconciler
│   ├── shared/         # Common TypeScript utilities and scope management
│   ├── ssr/            # Headless Server-Side Rendering VM
│   ├── vite-plugin/    # Vite plugin wrapping driftjs-compiler
│   └── vscode-plugin/  # VS Code syntax highlighting and diagnostics source
├── playground/         # Local workspace interactive template runner
├── scripts/            # Build automation and clean utilities
├── template/           # Default template copied by create-drift CLI
├── package.json        # Monorepo workspaces, scripts, and devDependencies
├── pnpm-workspace.yaml # pnpm monorepo workspace definitions
└── tsconfig.json       # Root monorepo TypeScript declarations
```

### Development Roles of Monorepo Components
* **Lexing & Parsing**: Done in `packages/compiler/src/lexer.ts` and `packages/compiler/src/parser.ts`.
* **Bytecode Generation**: Emitted in `packages/compiler/src/generator.ts`.
* **Execution**: Ran inside `packages/dom/src/client.ts` and `packages/dom/src/vm.ts`.
* **Array Reconciliation**: Structured in `packages/dom/src/reconciler.ts` (LIS reconciler).
