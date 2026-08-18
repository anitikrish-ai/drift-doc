# Contributing

We welcome contributions of all kinds! Whether you want to fix bugs, optimize opcode execution, improve compiler error reporting, write developer tools, or expand test coverage, this guide will help you get started.

---

## 💻 Repository Setup

### 1. Prerequisites
Ensure you have the following installed on your machine:
* **Node.js** `^20.0.0` or higher
* **pnpm** `^9.0.0` or higher (monorepo workspaces require pnpm)

### 2. Clone and Install
Clone the repository and install dependencies:

```bash
# Clone the repository
git clone https://github.com/hrutavmodha/driftjs.git
cd driftjs

# Install dependencies across all packages
pnpm install
```

---

## 🏗️ Development Scripts

Manage the monorepo workspace using these workspace scripts:

### Build Packages
Compile all packages in the correct order:
```bash
pnpm build
```

### Run Tests
Execute the Vitest test suites across the monorepo:
```bash
pnpm test
```

### Type Checking
Run TypeScript type-checks across all packages:
```bash
pnpm typecheck
```

### Run the Playground
Launch the local playground to test changes in an interactive starter application:
```bash
cd template
pnpm dev
```

---

## 🔄 Contribution Workflow

1. **Create a Feature Branch**:
   ```bash
   git checkout -b feature/my-feature-name
   ```
2. **Implement Changes**: Write code, tests, and documentation.
3. **Verify Checks**: Ensure all tests and type checks pass:
   ```bash
   pnpm test
   ```
4. **Commit Changes**: Use clear, descriptive commit messages.
5. **Open a Pull Request**: Submit your pull request to the main repository, detailing your changes and design decisions.
