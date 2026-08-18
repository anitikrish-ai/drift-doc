# Events

This page describes how to handle user interactions and events in a `.drift` component.

---

## ⚡ Basic Event Binding

Events are bound to elements using standard event properties (like `onclick` or `oninput`) with the handler wrapped in curly braces `{}`:

```html
<script>
  let count = 0;

  function handleClick() {
    count = count + 1;
  }
</script>

<!-- Calls handleClick when clicked -->
<button onclick={handleClick}>Clicked {count} times</button>
```

---

## 🔄 Inline Arrow Functions & Parameters

You can write inline arrow functions to pass arguments to your handlers directly from the template:

```html
<script>
  let message = "No button clicked yet";

  function setButtonMessage(num) {
    message = "Button " + num + " clicked!";
  }
</script>

<p>{message}</p>

<!-- Pass arguments using inline arrow functions -->
<button onclick={ () => setButtonMessage(1) }>Button 1</button>
<button onclick={ () => setButtonMessage(2) }>Button 2</button>
```

---

## ⚙️ Event Object Access

To access the native browser event object (like `MouseEvent` or `InputEvent`), pass it to your handler function:

```html
<script>
  let query = "";

  // Event object is passed from the inline arrow function
  function handleInput(event) {
    query = event.target.value;
  }
</script>

<div>
  <!-- Capture input event and pass event object -->
  <input type="text" oninput={ (e) => handleInput(e) } placeholder="Search..." />
  <p>Search Query: {query}</p>
</div>
```

---

## 🎯 Central Event Delegation

DriftJS optimizes memory usage and event setup speed by using a central event delegation system:
1. **Single Listener**: The runtime attaches a single event listener to the root `document` for each event type (such as `click` or `input`).
2. **Event Bubbling**: When a user interacts with an element, the event bubbles up to the root.
3. **Route & Match**: The event listener interceptor catches the event, travels up the element path to find a matching event handler, and executes it in the component scope.
4. **Auto Update**: If the handler function modifies any scope variables, the VM runs a `triggerUpdates()` cycle to apply the changes to the DOM automatically.
