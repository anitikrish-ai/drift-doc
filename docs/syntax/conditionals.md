# Conditionals

This page explains how to render DOM templates conditionally using `@if`, `@else if`, and `@else` directives.

---

## ⚡ Basic Conditional Syntax

Use the `@if` directive to render blocks of HTML based on reactive conditions. If the condition evaluates to **truthy**, the block is rendered; if **falsy**, it is unmounted.

```html
<script>
  let visible = true;

  function toggle() {
    visible = !visible;
  }
</script>

<button onclick={toggle}>Toggle Display</button>

@if visible {
  <div class="content-box">
    <h3>Dynamic Content</h3>
    <p>This paragraph is shown only when visible is true.</p>
  </div>
}
```

---

## 🎛️ `@else if` and `@else` Branches

You can chain multiple conditional branches together to handle complex states:

```html
<script>
  let status = "loading"; // "loading", "success", or "error"

  function setStatus(newStatus) {
    status = newStatus;
  }
</script>

<div class="controls">
  <button onclick={ () => setStatus("loading") }>Load</button>
  <button onclick={ () => setStatus("success") }>Success</button>
  <button onclick={ () => setStatus("error") }>Error</button>
</div>

<!-- Conditional branches -->
@if status === "loading" {
  <p class="status loading">Loading data from API...</p>
}
@else if status === "success" {
  <p class="status success">Data loaded successfully! 🎉</p>
}
@else {
  <p class="status error">Failed to load data. Please retry.</p>
}
```

---

## ⚙️ How It Works in the DOM

Unlike some frameworks that use CSS toggles (`display: none`) to hide elements, DriftJS physically mounts and unmounts conditional elements in the DOM.

To optimize this process:
1. The compiler wraps the `@if` block in a `REACTIVE_IF` instruction and creates boundary comment nodes (`<!--if-->` and `<!--/if-->`) in the template.
2. During rendering, the VM uses these comment nodes as anchors.
3. If a condition changes:
   * The VM removes all DOM elements located between the start and end anchors.
   * It executes the compiled bytecode sub-module representing the active branch.
   * It inserts the new DOM nodes directly after the start anchor.

This ensures conditional updates are isolated, preventing layout shift and avoiding full-page re-renders.
