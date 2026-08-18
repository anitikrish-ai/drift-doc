# ISA Reference

This page provides the specification for the DriftJS register-based Bytecode Instruction Set Architecture (ISA).

---

## 🔢 Opcode Summary Table

| Opcode Name | Hex | Dec | Length (Bytes) | Operands | Category | Summary |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| **`RETURN`** | `0x00` | `0` | 2 | `reg` | Control Flow | Returns DOM node/fragment from register as execution output |
| **`CREATE_ELEMENT`** | `0x01` | `1` | 3 | `dstReg, tagIdx` | DOM Node Creation | Instantiates DOM Element for tag `constants[tagIdx]` into `dstReg` |
| **`CREATE_TEXT`** | `0x02` | `2` | 3 | `dstReg, textIdx` | DOM Node Creation | Creates static DOM TextNode with content `constants[textIdx]` |
| **`CREATE_COMMENT`** | `0x03` | `3` | 3 | `dstReg, commentIdx` | DOM Node Creation | Creates DOM Comment node with content `constants[commentIdx]` |
| **`APPEND_CHILD`** | `0x04` | `4` | 3 | `parentReg, childReg` | DOM Manipulation | Appends node `childReg` to parent element `parentReg` |
| **`SET_ATTR`** | `0x05` | `5` | 5 | `elemReg, nameIdx, valIdx, isDynamic` | Attribute Patching | Sets attribute `constants[nameIdx]` on element in `elemReg` |
| **`CREATE_FRAGMENT`** | `0x06` | `6` | 2 | `dstReg` | DOM Node Creation | Creates a `DocumentFragment` into register `dstReg` |
| **`INTERPOLATE_TEXT`** | `0x07` | `7` | 3 | `dstReg, exprIdx` | Dynamic Binding | Evaluates expression `constants[exprIdx]` to create/patch TextNode |
| **`JUMP`** | `0x08` | `8` | 2 | `targetPc` | Control Flow | Unconditional jump to bytecode position `PC = targetPc` |
| **`JUMP_IF_FALSE`** | `0x09` | `9` | 3 | `condReg, targetPc` | Control Flow | Jumps to `PC = targetPc` if `condReg` evaluates to falsy |
| **`EVAL_EXPR`** | `0x0A` | `10` | 3 | `dstReg, exprIdx` | Expression Eval | Evaluates AST `constants[exprIdx]` into register `dstReg` |
| **`LOOP_ITER`** | `0x0B` | `11` | 5 | `arrReg, itemReg, indexReg, bodyPc` | Loop Control | Low-level loop iteration over array register |
| **`EXEC_SCRIPT`** | `0x0C` | `12` | 2 | `scriptIdx` | Scope Initialisation | Executes `<script>` AST `constants[scriptIdx]` into component scope |
| **`REACTIVE_IF`** | `0x0D` | `13` | 6 | `parentReg, condIdx, consIdx, altIdx, depsIdx` | Reactive Block | Binds dynamic conditional `@if` block between comment anchors |
| **`REACTIVE_FOR`** | `0x0E` | `14` | 7 | `parentReg, iterIdx, itemIdx, idxIdx, bodyIdx, depsIdx` | Reactive Block | Binds dynamic `@for` loop with LIS reconciliation & fast-path patching |

---

## 📖 Detailed Opcode Specifications

### `RETURN` (`0x00`)
* **Bytecode**: `0x00 <reg>`
* **Length**: 2 bytes
* **Category**: <span class="opcode-control">Control Flow</span>
* **Description**: Stops execution of the current module and returns the DOM Node or DocumentFragment stored in register `reg` to the VM caller.

### `CREATE_ELEMENT` (`0x01`)
* **Bytecode**: `0x01 <dstReg> <tagIdx>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-dom">DOM Node Creation</span>
* **Description**: Instantiates a DOM element using the HTML tag name string stored at `constants[tagIdx]` (e.g. `'div'`, `'button'`, `'li'`) and places the element reference in `dstReg`.

### `CREATE_TEXT` (`0x02`)
* **Bytecode**: `0x02 <dstReg> <textIdx>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-dom">DOM Node Creation</span>
* **Description**: Instantiates a static DOM `TextNode` with text content from `constants[textIdx]` and stores the reference in `dstReg`.

### `CREATE_COMMENT` (`0x03`)
* **Bytecode**: `0x03 <dstReg> <commentIdx>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-dom">DOM Node Creation</span>
* **Description**: Creates a DOM `Comment` node containing static text from `constants[commentIdx]` and places the reference in `dstReg`. Bypassed in layouts but useful for debug landmarks.

### `APPEND_CHILD` (`0x04`)
* **Bytecode**: `0x04 <parentReg> <childReg>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-dom">DOM Manipulation</span>
* **Description**: Appends the DOM node stored in `childReg` as a child of the element or document fragment stored in `parentReg`.

