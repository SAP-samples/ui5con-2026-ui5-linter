![OpenUI5 logo](http://openui5.org/images/OpenUI5_new_big_side.png)

# UI5 linter Workshop - Sample App

> **UI5con 2026 Bengaluru Hands-on Workshop:** Modernizing Legacy UI5 Applications with UI5 Linter

This repository contains the demo project for the **UI5 linter Hands-On** at [UI5con 2026](https://openui5.org/ui5con/). It is a variant of the OpenUI5 Todo sample application available at [github.com/UI5/sample-app](https://github.com/UI5/sample-app).

## Workshop Materials

**Start here:** [hands-on-lectures/00-overview.md](hands-on-lectures/00-overview.md)

The workshop is divided into the following sections:

1. [Introduction & Setup](hands-on-lectures/01-introduction-setup.md) (5 min)
2. [Running UI5 Linter - First Analysis](hands-on-lectures/02-running-linter.md) (10 min)
3. [Understanding Linter Rules](hands-on-lectures/03-understanding-rules.md) (5 min)
4. [Hands-on: Auto-fixing Issues](hands-on-lectures/04-autofix.md) (10 min)
5. [Hands-on: Manual Fixes](hands-on-lectures/05-manual-fixes.md) (15 min)
6. [Best Practices & Wrap-up](hands-on-lectures/06-best-practices.md) (optional/informational)

Reference: [Appendix - Legacy Patterns](hands-on-lectures/07-appendix.md)

## About This Demo Project

> [!IMPORTANT]
> This project **intentionally contains legacy code patterns** for educational purposes. Attendees will identify and fix these issues step-by-step during the workshop using the [UI5 linter](https://github.com/UI5/linter).

**Do not use this code as a reference for best practices!** The legacy patterns include:
- Deprecated APIs (`jQuery.sap.*`, `sap.ui.controller()`, etc.)
- Global variable access
- Deprecated themes and libraries
- CSP-violating inline scripts
- Legacy test setup

Checkout [github.com/UI5/sample-app](https://github.com/UI5/sample-app) for the original version of this application, following best practices and using modern API.

### Final Result

The modernized version of this application (with all linter issues resolved) is available on the **[`final-result`](../../tree/final-result)** branch.
