# vscode-plugin

The `drift-vscode` package is the official Visual Studio Code extension for DriftJS. It provides syntax highlighting and basic diagnostics check for `.drift` templates.

---

## ⚡ Features

### 1. Syntax Highlighting
* Highlights top-level `<script>` blocks as JavaScript/TypeScript.
* Highlights template control flow directives (`@if`, `@else if`, `@else`, `@for`, `@switch`, `@case`, `@default`).
* Highlights text interpolations (`{}`) and dynamic attributes within HTML tags.

### 2. Error Diagnostics
* **Mismatched Tags**: Highlights unclosed tags or mismatched elements (e.g. `<div></span>`) with visual squiggly lines.
* **Directive Syntax Warnings**: Flags invalid loop declarations (like `@for item items` missing the `in` keyword) or unclosed block brackets.

---

## 🛠️ Manual Installation

To compile and install the extension package locally:

### 1. Build the Extension
Navigate to the VS Code plugin directory and build the package:

```bash
cd packages/vscode-plugin
pnpm install
pnpm run compile
```

### 2. Package into a VSIX file
Compile the extension into a installable `.vsix` file using `vsce`:

```bash
pnpm exec vsce package
```

### 3. Install in VS Code
Install the compiled `.vsix` file in VS Code:

```bash
code --install-extension drift-vscode-0.0.1.vsix
```
*Alternatively, open VS Code, open the Extensions pane, click the three-dot menu (...) in the top-right, choose **Install from VSIX...**, and select the generated file.*
