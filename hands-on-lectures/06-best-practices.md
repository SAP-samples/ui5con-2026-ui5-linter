# Section 6: Best Practices & Wrap-up

[← Previous: Hands-on: Manual Fixes](05-manual-fixes.md) | [Back to Overview](00-overview.md)

---

To make sure you catch issues early and maintain code quality, it's important to integrate UI5 linter into your development workflow. Here we provide some best practices for doing so.

## 6.1 Integrating UI5 Linter in Your Workflow

Add convenience scripts to your `package.json`:

```json
// package.json
{
  "scripts": {
    "lint": "eslint webapp && ui5lint",
    "lint:fix": "ui5lint --fix"
  }
}
```

You cna then run these scripts with:

```bash
npm run lint       # Run linter checks
npm run lint:fix   # Run linter with autofix
```

## 6.2 CI/CD Integration

Integrate UI5 linter into your CI/CD pipeline to ensure no new linter issues are introduced. For example, in a [GitHub Actions](https://docs.github.com/en/actions) workflow:

```yaml
# GitHub Actions example
- name: Run UI5 Linter
  run: ui5lint
```

## 6.3 Key Takeaways

1. **Run linter regularly** - Catch issues early in development
2. **Use autofix cautiously** - Review changes before committing
3. **Prioritize fixes** - Focus on deprecated APIs that will be removed soon
4. **Update incrementally** - Don't try to fix everything at once
5. **Test after changes** - Ensure functionality still works

## 6.4 Resources

- [UI5 Linter GitHub](https://github.com/UI5/linter)
- [UI5 Linter Rules Documentation](https://github.com/UI5/linter/blob/main/docs/Rules.md)
- [UI5 Deprecation Information](https://ui5.sap.com/#/api/deprecated)
- [UI5 Best Practices](https://ui5.sap.com/#/topic/28fcd55b04654977b63dacbee0552712)

---

[← Previous: Hands-on: Manual Fixes](05-manual-fixes.md) | [Back to Overview](00-overview.md) | [Appendix: Legacy Patterns →](07-appendix.md)
