# DOM Updates

This page describes how the DriftJS Virtual Machine updates the DOM, focusing on text nodes, attribute patching, and event handlers.

---

## ⚡ Target-Direct Updates

In a Virtual DOM framework, updating a component involves running a full render cycle, building a new virtual tree, and comparing it against the old tree.

**DriftJS uses targeted, compile-time update paths.**

When a variable changes, the VM executes a targeted update:
1. The VM identifies which reactive bindings are associated with the changed variable.
2. It jumps directly to the bytecode instructions (`PC` locations) representing those bindings.
3. It re-evaluates the expressions and applies the changes directly to the DOM nodes.

---

## 📝 In-Place Text Node Patching

When the compiler encounters a text interpolation `{count}`, it emits an `INTERPOLATE_TEXT` instruction:

```javascript
// Bytecode representation
// 0x07 (INTERPOLATE_TEXT) <dstReg> <exprIdx>
```

### Initial Render
1. The VM executes the instruction at `PC`.
2. It evaluates `constants[exprIdx]` (e.g. `count`) against the scope.
3. It instantiates a DOM `TextNode` containing the string result.
4. It stores the `TextNode` reference in the destination register and registers a reactive binding linking the `TextNode` to the expression dependencies.

### Subsequent Updates
When `count` changes:
1. The VM loops through active reactive bindings.
2. It finds the binding pointing to the `TextNode` created at `PC`.
3. It re-evaluates the expression and updates the text node in-place:

```javascript
textNode.nodeValue = String(newValue);
```

This updates the DOM directly without needing to query elements, traverse parents, or run diffing logic.

---

## 🏷️ Fast-Path Attribute Patching

Element attributes (like class names, disabled states, or IDs) are updated using similar targets.

When compilation runs, dynamic attributes emit a `SET_ATTR` instruction:

```javascript
// Bytecode representation
// 0x05 <elemReg> <nameIdx> <valIdx> <isDynamic = 1>
```

### The Update Logic
When an attribute's dependency changes:
1. The VM re-evaluates the attribute expression.
2. It compares the new value with the cached attribute value on the VM instance.
3. If they match, the VM skips the update.
4. If different, the VM patches the attribute in-place:

```javascript
if (newValue === null || newValue === false) {
  element.removeAttribute(name);
} else {
  element.setAttribute(name, String(newValue));
}
```

### Special Class Handling
If the attribute is named `class`, DriftJS compares class lists and updates them to prevent rewriting the full string:

```javascript
// Handles boolean toggle class attributes like class={active ? 'active' : ''}
element.className = String(newValue);
```

---

## 🎯 Global Event Delegation

Instead of attaching separate event listeners to every interactive DOM element (which consumes memory and CPU cycles), DriftJS attaches a single event listener to the document root for each event type:

```javascript
// Registered once at runtime startup
document.addEventListener('click', handleDelegatedEvent);
document.addEventListener('input', handleDelegatedEvent);
```

### How Events Flow
1. When a user clicks a button, the event bubbles up to the `document` level.
2. The delegated event listener interceptor catches the event.
3. The listener traverses up the event path (from `event.target` to parent nodes) looking for elements that have a custom `drift-event` key.
4. When a matching handler is found:
   * The listener executes the associated JavaScript expression or handler function within the component's scope.
   * If the execution modifies scope variables, the VM triggers a `triggerUpdates()` cycle to apply the changes to the DOM.
