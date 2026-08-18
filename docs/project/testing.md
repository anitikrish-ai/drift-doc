# Testing

This page outlines the test suite organization, coverage metrics, and test execution details for the DriftJS project.

---

## 📊 Workspace Test Suite Inventory

Total Test Suites: **11** | Total Test Cases: **103** | Pass Rate: **100%**

| Package Name | Package Directory | Test Suites | Test Cases | Status |
| :--- | :--- | :---: | :---: | :---: |
| **`driftjs-compiler`** | `packages/compiler` | 4 | 52 | ✅ PASS |
| **`driftjs-shared`** | `packages/utils` | 1 | 5 | ✅ PASS |
| **`driftjs-dom`** | `packages/dom` | 3 | 19 | ✅ PASS |
| **`driftjs-ssr`** | `packages/ssr` | 1 | 5 | ✅ PASS |
| **`driftjs-vite-plugin`** | `packages/vite-plugin` | 1 | 14 | ✅ PASS |
| **`create-drift`** | `packages/cli` | 1 | 8 | ✅ PASS |

---

## 📦 Package Test Inventories

### 1. `driftjs-compiler` (`packages/compiler`)
Tests the core compiler stages (Lexer, Parser, Transformer, and Generator).
* **Lexer Tests (`tests/lexer.test.ts`)**: Verifies state transitions (e.g. `Data` ➔ `TagOpen` ➔ `Data`), nested expression braces, backticks inside interpolations, hyphen identifiers inside attributes, and syntax error throwing.
* **Parser Tests (`tests/parser.test.ts`)**: Verifies parsing of HTML elements, mixed attribute kinds, nested control flow directives (`@if`, `@for`, `@switch`), script blocks, and parser error recovery bounds.
* **Transformer Tests (`tests/transformer.test.ts`)**: Verifies stripping of redundant spaces/newlines and transformation of raw string expressions into JS ASTs.
* **Generator Tests (`tests/generator.test.ts`)**: Verifies correct emission of opcodes, operands, constants, and dependency binding mappings.

### 2. `driftjs-dom` (`packages/dom`)
Tests client-side VM execution, event handling, and list reconciliation.
* **Client VM Tests (`tests/client.test.ts`)**: Verifies execution of standard DOM instructions, reactive `@if` and `@for` block updates, component mounting, and delegated event bubbling.
* **LIS Reconciler Tests (`tests/edge-cases.test.ts`)**: Verifies that the Longest Increasing Subsequence (LIS) algorithm moves existing DOM elements and preserves element identities during list swaps.
* **Hydration Tests (`tests/hydration.test.ts`)**: Verifies that `hydrate()` claims server-rendered markup in-place and binds event listeners without recreating DOM nodes.

### 3. `driftjs-ssr` (`packages/ssr`)
Tests headless VM execution on the server.
* **SSR Tests (`tests/ssr.test.ts`)**: Verifies server rendering of static HTML strings and mock DOM elements.

### 4. `driftjs-vite-plugin` (`tests/vite-plugin.test.ts`)
Verifies transformation of `.drift` single-file components into valid JavaScript ESM outputs.

### 5. `create-drift` (`tests/cli.test.ts`)
Verifies interactive prompts, flag evaluations, and starter file exports.

---

## 🛠️ Running the Test Suites

To execute the test suites locally, install workspace dependencies and run Vitest:

```bash
# Install monorepo dependencies
pnpm install

# Run the test runner (Vitest)
pnpm test

# Run type checking checks
pnpm typecheck
```
