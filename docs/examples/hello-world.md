# Hello World Example

This example demonstrates how to create a simple DriftJS component that declares reactive scope variables and binds them to the DOM.

---

## 📄 Component Code

Create a file named `HelloWorld.drift`:

```html
<!-- HelloWorld.drift -->
<script>
  let framework = "DriftJS";
  let greeting = "Welcome to register-based reactivity!";
</script>

<div class="hello-card">
  <h1>Hello, {framework}!</h1>
  <p>{greeting}</p>
</div>
```

---

## ⚙️ Mounting the Component

To mount the component to the DOM:

```typescript
// main.ts
import { mount } from 'driftjs-dom'
import HelloWorld from './HelloWorld.drift'

const container = document.getElementById('app');

if (container) {
  mount(HelloWorld, container);
}
```
