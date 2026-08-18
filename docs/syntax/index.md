# .drift Syntax

A `.drift` file is a single-file component (SFC) format that brings markup, styles, and logic together.

---

## 📐 Layout Rules

Every `.drift` component consists of two main sections:
1. **`<script>` Block**: Declares reactive state variables, event handlers, and functions.
2. **HTML Markup**: Standard HTML markup enriched with directives (`@if`, `@for`, `@switch`) and text interpolations (`{}`).

```html
<!-- Example of a standard .drift Component -->
<script>
  let status = "online";
  let username = "Developer";

  function toggleStatus() {
    status = status === "online" ? "offline" : "online";
  }
</script>

<div class="user-profile">
  <h2>Welcome, {username}!</h2>
  
  <p>Current Status: 
    <span class={status === "online" ? "status-green" : "status-gray"}>
      {status}
    </span>
  </p>

  <button onclick={toggleStatus}>Toggle Status</button>
</div>
```

---

## 🎛️ Syntax Features

* **[Script & State Scope](/syntax/script)**: Learn how variables declared in the `<script>` tag form the component's reactivity scope.
* **[Expression Interpolation](/syntax/expressions)**: Embed dynamic variables and calculations directly within your HTML elements using `{}`.
* **[Attribute Bindings](/syntax/attributes)**: Bind dynamic attributes and classes to element nodes.
* **[Event Handling](/syntax/events)**: Hook up user interactions like `onclick` or `oninput` with automatic state synchronization.
* **[Conditionals (`@if`)](/syntax/conditionals)**: Render DOM segments conditionally.
* **[Loops (`@for`)](/syntax/loops)**: Iterate through arrays using the Keyed LIS reconciler.
* **[Switch (`@switch`)](/syntax/switch)**: Pattern match conditions to render specific layout branches.
