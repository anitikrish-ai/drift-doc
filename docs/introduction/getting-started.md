# Getting Started

Get up and running with DriftJS in under two minutes. This guide covers how to scaffold a new project, run a local development server, and build a production-ready application.

---

## ⚡ Prerequisites

To use DriftJS, you need:
- **Node.js** `^20.0.0` or higher (we recommend the latest LTS version)
- **pnpm** `^9.0.0` (highly recommended), or standard **npm** / **yarn** / **bun** package managers

---

## 🚀 Scaffolding a New Project

The easiest way to start a new DriftJS project is with the official interactive CLI scaffolder, `create-drift`.

Run the following command in your terminal:

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

### Interactive Setup Prompts
When you run the command, `create-drift` will guide you through setting up your project:
1. **Project name**: Enter your directory name (defaults to `my-drift-app`).
2. **Select template variant**: Choose between **TypeScript** (recommended) or **JavaScript**.
3. **Install dependencies**: Choose whether to run package installation automatically.

Once completed, navigate to your new project folder:

```bash
cd my-drift-app
```

---

## 💻 Local Development

To run the local development server with Hot Module Replacement (HMR):

::: code-group

```bash [pnpm]
pnpm dev
```

```bash [npm]
npm run dev
```

```bash [yarn]
yarn dev
```

```bash [bun]
bun dev
```

:::

The development server is powered by Vite. By default, it will open your application at `http://localhost:5173`. Any changes you make to `.drift` template files will be hot-reloaded instantly in the browser.

---

## 🏗️ Production Build

To compile your application and bundle it for production deployment:

::: code-group

```bash [pnpm]
pnpm build
```

```bash [npm]
npm run build
```

```bash [yarn]
yarn build
```

```bash [bun]
bun build
```

:::

This command triggers a production compilation. The `driftjs-vite-plugin` will compile all your `.drift` template components into lightweight, pre-compiled bytecode structures, and Vite will bundle them alongside the small `driftjs-dom` runtime into your final distribution folder (usually `/dist`).

### Previewing the Production Build
To spin up a local server previewing your compiled production output:

::: code-group

```bash [pnpm]
pnpm preview
```

```bash [npm]
npm run preview
```

```bash [yarn]
yarn preview
```

```bash [bun]
bun preview
```

:::

---

## 🎨 Next Steps

Now that you have your starter project running, you can explore:
- **[.drift Syntax Guide](/syntax/)**: Learn how to declare reactive variables, handle events, and write control directives.
- **[Register VM Concepts](/concepts/register-vm)**: Understand how DriftJS handles UI rendering without a Virtual DOM.
- **[Monorepo Architecture](/packages/)**: Peek inside the compiler, SSR runtime, and Vite plugin packages.
