# Section 3: Understanding Linter Rules (5 min)

[← Previous: Running UI5 Linter](02-running-linter.md) | [Back to Overview](00-overview.md)

---

## 3.1 Rule Categories

UI5 linter rules can be categorized into these groups. Some rules have "autofix" capabilities, meaning that UI5 linter can automatically apply certain transformations to fix the issue. However, not all issues can be autofixed. Some require manual code changes due to complexity or potential side effects.

**UI5 linter autofixes are designed for maximum compatibility and safety.** They will only apply transformations that are well-defined and have a low risk of breaking existing functionality.

### Code Quality Rules

| Rule | Description | Auto-fixable |
|------|-------------|--------------|
| `no-deprecated-api` | Deprecated UI5 APIs | Partially |
| `no-globals` | Global variable usage | Partially |
| `no-pseudo-modules` | Pseudo module imports | No |

### Manifest Rules

| Rule | Description | Auto-fixable |
|------|-------------|--------------|
| `no-deprecated-library` | Deprecated library dependencies | No |
| `no-outdated-manifest-version` | Old manifest version format | No |

### XML View Rules

| Rule | Description | Auto-fixable |
|------|-------------|--------------|
| `no-ambiguous-event-handler` | Ambiguous event handler names | Yes |

Finding details and the complete list of rules in the documentation:
- [UI5 linter: Rules Documentation](https://github.com/UI5/linter/blob/main/docs/Rules.md)
- [UI5 linter: Scope of Autofix Documentation](https://github.com/UI5/linter/blob/main/docs/Scope-of-Autofix.md)

## 3.2 Why These Rules Matter

- **Deprecated APIs and Global variables:** Will be removed in future UI5 versions
- **Sync loading:** Blocks the browser and hurts performance, might cause problems with strict Content Security Policies (CSP)
- **Deprecated libraries:** Don't receive updates and will be removed in future UI5 versions

---

[← Previous: Running UI5 Linter](02-running-linter.md) | [Back to Overview](00-overview.md) | [Next: Hands-on: Auto-fixing Issues →](04-autofix.md)
