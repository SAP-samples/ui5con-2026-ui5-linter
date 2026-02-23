# Section 2: Running UI5 Linter - First Analysis (10 min)

[← Previous: Introduction & Setup](01-introduction-setup.md) | [Back to Overview](00-overview.md)

---

## 2.1 Run the Linter

```bash
# Run UI5 linter
ui5lint
```

## 2.2 Expected Findings in This Demo App

The linter should detect **53 problems** (41 errors, 12 warnings) in these categories:

### Deprecated APIs (`no-deprecated-api`)
- `jQuery.sap.declare()` - legacy module declaration
- `jQuery.sap.require()` - legacy module loading
- `jQuery.sap.log.info()` - deprecated logging API
- `jQuery.sap.getModulePath()` - deprecated path resolution
- `jQuery.sap.extend()` - deprecated object merging
- `jQuery.sap.formatMessage()` - deprecated string formatting
- `sap.ui.controller()` - legacy controller definition
- `sap.ui.getCore().byId()` - deprecated Core access
- `.control()` - deprecated jQuery extension for getting UI5 controls
- `tap` event - deprecated event name (use `press`)
- Bootstrap attributes with camelCase spelling (e.g., `resourceRoots` → `resource-roots`)
- `synchronizationMode` - deprecated OData V4 model parameter
- `sap.ui5/resources/js` - deprecated manifest property

### Global Variables (`no-globals`)
- Direct access to `sap.ui.demo.todo.*` namespace
- Usage of `jQuery` global for DOM manipulation
- Access to `sap.ui.core.BarColor` without import
- Access to `sap.ui.model.Filter` and `sap.ui.model.FilterOperator` without import
- Access to `sap.ui.model.json.JSONModel` without import

### Deprecated Libraries (`no-deprecated-library`)
- `sap.ui.commons` - deprecated library in manifest.json and ui5.yaml

### Deprecated Themes (`no-deprecated-theme`)
- `sap_goldreflection` - deprecated theme in multiple HTML files

### Manifest Issues (`no-outdated-manifest-version`)
- Outdated manifest version

### Event Handler Issues (`no-ambiguous-event-handler`)
- `tap="onClearCompleted"` - missing `.` prefix for local handlers

### Content Security Policy (`csp-unsafe-inline-script`)
- Inline `<script>` tag in index.html

### Test Configuration (`prefer-test-starter`)
- Legacy QUnit test setup in multiple test files

## 2.3 Working with the Output

Run the linter with the `--details` flag to get additional context for each finding:

```bash
ui5lint --details
```

```
webapp/controller/App.controller.js
   1:1  error Use of deprecated API 'jQuery.sap.declare'. Details: since 1.58. To avoid usage of global variables in general, please do not use the jQuery.sap namespace any longer. Most of the jQuery.sap functionalities are replaced by alternative modules which can be found in the API doc.  no-deprecated-api
   11:8  error Call to deprecated function 'controller' (sap.ui.controller). Details: As of version 1.56. use Controller.extend (https://ui5.sap.com/1.136/#/api/sap.ui.core.mvc.Controller%23methods/sap.ui.core.mvc.Controller.extend) to define the controller class and Controller.create (https://ui5.sap.com/1.136/#/api/sap.ui.core.mvc.Controller) to create controller instances. For further information, see sap.ui.core.mvc.Controller (https://ui5.sap.com/1.136/#/api/sap.ui.core.mvc.Controller).  no-deprecated-api
   16:19 error Access of global variable 'sap' (sap.ui.core.BarColor). Details: Do not use global variables to access UI5 modules or APIs. See Best Practices for Developers (https://ui5.sap.com/#/topic/28fcd55b04654977b63dacbee0552712)  no-globals
   ...
```

For many findings, helpful links to the UI5 API documentation and best practices are provided. Use these resources to understand the issue and how to fix it.

> [!TIP]
> 💡 **Exercise:** Run the linter and check the total number of problems. Get an overview over what kind of problems exist in the app. Use `--details` to get more information. Maybe you can already get an idea on how to fix some of the issues?

---

[← Previous: Introduction & Setup](01-introduction-setup.md) | [Back to Overview](00-overview.md) | [Next: Understanding Linter Rules →](03-understanding-rules.md)
