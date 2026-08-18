# CLI Reference

This page provides the reference guide for the `create-drift` CLI command options and arguments.

---

## ⚡ Scaffolder Command

Use the CLI through your package manager:

```bash
npm create drift [project-name] [options]
```

---

## 🎛️ Command-Line Arguments

### `[project-name]`
* **Type**: `String`
* **Optional**: Yes (will prompt if omitted)
* **Description**: The directory name where your DriftJS app will be scaffolded.
* **Example**:
  ```bash
  pnpm create drift my-new-app
  ```

---

## ⚙️ Command-Line Options

### `--template`
* **Type**: `String`
* **Values**: `typescript`, `javascript`
* **Description**: Pre-selects the template variant, skipping the interactive prompt.
* **Example**:
  ```bash
  pnpm create drift my-new-app --template typescript
  ```

### `--install`
* **Type**: `Boolean`
* **Default**: `true`
* **Description**: Automatically installs dependencies after creating the files.
* **Example**:
  ```bash
  pnpm create drift my-new-app --install false
  ```

### `--help` / `-h`
* **Type**: `Flag`
* **Description**: Displays command help information and available options.
* **Example**:
  ```bash
  pnpm create drift --help
  ```

---

## 🛠️ Output Files

The CLI scaffolds the template into your project directory:

```
├── .gitignore
├── drift-env.d.ts
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
└── src/
    ├── main.ts
    ├── style.css
    └── App.drift
```
