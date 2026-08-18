# Monorepo Packages

DriftJS is structured as a monorepo containing several decoupled packages that coordinate to manage the compilation, runtime execution, SSR rendering, and developer tooling of the framework.

---

## 📦 Package Directory

| Package Name | Path | Role | Description |
| :--- | :--- | :--- | :--- |
| **[`driftjs-compiler`](/packages/compiler)** | `packages/compiler` | Build-time | Lexes, parses, transforms, and generates bytecode from templates |
| **[`driftjs-dom`](/packages/dom)** | `packages/dom` | Client-side | 256-register VM, expression evaluator, LIS reconciler, and client mounting |
| **[`driftjs-ssr`](/packages/ssr)** | `packages/ssr` | Server-side | Headless SSR engine that pre-renders components to static HTML strings |
| **[`driftjs-shared`](/packages/shared)** | `packages/shared` | Shared | Shared scope management models and AST expression evaluators |
| **[`driftjs-vite-plugin`](/packages/vite-plugin)** | `packages/vite-plugin` | Build-tooling | Vite plugin that transforms `.drift` SFC files into ESM JavaScript modules |
| **[`create-drift`](/packages/cli)** | `packages/cli` | CLI Tool | Interactive CLI scaffolder (`npm create drift`) |
| **[`drift-vscode`](/packages/vscode)** | `packages/vscode` | IDE Extension | VS Code extension providing syntax highlighting and diagnostic checks |

---

## 🔄 Package Coordination

This diagram illustrates how these packages interact during development and execution:

```
                  [ create-drift ] ──► Scaffolds new workspace
                           │
                           ▼
                  [ drift-vscode ] ──► IDE Syntax Highlighting
                           │
                           ▼
                 [ driftjs-vite-plugin ]
                           │
                           ├─► Calls [ driftjs-compiler ] (Compile .drift to Bytecode)
                           │
                           ▼
                  Vite Bundles Output
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      (Browser Client)            (Server SSR Node)
     [ driftjs-dom ]             [ driftjs-ssr ]
  (Runs 256-Register VM)       (Runs Headless VM)
             │                           │
             ▼                           ▼
        Dynamic DOM                 Static HTML
```
