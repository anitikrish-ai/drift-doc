# Register VM

This page covers the architecture of the DriftJS Virtual Machine, explaining how it uses 256 virtual registers to execute template bytecode.

---

## 🏛️ Register-Based vs. Stack-Based VMs

Virtual Machines generally fall into two categories:

### Stack-Based VMs (e.g. JVM, V8 Bytecode)
Operations push values onto a stack and pop them off to perform computations.
* *Example instruction stream*: `PUSH a`, `PUSH b`, `ADD`, `STORE c`.
* *Pros*: Very easy to compile.
* *Cons*: Requires many instructions; high memory allocation for stack pointer operations.

### Register-Based VMs (e.g. Lua, Dalvik, DriftJS)
Operations specify input and output registers directly, mimicking real CPU registers.
* *Example instruction stream*: `ADD r0, r1, r2` (add r1 and r2, store in r0).
* *Pros*: Fewer instructions overall; lower memory churn; closer mapping to hardware.
* *Cons*: Compiler register allocation is more complex.

DriftJS implements a **register-based machine** because UI construction maps naturally to register allocations: a register can hold a parent DOM element, another can hold a child, and an append instruction connects them directly.

---

## 🔢 Register Bank (`r0`–`r255`)

The DriftJS VM contains exactly **256 registers** representing slots on the VM instance:

```typescript
class DriftClientVirtualMachine {
  // Registers array storing DOM elements, text nodes, and primitives
  public registers: any[] = new Array(256).fill(null);
  
  // Program counter tracking current instruction
  private pc: number = 0;
}
```

### What Goes Into a Register?
* **DOM Element**: References to HTML elements instantiated via `CREATE_ELEMENT`.
* **DOM TextNode**: Static or dynamic text nodes created via `CREATE_TEXT` or `INTERPOLATE_TEXT`.
* **DocumentFragment**: Temporary wrapper nodes used to hold sibling root elements during updates.
* **Primitives**: JavaScript primitives (booleans, strings, numbers) evaluated via `EVAL_EXPR`.
* **Comments**: Anchors used for rendering `@if` and `@for` blocks.

---

## 🎨 Visualizing Register Allocations

Below is a visualization of how registers are utilized during compilation of a simple layout:

<div class="register-vis">
  <div class="register-slot" style="border-color: var(--drift-accent);">
    <span class="reg-name">r0</span>
    <span class="reg-label">Fragment Container</span>
  </div>
  <div class="register-slot" style="border-color: var(--drift-accent);">
    <span class="reg-name">r1</span>
    <span class="reg-label">div (wrapper)</span>
  </div>
  <div class="register-slot" style="border-color: var(--drift-accent-purple);">
    <span class="reg-name">r2</span>
    <span class="reg-label">h1 element</span>
  </div>
  <div class="register-slot" style="border-color: var(--drift-accent-cyan);">
    <span class="reg-name">r3</span>
    <span class="reg-label">TextNode ("Hello")</span>
  </div>
  <div class="register-slot">
    <span class="reg-name">r4</span>
    <span class="reg-label">p element</span>
  </div>
  <div class="register-slot">
    <span class="reg-name">r5</span>
    <span class="reg-label">TextNode (Count)</span>
  </div>
  <div class="register-slot">
    <span class="reg-name">r6</span>
    <span class="reg-label">evaluated count</span>
  </div>
  <div class="register-slot">
    <span class="reg-name">r7...r255</span>
    <span class="reg-label">Unallocated / Empty</span>
  </div>
</div>

### Walkthrough of Register Lifecycle
Let's trace how the VM builds this DOM structure:
1. `CREATE_FRAGMENT r0` instantiates a wrapper.
2. `CREATE_ELEMENT r1, 0` (where `constants[0]` is `'div'`) places the wrapper div in `r1`.
3. `CREATE_ELEMENT r2, 1` (where `constants[1]` is `'h1'`) places the heading element in `r2`.
4. `CREATE_TEXT r3, 2` (where `constants[2]` is `'Hello'`) places the static greeting text node in `r3`.
5. `APPEND_CHILD r2, r3` appends the text node (`r3`) to the heading element (`r2`).
6. `APPEND_CHILD r1, r2` appends the heading element (`r2`) to the wrapper div (`r1`).
7. `APPEND_CHILD r0, r1` appends the wrapper div (`r1`) to the root fragment (`r0`).
8. `RETURN r0` outputs the completed fragment.

Because the compiler knows the lifetime of DOM subtrees, it **reclaims and reuses** registers. Once a node is appended to its parent, its register slot can be reused for subsequent operations, keeping the active register count well under 256.
