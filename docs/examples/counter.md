# Counter App Example

This example demonstrates how to build a reactive counter component that handles button click events and updates the DOM in-place.

---

## 📄 Component Code

Create a file named `Counter.drift`:

```html
<!-- Counter.drift -->
<script>
  let count = 0;

  function increment() {
    count = count + 1;
  }

  function decrement() {
    if (count > 0) {
      count = count - 1;
    }
  }

  function reset() {
    count = 0;
  }
</script>

<div class="counter-container">
  <h2>Counter Demo</h2>
  
  <div class="value-display">{count}</div>

  <div class="actions">
    <button onclick={decrement} disabled={count === 0}>- Decrease</button>
    <button onclick={increment}>+ Increase</button>
  </div>

  @if count > 10 {
    <p class="warning-alert">Warning: Count value is getting high!</p>
  }

  <button class="btn-reset" onclick={reset}>Reset</button>
</div>
```

---

## ⚙️ How It Updates

1. **State Mutation**: When a user clicks a button, the click event bubbles up to the document root and triggers the bound event handler (e.g. `increment()`), which modifies the `count` state variable.
2. **Reactivity Interception**: The VM intercepts the reassignment and runs `triggerUpdates(['count'])`.
3. **In-Place Patching**: The VM locates the dynamic bindings for `count`, jumps directly to their instruction addresses in the bytecode, and updates:
   * The text node within `.value-display` directly.
   * The `disabled` state attribute of the decrease button.
   * The conditional visibility of the high-value warning message.
