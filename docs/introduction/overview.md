# Overview

**DriftJS** is a next-generation frontend framework powered by an in-browser **register-based Bytecode Virtual Machine (VM)**.

Unlike traditional Virtual DOM frameworks (e.g., React) that re-evaluate large tree structures, or compiler-only reactive frameworks (e.g., Svelte) that generate heavy boilerplate, DriftJS compiles template ASTs into compact, binary-serializable bytecode streams (`CompiledModule`). At runtime, a lightweight 256-register VM executes these instructions directly against the DOM with minimal memory allocation and surgical updates.

---

## 🎯 Architectural Philosophy

Most modern frontend frameworks are built on top of the **Virtual DOM (VDOM)** tree comparison. While the VDOM was a significant improvement over direct, unoptimized DOM manipulation, it introduces substantial overhead:
1. **Tree Diffing**: Traversing VDOM trees to find differences grows linearly with the size of your template.
2. **Memory Overhead**: Every render cycle allocates millions of temporary JavaScript objects representing the VDOM, triggering frequent Garbage Collection (GC) pauses.
3. **Execution Size**: VDOM runtimes carry complex reconciliation algorithms, synthetic event systems, and component lifecycles that bloat the bundle size.

**DriftJS** bypasses the VDOM completely. By using a register-based VM architecture, DriftJS mimics the design of hardware CPUs:
- **Registers (r0–r255)** act as high-speed slots to store active DOM elements, text nodes, and evaluated JavaScript primitives.
- **Bytecode Stream** acts as the execution path, executing linear commands (like `CREATE_ELEMENT`, `SET_ATTR`, `APPEND_CHILD`) that construct the DOM step-by-step.
- **Direct Patching** points the VM back to target instructions (like `INTERPOLATE_TEXT` or `SET_ATTR`) to modify specific DOM nodes when state changes, avoiding tree traversal entirely.

---

## ⚡ Key Features

### 1. Register-Based Virtual Machine
DriftJS implements a client-side register machine with **256 registers** (`r0` through `r255`). When executing a component's compiled bytecode, the VM stores references to DOM elements, attributes, text interpolations, and arrays directly in these registers, minimizing JavaScript heap allocations.

### 2. Streamlined 15-Opcode ISA
DriftJS defines a custom Instruction Set Architecture (ISA) of 15 compact opcodes that manage control flow, DOM instantiation, attribute patching, expression evaluation, and loop reconciliation.
[View the full ISA Reference →](/architecture/isa)

### 3. Keyed LIS Reconciliation
For array rendering (`@for`), DriftJS utilizes the **Longest Increasing Subsequence (LIS)** algorithm (`reconcileKeyedList`). This minimizes the physical movement, insertion, and deletion of DOM elements, ensuring optimal performance for list swapping, sorting, and clearing.

### 4. Fine-Grained Reactive Regions
Instead of re-evaluating parent containers, DriftJS bounds conditional `@if` and loop `@for` blocks inside HTML comment anchors (`<!--if-->`, `<!--for-->`). This allows the VM to surgically unmount, re-mount, or re-order specific DOM subtrees without touching surrounding elements.

### 5. High-Performance Expression Engine
Rather than using slow, insecure `eval()` or `new Function()` for every expression re-evaluation, DriftJS compiles template expressions into scoped JavaScript functions inside its constant pool, evaluating them on-demand with minimal overhead.

---

## 📦 Package Monorepo Structure

DriftJS is built as a modular monorepo:
- **[`driftjs-compiler`](/packages/compiler)**: Lexer, parser, transformer, and bytecode generator.
- **[`driftjs-dom`](/packages/dom)**: 256-register Client VM, expression engine, keyed LIS reconciler, and `mount()` API.
- **[`driftjs-ssr`](/packages/ssr)**: Headless Server-Side Rendering VM engine (`renderToString()`).
- **[`driftjs-shared`](/packages/shared)**: Shared scope models and expression evaluator.
- **[`driftjs-vite-plugin`](/packages/vite-plugin)**: Transforms `.drift` SFCs into standard ESM modules during Vite builds.
- **[`create-drift`](/packages/cli)**: Interactive CLI scaffolding tool (`npm create drift`).
- **[`drift-vscode`](/packages/vscode)**: VS Code extension providing syntax highlighting and diagnostic checks.

---

> [!WARNING]
> **DriftJS is an Experimental Prototype**
>
> DriftJS is currently a research project exploring bytecode execution for web interfaces.
>
> **What is implemented:**
> - Full compiler pipeline (Lexer, Parser, Transformer, Bytecode Generator)
> - 256-register Client VM and VM scope evaluation
> - Keyed list reconciliation using the LIS algorithm
> - In-place text and attribute patching
> - Server-side rendering (SSR) of template bytecode
> - Vite plugin & HMR (Hot Module Replacement) integration
>
> **What is NOT yet implemented:**
> - Component composition (nesting custom components inside other components)
> - Component props and event passing
> - Global state management stores
> - Client-side routing
> - Browser developer tools & source-map debugging
>
> Read more about our current [Limitations & Roadmap](/project/limitations).