### `SET_ATTR` (`0x05`)
* **Bytecode**: `0x05 <elemReg> <nameIdx> <valIdx> <isDynamic>`
* **Length**: 5 bytes
* **Category**: <span class="opcode-attr">Attribute Patching</span>
* **Description**: Sets the attribute named `constants[nameIdx]` on the element stored in register `elemReg`.
  * If `isDynamic === 0`: Sets the literal string value from `constants[valIdx]`.
  * If `isDynamic === 1`: Evaluates the AST expression stored in `constants[valIdx]` against the active component scope.

### `CREATE_FRAGMENT` (`0x06`)
* **Bytecode**: `0x06 <dstReg>`
* **Length**: 2 bytes
* **Category**: <span class="opcode-dom">DOM Node Creation</span>
* **Description**: Creates an in-memory DOM `DocumentFragment` and stores the reference in register `dstReg`. Fragments are useful for grouping sibling root elements.

### `INTERPOLATE_TEXT` (`0x07`)
* **Bytecode**: `0x07 <dstReg> <exprIdx>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-expr">Dynamic Binding</span>
* **Description**: Evaluates the AST expression stored at `constants[exprIdx]` against the current scope, creates a DOM `TextNode` with the string result, and stores it in `dstReg`. The node is registered in `reactiveBindings` for in-place text updates when scope variables change.

### `JUMP` (`0x08`)
* **Bytecode**: `0x08 <targetPc>`
* **Length**: 2 bytes
* **Category**: <span class="opcode-control">Control Flow</span>
* **Description**: Unconditionally branches execution by setting the Program Counter (`PC`) to `targetPc`.

### `JUMP_IF_FALSE` (`0x09`)
* **Bytecode**: `0x09 <condReg> <targetPc>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-control">Control Flow</span>
* **Description**: Inspects register `condReg`. If the value is falsy (`false`, `null`, `undefined`, `0`, `""`), sets the Program Counter (`PC`) to `targetPc`.

### `EVAL_EXPR` (`0x0A`)
* **Bytecode**: `0x0A <dstReg> <exprIdx>`
* **Length**: 3 bytes
* **Category**: <span class="opcode-expr">Expression Eval</span>
* **Description**: Evaluates the AST expression node stored at `constants[exprIdx]` in the component scope and stores the primitive result (e.g. string or number) in register `dstReg`.

### `LOOP_ITER` (`0x0B`)
* **Bytecode**: `0x0B <arrReg> <itemReg> <indexReg> <bodyPc>`
* **Length**: 5 bytes
* **Category**: <span class="opcode-control">Loop Control</span>
* **Description**: Iterates over the array stored in `arrReg`. Updates `itemReg` with the current item and `indexReg` with the current index, then jumps to `bodyPc` to run the loop body.

### `EXEC_SCRIPT` (`0x0C`)
* **Bytecode**: `0x0C <scriptIdx>`
* **Length**: 2 bytes
* **Category**: <span class="opcode-reactive">Scope Initialisation</span>
* **Description**: Executes the `<script>` block AST statement array stored at `constants[scriptIdx]` inside the VM, initialising scope variables and event handlers before DOM construction starts.

### `REACTIVE_IF` (`0x0D`)
* **Bytecode**: `0x0D <parentReg> <condIdx> <consIdx> <altIdx> <depsIdx>`
* **Length**: 6 bytes
* **Category**: <span class="opcode-reactive">Reactive Block</span>
* **Description**: Binds a dynamic conditional `@if` block inside comment anchors (`<!--if-->` and `<!--/if-->`) within the parent element `parentReg`. Re-evaluates condition `constants[condIdx]` and mounts consequent module `constants[consIdx]` or alternate module `constants[altIdx]` when variables in dependency array `constants[depsIdx]` change.

### `REACTIVE_FOR` (`0x0E`)
* **Bytecode**: `0x0E <parentReg> <iterIdx> <itemNameIdx> <idxNameIdx> <bodyIdx> <depsIdx>`
* **Length**: 7 bytes
* **Category**: <span class="opcode-reactive">Reactive Block</span>
* **Description**: Binds a dynamic loop `@for` block inside comment anchors (`<!--for-->` and `<!--/for-->`) within the parent element `parentReg`. Iterates over array `constants[iterIdx]` using the sub-module template `constants[bodyIdx]`.
* **Features**:
  * Uses Longest Increasing Subsequence (LIS) keyed reconciliation (`reconcileKeyedList`).
  * Uses `patchItemAttributes` fast-path for unchanged item references when scope dependencies in `constants[depsIdx]` change.
