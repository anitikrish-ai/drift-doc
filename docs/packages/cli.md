# create-drift (CLI)

The `create-drift` package is the command-line interface (CLI) tool used to scaffold new DriftJS applications.

---

## 🔧 Scaffolding Command

Run the scaffolder using your preferred package manager:

::: code-group

```bash [pnpm]
pnpm create drift my-drift-app
```

```bash [npm]
npm create drift my-drift-app
```

```bash [yarn]
yarn create drift my-drift-app
```

```bash [bun]
bun create drift my-drift-app
```

:::

---

## ⚙️ Interactive Prompts

When run, the CLI guides you through project setup:

### 1. Project Directory
* **Prompt**: `Project name: (my-drift-app)`
* **Description**: Enter the folder name for your project. If the directory does not exist, the CLI will create it.

### 2. Template Variant
* **Prompt**: `Select template variant:`
  * `typescript` (recommended)
  * `javascript`
* **Description**: Choose between TypeScript (adds type definitions and strict checks) or vanilla JavaScript.

### 3. Automatic Dependency Installation
* **Prompt**: `Install dependencies? (Y/n)`
* **Description**: Installs packages automatically using the package manager that launched the CLI.

---

## 🏛️ Template Files Created

The CLI creates a starter project containing:
* **`index.html`**: Host document containing the target container `<div id="app"></div>`.
* **`vite.config.ts`**: Pre-configured Vite configuration registering `driftPlugin`.
* **`src/main.ts`**: Entry point that imports and mounts the root component.
* **`src/App.drift`**: Starter counter component.
* **`src/style.css`**: Global stylesheet.
* **`drift-env.d.ts`**: TypeScript declaration file.
