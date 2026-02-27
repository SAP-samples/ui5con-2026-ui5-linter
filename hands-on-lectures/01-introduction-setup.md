# Section 1: Introduction & Setup (5 min)

[← Back to Overview](00-overview.md)

---

## 1.1 What is UI5 Linter?

UI5 linter is a static code analysis tool that helps you:
- Detect deprecated UI5 APIs and patterns
- Identify global variable usage
- Find synchronous loading patterns
- Ensure Content Security Policy (CSP) compliance
- Validate manifest.json configuration

## 1.2 Install UI5 Linter

> **Note:** If you receive an error like `npm: command not found`, you need to install Node.js first. Download and install it from [nodejs.org](https://nodejs.org/) (LTS version recommended).

```bash
# Install UI5 linter globally (provides the ui5lint command)
npm install -g @ui5/linter

# Verify installation
ui5lint --version
```

## 1.3 Project Setup

```bash
# Clone the demo project
git clone git@github.com:SAP-samples/ui5con-2026-ui5-linter.git
cd ui5con-2026-ui5-linter

# Install dependencies (includes UI5 linter as dev dependency)
npm install

# Start the UI5 CLI server to verify that the app works
npm start
```

Open the app in your browser: [`http://localhost:8080/index.html`](http://localhost:8080/index.html)

If the `git clone` command fails, you can also download the repository as a ZIP file [here](https://github.com/SAP-samples/ui5con-2026-ui5-linter/archive/refs/heads/main.zip).

---

[← Back to Overview](00-overview.md) | [Next: Running UI5 Linter →](02-running-linter.md)
