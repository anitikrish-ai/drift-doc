# Reconciliation

This page details the list reconciliation algorithm inside DriftJS, explaining how the Longest Increasing Subsequence (LIS) algorithm optimizes `@for` loop updates.

---

## 🔄 The Keyed List Problem

When rendering arrays of items inside a loop (`@for`), the array data can change in several ways: items can be added, removed, re-ordered, swapped, or replaced.

Without a reconciliation system, the simplest way to update the DOM is to delete all existing list items and recreate them from scratch. However, this is highly inefficient and causes issues like loss of input focus, reset scroll states, and broken CSS animations.

To solve this, frameworks use **keyed reconciliation**. By assigning a unique key to each item (e.g. `{id: 101}`), the framework can match updated items with existing DOM nodes and apply only the necessary changes.

---

## ⚡ Longest Increasing Subsequence (LIS)

DriftJS implements a **Longest Increasing Subsequence (LIS)** algorithm to handle list updates. 

The LIS algorithm identifies the largest group of items that do not need to be moved relative to each other. By keeping these elements in place, the VM minimizes the number of physical DOM movements.

### Step-by-Step Example

Let's say we have an initial list of items:
* **Old Keys**: `[A, B, C, D, E, F]`

A state update swaps the items into a new order:
* **New Keys**: `[A, C, D, B, E, F]` (B has been moved after D)

Here is how the DriftJS reconciler resolves the update:

```
1. Map new keys to indices:
   A:0, C:1, D:2, B:3, E:4, F:5

2. Build index mapping array (matching old items to their new positions):
   Old array: [A, B, C, D, E, F]
   New positions: [0, 3, 1, 2, 4, 5]

3. Find the Longest Increasing Subsequence (LIS) of new positions:
   Subsequence of [0, 3, 1, 2, 4, 5] is [0, 1, 2, 4, 5]
   These values correspond to items: [A, C, D, E, F]

4. Apply the reconciliation:
   - The LIS tells us that A, C, D, E, and F do not need to move relative to each other.
   - Only B needs to be moved.
   - The VM performs a single DOM operation: moves item B and inserts it after D.
```

By calculating the LIS, the VM resolves the update with **1 physical DOM insertion** instead of re-ordering multiple elements, ensuring optimal performance.

---

## 🛠️ The `reconcileKeyedList` Implementation

The LIS reconciliation logic is implemented in the `reconcileKeyedList` function inside `packages/dom/src/reconciler.ts`.

It runs through four main phases during an update:

```
                  reconcileKeyedList
                          │
                          ▼
            [ Phase 1: Sync Prefix / Suffix ]
         (Fast-path matches at start and end of lists)
                          │
                          ▼
             [ Phase 2: Unmount Removed ]
           (Remove old items not in new list)
                          │
                          ▼
             [ Phase 3: Map New Elements ]
         (Track positions of remaining items)
                          │
                          ▼
              [ Phase 4: LIS & Reorder ]
         (Calculate LIS and move items into place)
```

### 1. Sync Prefix / Suffix (Fast Path)
Before running the full LIS algorithm, the reconciler checks the start and end of the lists for matching items. If items are only added or removed at the edges, they are patched in-place, skipping the need for an LIS calculation.

### 2. Map New Elements
If the lists differ in the middle, the reconciler builds a key-index mapping of the new items to track their positions.

### 3. Calculate LIS & Move
The reconciler calculates the LIS on the mapped positions and iterates backward through the new list. If an item's index is not in the LIS, the VM moves the corresponding DOM node into place using `insertBefore`.
