# Loops

This page explains how to render lists of elements from arrays using the `@for` directive.

---

## ⚡ Basic Loop Syntax

Use the `@for` directive to iterate through arrays and render a template for each item:

```html
<script>
  let tasks = [
    { id: 1, text: "Build DriftJS Compiler" },
    { id: 2, text: "Optimize VM Registers" },
    { id: 3, text: "Deploy Web App" }
  ];
</script>

<ul class="task-list">
  @for task in tasks {
    <li class="task-item">
      <span>{task.text}</span>
    </li>
  }
</ul>
```

---

## 🎛️ Accessing Item Indices

To access the index of the current item during iteration, wrap the item variable and index variable in parentheses `(item, index)`:

```html
<script>
  let users = ["Alice", "Bob", "Charlie"];
</script>

<div class="user-list">
  @for (user, idx) in users {
    <div class="user-row">
      <span class="user-index">#{idx + 1}</span>
      <span class="user-name">{user}</span>
    </div>
  }
</div>
```

---

## 🔄 List Reconciliation and Keyed Updates

When list data changes (e.g. items are added, removed, or reordered), updating the full list in the DOM can be slow. 

DriftJS optimizes this using a **Keyed LIS (Longest Increasing Subsequence)** reconciliation algorithm:
* **Comment Anchors**: The loop is bounded in the DOM by start and end comment nodes (`<!--for-->` and `<!--/for-->`).
* **Key Identification**: The VM identifies items using keys (or matches items by reference if keys are not present).
* **LIS Calculation**: When the array changes, the VM calculates the longest subsequence of items that did not change order.
* **Surgical Moves**: The VM leaves the matching items in place and moves or inserts only the changed elements using `insertBefore`.

This minimizes DOM manipulations during operations like sorting, swapping, and clearing lists.

---

## ⚠️ Mutating Arrays

Reactivity in DriftJS is triggered by variable assignments (`=`). 

To trigger list updates, you must assign the updated array back to the state variable:

::: code-group

```javascript [❌ Direct Mutation (No Update)]
function addTask(newText) {
  // This modifies the array but does NOT trigger a DOM update
  tasks.push({ id: Date.now(), text: newText });
}
```

```javascript [✅ Array Reassignment (Updates DOM)]
function addTask(newText) {
  // Assigning a new array reference triggers the reconciler
  tasks = [...tasks, { id: Date.now(), text: newText }];
}
```

:::
