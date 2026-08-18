# API Reference

This page provides the API reference for the main packages in the DriftJS framework.

---

## 📦 `driftjs-dom` (Browser Runtime)

```typescript
import { mount, hydrate, DriftClientVirtualMachine } from 'driftjs-dom'
```

### `mount(module: CompiledModule, container: HTMLElement): void`
Mounts a compiled component template to a DOM container.
* **Arguments**:
  * `module`: A `CompiledModule` object containing bytecode and constants.
  * `container`: The target HTML element.

### `hydrate(module: CompiledModule, container: HTMLElement): void`
Hydrates server-rendered static HTML, binding event handlers and reactive targets in-place without recreating elements.
* **Arguments**:
  * `module`: A `CompiledModule` object.
  * `container`: The HTML element containing the server-rendered markup.

### `DriftClientVirtualMachine`
The Virtual Machine class that executes bytecode in the browser.
* **Properties**:
  * `registers: any[]`: Virtual register bank storing DOM nodes and values.
  * `scope: Record<string, any>`: Reactivity scope object.
* **Methods**:
  * `execute(module: CompiledModule): any`: Runs instruction stream and returns output node.
  * `triggerUpdates(changedKeys: string[]): void`: Patch registered DOM nodes whose dependencies are listed in `changedKeys`.

---

## 📦 `driftjs-compiler` (Build-time Compiler)

```typescript
import { interpret, DriftLexer, DriftParser, DriftTransformer, DriftGenerator } from 'driftjs-compiler'
```

### `interpret(source: string): CompiledModule`
Compiles a `.drift` single-file component template string into a bytecode module.
* **Arguments**:
  * `source`: The raw template source code string.
* **Returns**:
  * `CompiledModule`: Object containing bytecode (`Uint8Array`), constants (`any[]`), and reactive bindings.

### `DriftLexer`
* **Constructor**: `new DriftLexer(source: string)`
* **Methods**:
  * `nextToken(): Token`: Returns the next token in the stream.

### `DriftParser`
* **Constructor**: `new DriftParser(lexer: DriftLexer)`
* **Methods**:
  * `parse(): ProgramNode`: Parses the token stream and returns the AST program.

---

## 📦 `driftjs-ssr` (Server-Side Renderer)

```typescript
import { renderToString } from 'driftjs-ssr'
```

### `renderToString(module: CompiledModule): string`
Executes a compiled module inside a headless VM and returns the static HTML string.
* **Arguments**:
  * `module`: A `CompiledModule` object.
* **Returns**:
  * `string`: The rendered static HTML markup.
