# Task Tracker Example

This example demonstrates a complete DriftJS component that combines state scope management, list rendering (`@for`), conditional blocks (`@if`), pattern matching (`@switch`), and keyed list reconciliation.

---

## 📄 Component Code

Create a file named `TaskTracker.drift`:

```html
<!-- TaskTracker.drift -->
<script>
  let newTaskTitle = "";
  let priority = "medium";
  let tasks = [
    { id: 101, title: "Configure Vite Plugin", priority: "high", done: true },
    { id: 102, title: "Optimize VM Registers", priority: "high", done: false },
    { id: 103, title: "Write Benchmarks", priority: "medium", done: false }
  ];

  function toggleTask(id) {
    // Re-assign the array to trigger updates
    tasks = tasks.map(t => t.id === id ? { ...t, done: !t.done } : t);
  }

  function deleteTask(id) {
    // Filter out the task and re-assign the array
    tasks = tasks.filter(t => t.id !== id);
  }

  function addTask() {
    if (newTaskTitle.trim() === "") return;
    
    const newTask = {
      id: Date.now(),
      title: newTaskTitle,
      priority: priority,
      done: false
    };

    tasks = [...tasks, newTask];
    newTaskTitle = ""; // Reset input value
  }

  function handleInput(event) {
    newTaskTitle = event.target.value;
  }

  function handlePriorityChange(event) {
    priority = event.target.value;
  }
</script>

<div class="app-container">
  <header class="app-header">
    <h1>Task Board</h1>
    <span class="counter">Pending: {tasks.filter(t => !t.done).length} / {tasks.length}</span>
  </header>

  <!-- Task input controls -->
  <div class="task-form">
    <input 
      type="text" 
      oninput={ (e) => handleInput(e) } 
      value={newTaskTitle} 
      placeholder="What needs to be done?" 
    />
    
    <select onchange={ (e) => handlePriorityChange(e) }>
      <option value="low">Low Priority</option>
      <option value="medium" selected>Medium Priority</option>
      <option value="high">High Priority</option>
    </select>

    <button onclick={addTask}>Add Task</button>
  </div>

  <!-- Conditional display of task list or empty state -->
  @if tasks.length === 0 {
    <div class="empty-state">
      <p>🎉 All tasks are completed! Enjoy your day.</p>
    </div>
  }
  @else {
    <ul class="task-list">
      <!-- Keyed loop iteration -->
      @for (task, idx) in tasks {
        <li class={task.done ? "task-item completed" : "task-item"}>
          <span class="task-num">#{idx + 1}</span>
          <span class="task-title">{task.title}</span>

          <!-- Pattern matching element badges -->
          @switch task.priority {
            @case "high" { <span class="badge badge-red">High</span> }
            @case "medium" { <span class="badge badge-amber">Medium</span> }
            @default { <span class="badge badge-gray">Low</span> }
          }

          <div class="item-actions">
            <button onclick={ () => toggleTask(task.id) }>
              {task.done ? "Undo" : "Complete"}
            </button>
            <button class="btn-danger" onclick={ () => deleteTask(task.id) }>Delete</button>
          </div>
        </li>
      }
    </ul>
  }
</div>
```
