# Attributes

This page explains how to bind static and dynamic attributes to elements in a `.drift` template.

---

## 🏷️ Static Attributes

Static attributes are declared just like standard HTML attributes:

```html
<!-- Renders static class and input type attributes -->
<input class="form-input" type="text" placeholder="Enter name" />
```

---

## ⚡ Dynamic Attributes

To bind an attribute to a dynamic state variable or expression, wrap the value in curly braces `{}`:

```html
<script>
  let id = "task-102";
  let isImportant = true;
</script>

<!-- Renders: <div id="task-102" class="task important"> -->
<div id={id} class={isImportant ? "task important" : "task"}>
```

---

## ⚙️ Boolean Attributes

If an attribute evaluates to a boolean value, DriftJS handles it dynamically:
* If the value is **truthy**, the attribute is added to the element.
* If the value is **falsy** (`false`, `null`, `undefined`), the attribute is removed from the element.

```html
<script>
  let isSending = false;
  let items = [];
</script>

<!-- If isSending is false, button is enabled. If true, disabled attribute is added. -->
<button disabled={isSending}>Send Feedback</button>

<!-- If items list is empty, button disabled attribute is added. -->
<button disabled={items.length === 0}>Clear List</button>
```

---

## 🎨 Styling and Classes

Class names can be toggled using ternary operators:

```html
<script>
  let tab = "profile";
</script>

<!-- Adds 'active' class only when tab equals 'profile' -->
<div class={tab === "profile" ? "tab active" : "tab"}>Profile</div>
<div class={tab === "settings" ? "tab active" : "tab"}>Settings</div>
```

---

## ⚡ Update Optimization

When a dynamic attribute is updated, the VM re-evaluates the expression and patches the attribute in-place. If the value hasn't changed, the VM skips the update, preventing unnecessary DOM writes.
