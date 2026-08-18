# Switch

This page describes how to use pattern matching inside templates using the `@switch`, `@case`, and `@default` directives.

---

## ⚡ Basic Switch Syntax

The `@switch` directive evaluates a discriminant expression and renders the matching `@case` branch. If no case matches, the optional `@default` branch is rendered instead.

```html
<script>
  let view = "home"; // "home", "profile", or "settings"

  function setView(target) {
    view = target;
  }
</script>

<div class="tabs">
  <button onclick={ () => setView("home") }>Home</button>
  <button onclick={ () => setView("profile") }>Profile</button>
  <button onclick={ () => setView("settings") }>Settings</button>
</div>

<!-- Switch directive matching view state -->
@switch view {
  @case "home" {
    <div class="panel">
      <h3>Home Panel</h3>
      <p>Welcome to your homepage.</p>
    </div>
  }
  @case "profile" {
    <div class="panel">
      <h3>User Profile</h3>
      <p>Manage your account settings here.</p>
    </div>
  }
  @default {
    <div class="panel">
      <h3>System Settings</h3>
      <p>Configure app integrations and properties.</p>
    </div>
  }
}
```

---

## ⚙️ How It Works

* **Parser Bounds**: The parser checks case elements inside the switch container, ensuring only valid directive statements exist as children.
* **Anchor Positioning**: Similar to conditionals, the VM places boundary comment anchors around the switch block.
* **Surgical Switching**: When the view variable changes, the VM clears the active case elements and mounts the newly matched branch, preserving the rest of the DOM structure.
