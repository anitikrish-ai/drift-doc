# Expressions

This page describes how to write dynamic JavaScript expressions inside a `.drift` component template.

---

## ⚡ Basic Interpolation

To output dynamic values in your markup, wrap standard JavaScript expressions in curly braces `{}`:

```html
<script>
  let user = "Alex";
</script>

<!-- Renders: <h1>Welcome, Alex!</h1> -->
<h1>Welcome, {user}!</h1>
```

---

## 🎛️ Supported Expression Formats

DriftJS parses expression content into an AST using Acorn. It supports a wide range of JavaScript expressions, including:

### 1. Operations & Calculations
You can perform math and string operations directly inside the template:
```html
<p>Next Year: {year + 1}</p>
<p>Full Name: {firstName + ' ' + lastName}</p>
```

### 2. Method Calls & Property Access
Call methods on objects, access array lengths, or inspect object properties:
```html
<p>Total Items: {items.length}</p>
<p>Uppercase Name: {user.toUpperCase()}</p>
<p>User Email: {profile.contact.email}</p>
```

### 3. Ternary Operators
Ternary operators are useful for conditional classes or text content:
```html
<p>Status: {active ? "Connected" : "Disconnected"}</p>
```

### 4. Arrow Functions
You can write inline arrow functions, which is common for array operations:
```html
<p>Pending Count: {tasks.filter(t => !t.done).length}</p>
```

---

## 🚫 Current Constraints

Because DriftJS relies on static AST parsing and code generation to compile templates, there are a few restrictions to keep in mind:

* **No Variable Declarations**: You cannot declare new variables inside interpolations (e.g. `{ let x = 10; x }` is invalid).
* **No Control Flow Statements**: Loops or conditional blocks cannot be written inside interpolations (use `@if` and `@for` directives instead).
* **No Complex Operations**: Avoid writing multi-statement blocks inside interpolations. If an operation requires more than one line, define it as a helper function inside the `<script>` block and call it instead:

::: code-group

```html [❌ Avoid Complex Logic]
<p>Score Grade: {
  score >= 90 ? 'A' : (score >= 80 ? 'B' : 'C')
}</p>
```

```html [✅ Use Helper Functions]
<script>
  let score = 85;

  function getGrade(value) {
    if (value >= 90) return 'A';
    if (value >= 80) return 'B';
    return 'C';
  }
</script>

<p>Score Grade: {getGrade(score)}</p>
```

:::
