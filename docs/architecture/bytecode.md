# Bytecode Design

This page covers the bytecode design of DriftJS, detailing instruction formats, variable-length layouts, and compilation outputs.

---

## ⚡ Instruction Format

DriftJS templates are compiled into a binary instruction stream (`Uint8Array`).

Each instruction in the stream has a structured layout:
1. **Opcode**: A 1-byte identifier specifying the operation (e.g. `0x01` for `CREATE_ELEMENT`).
2. **Operands**: Zero or more bytes containing arguments (like register indexes or constant pool offsets).

```
┌───────────┬───────────┬───────────┐
│  OPCODE   │ OPERAND 1 │ OPERAND 2 │ ...
│  (1 Byte) │  (1 Byte) │  (1 Byte) │
└───────────┴───────────┴───────────┘
```

The length of each instruction is fixed based on its opcode, allowing the VM to parse the stream without reading instruction headers.

---

## 📐 Instruction Lengths & Categories

The Instruction Set Architecture (ISA) contains 15 instructions categorized by role:

| Instruction | Hex | Opcode | Length (Bytes) | Operand Details |
| :--- | :---: | :---: | :---: | :--- |
| **`RETURN`** | `0x00` | 0 | 2 | `reg` |
| **`CREATE_ELEMENT`** | `0x01` | 1 | 3 | `dstReg, tagIdx` |
| **`CREATE_TEXT`** | `0x02` | 2 | 3 | `dstReg, textIdx` |
| **`CREATE_COMMENT`** | `0x03` | 3 | 3 | `dstReg, commentIdx` |
| **`APPEND_CHILD`** | `0x04` | 4 | 3 | `parentReg, childReg` |
| **`SET_ATTR`** | `0x05` | 5 | 5 | `elemReg, nameIdx, valIdx, isDynamic` |
| **`CREATE_FRAGMENT`** | `0x06` | 6 | 2 | `dstReg` |
| **`INTERPOLATE_TEXT`** | `0x07` | 7 | 3 | `dstReg, exprIdx` |
| **`JUMP`** | `0x08` | 8 | 2 | `targetPc` |
| **`JUMP_IF_FALSE`** | `0x09` | 9 | 3 | `condReg, targetPc` |
| **`EVAL_EXPR`** | `0x0A` | 10 | 3 | `dstReg, exprIdx` |
| **`LOOP_ITER`** | `0x0B` | 11 | 5 | `arrReg, itemReg, indexReg, bodyPc` |
| **`EXEC_SCRIPT`** | `0x0C` | 12 | 2 | `scriptIdx` |
| **`REACTIVE_IF`** | `0x0D` | 13 | 6 | `parentReg, condIdx, consIdx, altIdx, depsIdx` |
| **`REACTIVE_FOR`** | `0x0E` | 14 | 7 | `parentReg, iterIdx, itemIdx, idxIdx, bodyIdx, depsIdx` |

---

## 🔍 Compilation Walkthrough

Let's look at how the compiler processes a basic dynamic button element:

```html
<button onclick={increment}>Clicks: {count}</button>
```

### 1. Constant Pool Extraction
Static string descriptors and dynamic JS AST nodes are placed in the constant pool:

```javascript
constants: [
  "button",                // constants[0] - Element tag
  "onclick",               // constants[1] - Event attribute key
  AST_node_for_increment,  // constants[2] - Event handler callback AST
  "Clicks: ",              // constants[3] - Static label text
  AST_node_for_count       // constants[4] - Counter expression AST
]
```

### 2. Bytecode Generation
The compiler emits a `Uint8Array` instruction stream:

```javascript
bytecode: new Uint8Array([
  // Instantiates a button element into register r1
  0x01, 1, 0,              // CREATE_ELEMENT r1, constants[0]

  // Attaches event handler from constants[2] to button element in r1
  0x05, 1, 1, 2, 1,        // SET_ATTR r1, constants[1], constants[2], isDynamic = 1

  // Creates the static text node label into register r2
  0x02, 2, 3,              // CREATE_TEXT r2, constants[3]

  // Appends static text node label in r2 to button in r1
  0x04, 1, 2,              // APPEND_CHILD r1, r2

  // Evaluates count from constants[4] and creates dynamic text node in r3
  0x07, 3, 4,              // INTERPOLATE_TEXT r3, constants[4]

  // Appends dynamic text node in r3 to button in r1
  0x04, 1, 3,              // APPEND_CHILD r1, r3

  // Returns button element in r1 as execution output
  0x00, 1                  // RETURN r1
])
```

### 3. Reactive Bindings Mapping
Finally, the compiler maps dynamic instructions to their reactive variables:

```javascript
reactiveBindings: [
  // The INTERPOLATE_TEXT instruction at PC = 13 (index 13 in the array)
  // must run again when the state variable 'count' changes
  { pc: 13, deps: ["count"] }
]
```

This mapping allows the VM to skip the first 13 bytes of instructions and jump directly to update `r3` in-place when `count` changes, avoiding a full rebuild of the button.
