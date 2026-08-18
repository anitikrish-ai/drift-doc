# driftjs-dom

The `driftjs-dom` package is the browser runtime for DriftJS. It contains the 256-register Virtual Machine, expression engine, keyed LIS reconciler, and user mounting APIs.

---

## 🔧 Core API Methods

### 1. `mount`
Mounts a compiled bytecode module to a target HTML element:

```typescript
import { mount } from 'driftjs-dom'
import App from './App.drift' // Compiled to a CompiledModule object

const container = document.getElementById('app');

if (container) {
  // Evaluates component script, creates DOM elements,
  // attaches event handlers, and appends the output to the container.
  mount(App, container);
}
```

### 2. `hydrate`
Hydrates pre-rendered SSR HTML markup in the browser, attaching event listeners and reactivity bindings to existing DOM nodes without recreating them:

```typescript
import { hydrate } from 'driftjs-dom'
import App from './App.drift'

const container = document.getElementById('app');

if (container) {
  // Claims existing SSR HTML nodes and attaches event listeners
  hydrate(App, container);
}
```

---

## 🏛️ Internal Runtime Classes

### 1. `DriftClientVirtualMachine`
The Virtual Machine manages execution state in the browser:
* **`registers`**: Array storing references to DOM elements, text nodes, and evaluated values.
* **`scope`**: Flat object holding component variables and helper functions.
* **`execute(module)`**: Loops through bytecode and runs the corresponding instructions.
* **`triggerUpdates(changedVars)`**: Jumps to target instructions to patch DOM elements when variables in `changedVars` are modified.

### 2. `reconcileKeyedList`
The Keyed Reconciler matches items by unique keys, calculates the Longest Increasing Subsequence (LIS) of indices, and reorders elements in-place to minimize DOM insertions and deletions.
[View the Reconciliation Concept →](/concepts/reconciliation)
