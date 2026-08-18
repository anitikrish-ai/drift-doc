# driftjs-compiler

The `driftjs-compiler` package parses `.drift` single-file templates and outputs executable `CompiledModule` bytecode structures.

---

## 🔧 Core API Methods

The compiler exposes a single primary entry point for compiling templates:

```typescript
import { interpret } from 'driftjs-compiler'

const source = `
<script>
  let message = "Hello";
</script>
<h1>{message}</h1>
`;

// Compiles template code into a CompiledModule
const compiledModule = interpret(source);

console.log(compiledModule.bytecode); // Uint8Array
console.log(compiledModule.constants); // Array of tags, ASTs, and sub-modules
```

---

## 🏛️ Internal Pipeline Classes

The compilation process is managed by four distinct pipeline classes:

### 1. `DriftLexer`
The Lexer performs lexical analysis on the template string, emitting tokens on-demand.
```typescript
import { DriftLexer } from 'driftjs-compiler'

const lexer = new DriftLexer(source);
let token = lexer.nextToken();
while (token.type !== TokenType.EOF) {
  console.log(token);
  token = lexer.nextToken();
}
```

### 2. `DriftParser`
The Parser consumes tokens from the Lexer and constructs an Abstract Syntax Tree (AST).
```typescript
import { DriftParser } from 'driftjs-compiler'

const parser = new DriftParser(lexer);
const ast = parser.parse(); // Returns ProgramNode AST
```

### 3. `DriftTransformer`
The Transformer optimizes the AST by removing formatting whitespace and parsing JavaScript string expressions into JavaScript AST structures using Acorn.
```typescript
import { DriftTransformer } from 'driftjs-compiler'

const transformer = new DriftTransformer();
const optimizedAst = transformer.transform(ast);
```

### 4. `DriftGenerator`
The Generator compiles the optimized AST nodes into low-level bytecode instruction streams, register allocations, and dependency arrays.
```typescript
import { DriftGenerator } from 'driftjs-compiler'

const generator = new DriftGenerator();
const compiledModule = generator.generate(optimizedAst);
```
