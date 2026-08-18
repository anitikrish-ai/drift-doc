# Script & State

This page explains how to write component logic and manage state within the `<script>` block of a `.drift` file.

---

## ⚡ Reactivity Scope

Every variable declared in the top level of the `<script>` block (using `let` or `const`) automatically becomes part of the component's reactivity scope.

```html
<script>
  // These variables are reactive and visible in the template markup
  let title = "My Dashboard";
  let count = 10;
  
  // This constant is visible in the template but cannot be reassigned
  const VERSION = "1.0.0";
</script>

<div>
  <h1>{title} (v{VERSION})</h1>
  <p>Count: {count}</p>
</div>
```

---

## 🔧 Declaring Functions & Event Handlers

To handle user interactions or define helper utilities, declare standard JavaScript functions inside the script block. These functions have access to all reactive state variables.

```html
<script>
  let visitor = "Guest";
  let visits = 0;

  // Modifies visits state variable
  function registerVisit() {
    visits = visits + 1;
  }

  // Helper utility that returns a formatted string
  function getGreeting() {
    return "Welcome back, " + visitor + "!";
  }
</script>

<div>
  <h2>{getGreeting()}</h2>
  <button onclick={registerVisit}>Visits: {visits}</button>
</div>
```

---

## 🔄 State Mutation Guidelines

Reactivity in DriftJS is triggered by assignments (`=`). 

When you reassign a variable, the runtime intercepts the write and runs a `triggerUpdates()` cycle to update the DOM.

### Primitive Reassignment
Updating numbers, booleans, and strings is straightforward:
```javascript
let count = 0;
count = count + 1; // Triggers DOM updates
```

### Array & Object Reassignment
To trigger updates when modifying arrays or objects, you must assign the updated array or object reference back to the variable:

::: code-group

```javascript [Array Updates]
let items = [1, 2, 3];

// ❌ Avoid direct mutations:
items.push(4); // Will NOT update the DOM

// ✅ Assign the updated array:
items = [...items, 4]; // Triggers DOM updates
```

```javascript [Object Updates]
let user = { name: "Alex", role: "guest" };

// ❌ Avoid direct mutations:
user.role = "admin"; // Will NOT update the DOM

// ✅ Assign the updated object:
user = { ...user, role: "admin" }; // Triggers DOM updates
```

:::

By requiring explicit assignments for mutations, DriftJS avoids the overhead of proxy wrappers or tracking graphs, maintaining a lightweight runtime footprint.
