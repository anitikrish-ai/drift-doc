# driftjs-shared

The `driftjs-shared` package contains utility variables and shared code utilized by the compiler, DOM runtime, and SSR packages.

---

## 🔧 Core Utility Methods

### 1. `evaluateExpression`
Evaluates Acorn JS AST nodes against a scope object. Used by both the DOM runtime and SSR packages to evaluate dynamic template expressions.

```typescript
import { evaluateExpression } from 'driftjs-shared'

// Flat component scope object
const scope = {
  user: { name: "Alex", role: "admin" },
  isAdmin() {
    return this.user.role === "admin";
  }
};

// Evaluate member expression AST node against the scope
const result = evaluateExpression(memberExprAST, scope);
console.log(result); // "admin"
```

### 2. `executeBlockStatement`
Executes JavaScript statement block AST nodes inside the component's scope. Used to initialize variables and run function declarations in `<script>` blocks.

```typescript
import { executeBlockStatement } from 'driftjs-shared'

const scope = {};
executeBlockStatement(scriptBlockAST, scope);

// Scope is populated with variables and functions declared in the AST
console.log(scope.myVar);
```

### 3. `resolveIterable`
Resolves arrays and iterable objects during `@for` loop execution:

```typescript
import { resolveIterable } from 'driftjs-shared'

const result = resolveIterable(someScopeValue); // Ensures output is a safe array
```

---

## 🔢 Exported Constants

### `MAX_REGISTERS`
Defines the register bank boundary for the Virtual Machine:

```typescript
import { MAX_REGISTERS } from 'driftjs-shared'

console.log(MAX_REGISTERS); // 256
```
