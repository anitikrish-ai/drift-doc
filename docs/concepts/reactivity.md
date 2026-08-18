# Reactivity

This page explains how reactivity is implemented in DriftJS, focusing on scope mapping, dependency tracking, and update triggers.

---

## 🚫 No Proxy-Based Observers

Many modern frontend frameworks (like Vue or Svelte) use JavaScript `Proxy` objects or signal wrappers to observe variable reads and writes. While proxies provide fine-grained updates, they introduce:
1. **Performance Penalties**: Wrapping objects in proxy handlers adds CPU overhead to every property access.
2. **Memory Footprint**: Observer graphs allocate millions of node objects, leading to high garbage collector churn.

**DriftJS avoids proxy tracking entirely.** 

DriftJS relies on a **compiler-informed update trigger system**. Reactivity operates through explicit top-level assignments in event handlers or scripts. 

---

## 📐 Scope & Compile-Time Dependency Tracking

During compilation, the `DriftTransformer` analyzes the AST of both the script block and dynamic expressions in the template.

### 1. Variables Scope
When a component compiles, the compiler extracts all top-level variables (declared via `let` or `const` in `<script>`) and maps them into a flat execution scope object:

```javascript
// Runtime scope instance
const scope = {
  count: 0,
  username: "Alex",
  increment() {
    this.count = this.count + 1; // triggers VM update!
  }
}
```

### 2. Dependency Array Mapping
Every dynamic expression (in attribute bindings, text interpolations, conditional tests, and loops) is analyzed to extract its referenced variable names. The generator then writes these variables into a static dependency array for each reactive opcode:

* For a text interpolation `{count + 1}`:
  * Expression AST: `count + 1`
  * Dependencies: `['count']`
  * Associated Opcode: `INTERPOLATE_TEXT` (at instruction address `PC`)

* For a conditional block `@if user.role === 'admin'`:
  * Expression AST: `user.role === 'admin'`
  * Dependencies: `['user']`
  * Associated Opcode: `REACTIVE_IF` (at instruction address `PC`)

---

## 🔄 The Update Cycle (`triggerUpdates`)

When a state mutation occurs (typically triggered by an event handler), the VM executes a central update cycle:

```
State Mutation (e.g. count = count + 1)
   │
   ▼
[ VM interceptor / writeback ]
   │
   ▼
[ triggerUpdates(changedKeys) ]
   │
   ▼
Iterate through reactiveBindings / reactiveRegions
   │
   ├─► Is dependency key in changedKeys?
   │      ├─ Yes: Re-evaluate expression and patch DOM
   │      └─ No:  Skip (Zero work)
```

### In-Place Patching Examples

#### 1. Text Interpolation Update
If `count` changes, the VM looks up all `reactiveBindings` pointing to `count`. If there is a binding at `PC = 18`:
1. The VM jumps directly to `PC = 18` in the bytecode.
2. Re-runs the `INTERPOLATE_TEXT` instruction.
3. Evaluates the expression in the constant pool using the current scope.
4. Updates the text content of the existing DOM `TextNode` directly (surgical in-place patch).

#### 2. Attribute Update
If a button is bound via `<button disabled={items.length === 0}>`, a change in `items` triggers a jump to the corresponding `SET_ATTR` instruction:
1. Re-evaluates `items.length === 0`.
2. Compares the new value with the previously cached value.
3. If different, performs an in-place `setAttribute` or `removeAttribute` on the target element.

---

> [!NOTE]
> **Explicit Mutation Requirement**
>
> Reactivity in DriftJS is triggered by variable assignments (e.g. `items = items.map(...)`). Direct mutating actions on arrays (such as `arr.push(item)`) will not trigger updates unless followed by an assignment or explicit scope update. This matches Svelte's reactivity model and keeps compiler code overhead extremely light.
