# Limitations & Roadmap

As an experimental prototype exploring register-based VM execution for web interfaces, DriftJS is under active development. This page details current limitations and future roadmap goals.

---

## ⚠️ Current Limitations

### 1. Component Nesting & Composition
DriftJS currently supports only single-template compilation. You cannot import or nest custom `.drift` components inside other templates:
* **Current status**: Nested templates are ignored by the parser.
* **Workaround**: Build layouts within a single template or use separate entry points mounted individually.

### 2. State & Data Routing
* **Props/Events**: Passing data parameters or custom events between components is not supported.
* **Routing**: There is no official router package. Single-page applications must manage navigation using manual state switching directives (like `@switch`).
* **Stores**: No global state management container (such as Redux or Pinia) is integrated.

### 3. Developer Debugging Support
* **Source Maps**: Bytecode execution does not currently support mapping stack traces back to template line numbers.
* **DevTools**: There is no browser developer extension to inspect registers, variable scopes, or VM instructions.

### 4. Deep Reactivity Tracking
Reactivity is triggered by assignments (`=`). Mutating arrays or nested objects in-place (e.g. `items.push()`) will not update the DOM unless the variable is reassigned (`items = [...]`).

---

## 🗺️ Roadmap and Active Design

We invite compiler engineers, framework researchers, and open-source contributors to collaborate on these roadmap priorities:

### Phase 1: Nested Component Support (High Priority)
* **Goal**: Enable importing custom components inside templates.
* **Proposed Design**: Compile custom component tags into sub-modules and use a new `CALL_COMPONENT` opcode to instantiate them in the register bank.

### Phase 2: Props and Event Passing
* **Goal**: Establish communication channels between parent and child VMs.
* **Proposed Design**: Allocate a reserved range of registers (e.g. `r100`–`r120`) to store incoming property references and callback handlers.

### Phase 3: Scope and Store Management
* **Goal**: Implement shared reactive stores.
* **Proposed Design**: Integrate a global proxy wrapper that registers component VM instances as listeners and broadcasts updates when properties change.

### Phase 4: Developer Diagnostics Tools
* **Goal**: Improve debugging.
* **Proposed Design**: Output source-map mappings during compilation, mapping bytecode instruction indexes (`PC` values) back to original template lines.
