# Compiler Pipeline

This page describes the five compilation stages that transform `.drift` template source code into executable bytecode modules.

---

## 📐 Compilation Workflow

The compilation pipeline operates as a linear transformation flow:

```
.drift Source File
   │
   ▼
[ DriftLexer ]         # Tokenizes characters into semantic token streams
   │
   ▼
[ DriftParser ]        # Parses tokens into an Abstract Syntax Tree (AST)
   │
   ▼
[ DriftTransformer ]   # Cleans whitespace and enriches expressions via Acorn
   │
   ▼
[ DriftGenerator ]     # Emits 15-opcode bytecode, constant pool, and bindings
   │
   ▼
CompiledModule Object
```

---

## 🎛️ Compilation Stages

### 1. Tokenization (`DriftLexer`)
The compiler reads the `.drift` file as raw text. The Lexer tokenizes the content on-demand (lazily) to keep memory usage low.
* **Lexical States**: The Lexer switches states (e.g. `Data`, `TagOpen`, `BeforeAttributeName`, `Interpolation`) as it encounters tags, comments, and JavaScript expressions.
* **JS Code Extraction**: Braces `{}` inside text content trigger the extraction of raw JS code blocks. The Lexer tracks nesting depth (to handle JS object literals like `{ user: { name: 'Alex' } }` correctly).

### 2. AST Construction (`DriftParser`)
The Parser consumes tokens lazily from the Lexer and constructs an Abstract Syntax Tree (AST).
* **AST Node Types**: The AST contains structured nodes representing elements (`ElementNode`), plain text (`TextNode`), expressions (`InterpolatedTextNode`), and control directives (`IfNode`, `ForNode`, `SwitchNode`).
* **Tag Matching**: The parser checks for correct HTML tag nesting and returns compiler errors if mismatched tags (e.g. `<div><span></div></span>`) are found.

### 3. Syntax Transformation (`DriftTransformer`)
Before generating bytecode, the AST undergoes two optimization passes:
* **Whitespace Pruning**: Removes redundant formatting spaces, empty text nodes, and double newlines between HTML elements to optimize rendering.
* **Expression Parsing**: Converts raw string expressions (like `items.length === 0`) into JavaScript AST representations using the Acorn parser. This validates JS syntax before code generation.

### 4. Bytecode Emission (`DriftGenerator`)
The final stage translates the optimized AST nodes into low-level instructions.
* **Register Allocation**: The generator maps DOM nodes and evaluated values to register coordinates (`r0`–`r255`). When temporary nodes are appended to parents, their registers are marked as available for reuse.
* **Constant Extraction**: Static strings (like element tag names) and JS ASTs are moved to a constant pool (`constants`), replacing inline data with index references.
* **Instruction Generation**: Emits a `Uint8Array` bytecode stream containing instructions like `CREATE_ELEMENT`, `SET_ATTR`, and `REACTIVE_IF`.

---

## 📦 The Compiled Output (`CompiledModule`)

The compiler returns a `CompiledModule` object, which is sent to the client VM:

```typescript
interface CompiledModule {
  // Constant pool storing tag strings, script ASTs, and sub-modules
  constants: any[];
  
  // Uint8Array bytecode instruction stream
  bytecode: Uint8Array;
  
  // Maps instruction offsets to state variables for targeted updates
  reactiveBindings: Array<{ pc: number; deps: string[] }>;
}
```
*Because the output is structured data with no active functions, it is serializable and can be sent directly from a server or stored in a cache.*
