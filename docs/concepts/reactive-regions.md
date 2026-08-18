# Reactive Regions

This page details how DriftJS isolates dynamic blocks (like `@if` and `@for`) from static layout using comment anchors.

---

## 📍 What is a Reactive Region?

In standard Virtual DOM frameworks, a change inside a conditional statement or list requires re-running the render function for the entire containing component, diffing the old VDOM tree with the new one.

**DriftJS introduces Reactive Regions.** 

A Reactive Region is a section of the DOM bounded by special placeholder comment nodes (`<!--if-->`, `<!--for-->`) that act as anchors:

```html
<!-- DOM representation of a reactive conditional region -->
<div class="card">
  <h3>User Status</h3>
  <!--if-->
  <div class="badge badge-success">Online</div>
  <!--/if-->
</div>
```

These comment nodes serve as visual anchor coordinates in the DOM. The VM uses them to mount, unmount, swap, or re-order elements directly without needing to query parent elements or disturb surrounding nodes.

---

## 🛠️ How Comment Anchors Work

When the parser encounters a `@if` or `@for` directive:
1. It inserts a start comment node (`<!--if-->` or `<!--for-->`) and an end comment node (`<!--/if-->` or `<!--/for-->`) into the DOM instruction stream.
2. It compiles the body of the directive into a sub-module (`CompiledModule`) stored in the constant pool.
3. It registers the region in the VM's `reactiveRegions` registry, mapping the boundary comment nodes to the sub-module.

```typescript
interface ReactiveIfRegion {
  startAnchor: Comment;
  endAnchor: Comment;
  currentActiveBranch: 'consequent' | 'alternate' | null;
  consequentModuleIdx: number;
  alternateModuleIdx: number;
}
```

---

## ⚡ The Mount & Unmount Operations

When conditions evaluate, the VM performs DOM manipulation relative to the anchors:

### 1. The Clear Operation (Unmounting)
To clear a region, the VM removes all DOM elements located between `startAnchor` and `endAnchor`:

```javascript
let current = startAnchor.nextSibling;
while (current && current !== endAnchor) {
  const next = current.nextSibling;
  current.remove();
  current = next;
}
```
*Because the anchors stay physically in the DOM, the region's position is never lost.*

### 2. The Insertion Operation (Mounting)
To mount the compiled bytecode of the selected branch (e.g. the `@else` block):
1. The VM executes the sub-module's bytecode stream.
2. The sub-module returns a `DocumentFragment` containing the new DOM subtree.
3. The VM inserts this fragment into the DOM directly after the `startAnchor`:

```javascript
startAnchor.after(newFragment);
```

---

## 💎 Why Comment Nodes?

Using comment nodes for layout mapping provides three key benefits:
* **Zero Layout Impact**: Comment nodes do not affect CSS selectors (`:first-child`, `:last-child`), flexbox/grid alignments, or document styling.
* **Stable References**: Unlike dynamically generated tags, comment nodes are lightweight and remain in the DOM, serving as permanent coordinates.
* **GC Efficiency**: Bounding regions between comment anchors avoids allocating wrapper elements (like helper `<div>` tags), preventing layout pollution and keeping memory clean.
