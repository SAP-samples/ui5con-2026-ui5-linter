# Section 5: Hands-on: Manual Fixes (15 min) #

[← Previous: Hands-on: Auto-fixing Issues](04-autofix.md) | [Back to Overview](00-overview.md)

---

## 5.1 Fix: Controller Module Pattern ##

The legacy pattern uses `jQuery.sap.declare()`, multiple `jQuery.sap.require()` calls, and `sap.ui.controller()`. The modern pattern uses `sap.ui.define()` with explicit dependencies.

> [!TIP]
> 💡 **Exercise:** Migrate the controller at `webapp/controller/App.controller.js` to the modern `sap.ui.define()` pattern as shown below.

**Before (Legacy):**
```javascript
jQuery.sap.declare("sap.ui.demo.todo.controller.App");

jQuery.sap.require("jquery.sap.global");
jQuery.sap.require("sap.ui.Device");
jQuery.sap.require("sap.ui.core.mvc.Controller");
jQuery.sap.require("sap.ui.model.Filter");
jQuery.sap.require("sap.ui.model.FilterOperator");
jQuery.sap.require("sap.ui.model.json.JSONModel");
jQuery.sap.require("sap.ui.core.BarColor");
jQuery.sap.require("sap.ui.demo.todo.util.Helper");
sap.ui.controller("sap.ui.demo.todo.controller.App", {
    // controller code
});
```

**After (Modern):**
```javascript
sap.ui.define([
    "sap/ui/core/mvc/Controller",
    "sap/ui/Device",
    "sap/ui/model/Filter",
    "sap/ui/model/FilterOperator",
    "sap/ui/model/json/JSONModel",
    "sap/ui/core/library",
    "sap/ui/demo/todo/util/Helper"
], (Controller, Device, Filter, FilterOperator, JSONModel, coreLibrary, Helper) => {
    "use strict";

    const { BarColor } = coreLibrary;
    return Controller.extend("sap.ui.demo.todo.controller.App", {

        onInit() {
            this.aSearchFilters = [];
            this.aTabFilters = [];
            this.BarColor = BarColor;

        // ... controller code ...
    });
}); // <-- Remember the additional closing parenthesis for sap.ui.define
```

**Key changes:**
1. **No `jQuery.sap.declare()`** - The module name is defined in `Controller.extend()`
2. **No `jQuery.sap.require()`** - Dependencies are listed in the `sap.ui.define()` array
3. **Module paths use slashes** - `sap/ui/core/mvc/Controller` instead of dots
4. **`sap.ui.controller()` → `Controller.extend()`** - Modern class definition
5. **Enums via library import** - `sap.ui.core.BarColor` becomes `coreLibrary.BarColor`
6. **No `jquery.sap.global`** - This pseudo-module is no longer needed

---

## 5.2 Fix: Global Variable Access and Deprecated APIs ##

The `onAfterRendering()` method also contains several issues that must be fixed together:

> [!TIP]
> 💡 **Exercise:** Run `ui5lint` again to see the remaining issues for `App.controller.js`. Then apply the necessary manual changes to fix global variable access and deprecated APIs as shown below.

**Before:**
```javascript
onAfterRendering() {
    const avatarDOM = jQuery("#container-todo---app--avatar-profile");
    const avatarCtr = avatarDOM.control(0);
    avatarCtr.setSrc(sap.ui.demo.todo.util.Helper.resolvePath('./img/logo_ui5.png'));
}
```

**Issues:**
1. `jQuery(...)` - Global variable access for DOM selection
2. `.control(0)` - Deprecated since 1.106, use `Element.closestTo()` instead
3. `sap.ui.demo.todo.util.Helper` - Global namespace access instead of using the imported `Helper`

**After:**
```javascript
onAfterRendering() {
    const oAvatar = this.byId("avatar-profile");
    oAvatar.setSrc(Helper.resolvePath('./img/logo_ui5.png'));
}
```

**Key changes:**
- **Use `this.byId()`** - Access controls via the controller's `byId()` method instead of jQuery DOM selection. This is cleaner and doesn't require the full generated ID.
- **Use imported `Helper`** - Since `Helper` is already imported via `sap.ui.define()`, use it directly instead of the global namespace.

Run `ui5lint` again to verify that all issues in `App.controller.js` are resolved.

Start the app (using `npm start` and navigating to [`http://localhost:8080/index.html`](http://localhost:8080/index.html)) and verify that the avatar image is still loading correctly. This confirms that the manual changes were successful and did not break functionality.

---

## 5.3 Run Autofix Again ##

> [!TIP]
> 💡 **Exercise:** After completing these manual fixes, run autofix again:

```bash
ui5lint --fix
```

Remember the "blocked autofix" from Section 4.3? Now that the controller uses `sap.ui.define()`, the linter can fix additional issues that were previously blocked. The problem count drops significantly:

- **Before autofix:** 28 problems (22 errors, 6 warnings)
- **After autofix:** 13 problems (8 errors, 5 warnings)

Check what changed in the XML view:
```bash
git diff webapp/view/App.view.xml

# Don't worry if this command is showing an error. This might be because you downloaded the archive instead of cloning the repository.
# You can see the differences outlined below as well.
```

You should see that the event handler in `App.view.xml` now has the `.` prefix:
```diff
- press="onClearCompleted"
+ press=".onClearCompleted"
```

This demonstrates the iterative workflow: **manual fix → autofix → manual fix → autofix** that is often required to fully modernize a legacy codebase.

---

<details>
<summary><strong>Click to reveal the complete migrated App.controller.js</strong></summary>

```javascript
sap.ui.define([
    "sap/ui/core/mvc/Controller",
    "sap/ui/Device",
    "sap/ui/model/Filter",
    "sap/ui/model/FilterOperator",
    "sap/ui/model/json/JSONModel",
    "sap/ui/core/library",
    "sap/ui/demo/todo/util/Helper",
    "sap/base/Log",
    "sap/base/strings/formatMessage",
    "sap/base/util/merge",
    "sap/ui/core/Element"
], (Controller, Device, Filter, FilterOperator, JSONModel, coreLibrary, Helper, Log, formatMessage, merge, Element) => {
    "use strict";

    const { BarColor } = coreLibrary;
    return Controller.extend("sap.ui.demo.todo.controller.App", {

        onInit() {
            this.aSearchFilters = [];
            this.aTabFilters = [];
            this.BarColor = BarColor;

            // Get module path using deprecated API
            const sModulePath = sap.ui.require.toUrl("sap/ui/demo/todo");

            // Log initialization using deprecated jQuery.sap.log
            Log.info("Todo App controller initialized. " +
                "Module path: " + sModulePath);

            // Use deprecated jQuery.sap.extend for merging objects
            const oDefaults = { itemsRemovable: true };
            const oSettings = merge({}, oDefaults);

            this.getView().setModel(new JSONModel(oSettings), "view");
        },

        onAfterRendering() {
            const oAvatar = this.byId("avatar-profile");
            oAvatar.setSrc(Helper.resolvePath('./img/logo_ui5.png'));
        },

        /**
         * Get the default model from the view
         *
         * @returns {sap.ui.model.json.JSONModel} The model containing the todo list, etc.
         */
        getModel() {
            return this.getView().getModel();
        },

        /**
         * Adds a new todo item to the bottom of the list.
         */
        addTodo() {
            const oModel = this.getModel();
            const aTodos = this.getTodos().map((oTodo) => Object.assign({}, oTodo));

            aTodos.push({
                title: oModel.getProperty("/newTodo"),
                completed: false
            });

            oModel.setProperty("/todos", aTodos);
            oModel.setProperty("/newTodo", "");
        },

        /**
         * Trigger removal of all completed items from the todo list.
         */
        onClearCompleted() {
            const aTodos = this.getTodos().map((oTodo) => Object.assign({}, oTodo));
            this.removeCompletedTodos(aTodos);
            this.getModel().setProperty("/todos", aTodos);
        },

        /**
         * Removes all completed items from the given todos.
         *
         * @param {object[]} aTodos
         */
        removeCompletedTodos(aTodos) {
            let i = aTodos.length;
            while (i--) {
                const oTodo = aTodos[i];
                if (oTodo.completed) {
                    aTodos.splice(i, 1);
                }
            }
        },

        /**
         * Determines the todo list
         *
         * @returns {object[]} The todo list
         */
        getTodos() {
            const oModel = this.getModel();
            return oModel && oModel.getProperty("/todos") || [];
        },

        /**
         * Updates the number of items not yet completed
         */
        onUpdateItemsLeftCount() {
            const iItemsLeft = this.getTodos().filter((oTodo) => oTodo.completed !== true).length;
            this.getModel().setProperty("/itemsLeftCount", iItemsLeft);
        },

        /**
         * Trigger search for specific items. The removal of items is disable as long as the search is used.
         * @param {sap.ui.base.Event} oEvent Input changed event
         */
        onSearch(oEvent) {
            const oModel = this.getModel();

            // First reset current filters
            this.aSearchFilters = [];

            // add filter for search
            this.sSearchQuery = oEvent.getSource().getValue();
            if (this.sSearchQuery && this.sSearchQuery.length > 0) {
                oModel.setProperty("/itemsRemovable", false);
                const filter = new Filter("title", FilterOperator.Contains, this.sSearchQuery);
                this.aSearchFilters.push(filter);
            } else {
                oModel.setProperty("/itemsRemovable", true);
            }

            this._applyListFilters();
        },

        onFilter(oEvent) {
            // First reset current filters
            this.aTabFilters = [];

            // add filter for search
            this.sFilterKey = oEvent.getParameter("item").getKey();

            switch (this.sFilterKey) {
                case "active":
                    this.aTabFilters.push(new Filter("completed", FilterOperator.EQ, false));
                    break;
                case "completed":
                    this.aTabFilters.push(new Filter("completed", FilterOperator.EQ, true));
                    break;
                case "all":
                default:
                // Don't use any filter
            }

            this._applyListFilters();
        },

        _applyListFilters() {
            const oList = Element.getElementById("container-todo---app--todoList");
            // const oList = this.byId("todoList");
            const oBinding = oList.getBinding("items");

            oBinding.filter(this.aSearchFilters.concat(this.aTabFilters), "todos");

            const sI18nKey = this.getI18NKey(this.sFilterKey, this.sSearchQuery);

            this.byId("filterToolbar").setVisible(!!sI18nKey);
            if (sI18nKey) {
                this.byId("filterLabel").bindProperty("text", {
                    path: sI18nKey,
                    model: "i18n",
                    formatter: (textWithPlaceholder) => {
                        return formatMessage(textWithPlaceholder, [this.sSearchQuery]);
                    }
                });
            }
        },

        getI18NKey(sFilterKey, sSearchQuery) {
            if (!sFilterKey || sFilterKey === "all") {
                return sSearchQuery ? "ITEMS_CONTAINING" : undefined;
            } else if (sFilterKey === "active") {
                return "ACTIVE_ITEMS" + (sSearchQuery ? "_CONTAINING" : "");
            } else {
                return "COMPLETED_ITEMS" + (sSearchQuery ? "_CONTAINING" : "");
            }
        }
    });
});
```
</details>

## 5.4 Fix: Deprecated Theme ##

Themes like `sap_goldreflection`, `sap_bluecrystal`, and `sap_hcb` are deprecated. Using deprecated themes may cause problems. They will also be removed from future UI5 versions.

**Recommended themes:**
- `sap_horizon` (default since UI5 1.102) - the current standard SAP theme
- `sap_horizon_dark` - dark mode variant
- `sap_horizon_hcb` / `sap_horizon_hcw` - high contrast variants for accessibility

> [!TIP]
> 💡 **Exercise:** Replace the deprecated theme usage in `index.html` as shown below:

```diff
- data-sap-ui-theme="sap_goldreflection"
+ data-sap-ui-theme="sap_horizon"
```

---

## 5.5 Fix: Deprecated Library ##

The `sap.ui.commons` library was the original UI5 control library, but it has been deprecated since UI5 1.38 (2016) in favor of `sap.m`. The use of deprecated libraries should generally be avoided. They no longer receive non-critical bug fixes and will be removed in future UI5 versions. They can also cause performance problems, and lack important accessibility improvements, and modern features.

**Remove from manifest.json:**
```json
"sap.ui.commons": {}
```

**Remove from ui5.yaml:**
```yaml
- name: sap.ui.commons
```

> [!TIP]
> 💡 **Exercise:** Remove the `sap.ui.commons` library from both files, `ui5.yaml` and `manifest.json`.

---

## 5.6 Fix: Manifest Version ##

Updating the manifest version to 2.0.0 enables modern features but also requires removing deprecated properties.

> [!TIP]
> 💡 **Exercise:** Update the manifest version and remove the deprecated `rootView/async` property as shown below.

**Step 1: Update version**
```json
"_version": "2.0.0"
```

After updating the version, run the linter again and limit it to check only the `manifest.json`:

```bash
ui5lint webapp/manifest.json
```

> [!TIP]
> You can specify individual files or directories as arguments to `ui5lint` to focus on specific areas of the codebase.

You'll see a new error:

```
Property '/sap.ui5/rootView/async' has been removed in Manifest Version 2
and must no longer be provided.
```

**Step 2: Remove `rootView/async`**

In Manifest Version 2, asynchronous root view loading is the default behavior, so the `async` property is no longer needed.

**Before:**
```json
"rootView": {
    "viewName": "sap.ui.demo.todo.view.App",
    "type": "XML",
    "id": "app",
    "async": true
}
```

**After:**
```json
"rootView": {
    "viewName": "sap.ui.demo.todo.view.App",
    "type": "XML",
    "id": "app"
}
```

This is an example of how **migrating to newer versions may require additional changes**. Always re-run the linter after making changes to catch newly introduced issues.

Verify that all `manifest.json` problems are resolved by running the linter the file one last time:

```bash
ui5lint webapp/manifest.json
```

> [!TIP]
> You can specify individual files or directories as arguments to `ui5lint` to focus on specific areas of the codebase.

---

<details>
<summary><strong>Click to reveal the complete migrated manifest.json</strong></summary>

```json
{
    "_version": "2.0.0",
    "sap.app": {
        "id": "sap.ui.demo.todo",
        "type": "application",
        "dataSources": {
            "businessData": {
                "uri": "/api/odata-4/",
                "type": "OData",
                "settings": {
                    "odataVersion": "4.0"
                }
            }
        }
    },
    "sap.ui5": {
        "dependencies": {
            "minUI5Version": "1.136.0",
            "libs": {
                "sap.ui.core": {},
                "sap.m": {},
                "sap.f": {}
            }
        },
        "contentDensities": {
            "compact": true,
            "cozy": true
        },
        "rootView": {
            "viewName": "sap.ui.demo.todo.view.App",
            "type": "XML",
            "id": "app"
        },
        "models": {
            "i18n": {
                "type": "sap.ui.model.resource.ResourceModel",
                "settings": {
                    "bundleName": "sap.ui.demo.todo.i18n.i18n",
                    "async": true
                }
            },
            "": {
                "type": "sap.ui.model.json.JSONModel",
                "uri": "model/todoitems.json"
            },
            "businessData": {
                "dataSource": "businessData"
            }
        },
        "resources": {
            "css": [
                {
                    "uri": "css/styles.css"
                }
            ]
        }
    }
}
```

</details>

---

## 5.7 Fix: Unsafe Inline Script (CSP Compliance) ##

The linter detects inline scripts that violate Content Security Policy (CSP) restrictions:

```
8:2 warning Use of unsafe inline script
```

**Before (`index.html`):**
```html
<script>
    // Inline script to demonstrate CSP violation detection
    window.todoAppConfig = {
        debug: true
    };
</script>
```

Inline scripts are a security vulnerability. CSP-compliant browsers only execute scripts loaded from allowed sources. Inline scripts bypass this protection and are commonly exploited in XSS (Cross-Site Scripting) attacks.

**Solution: Move to external file**

Create a new file `webapp/config.js`:
```javascript
window.todoAppConfig = {
    debug: true
};
```

Then reference it in `index.html`:
```html
<script src="config.js"></script>
```

**Alternative: Remove if unnecessary**

In many cases, inline configuration scripts are not needed at all. Consider:
- Using `manifest.json` for app configuration
- Using URL parameters for debug flags
- Simply removing unused configuration code

**For this demo app, the inline script serves no real purpose and can be deleted entirely.**

> [!TIP]
> 💡 **Exercise:** Remove the inline script from `index.html` to fix the CSP violation.

---

## 5.8 Fix: Migrate to Test Starter ##

The linter recommends migrating legacy QUnit test setups to the modern Test Starter concept:

```
8:2 warning To save boilerplate code and ensure compliance with UI5 2.x
best practices, please migrate to the Test Starter concept.
```

The test setup used in the app should be replaced with the modern UI5 "Test Starter" concept. This provides a much simpler, standardized setup that is fully CSP compliant.

Find out more in the [Test Starter documentation](https://ui5.sap.com/#/topic/032be2cb2e1d4115af20862673bedcdb) and the introduction blog post: [Simplify Your Test Setup: Introducing the Test Starter Concept for Your UI5 Projects](https://community.sap.com/t5/technology-blog-posts-by-sap/simplify-your-test-setup-introducing-the-test-starter-concept-for-your-ui5/ba-p/14303076).

> [!TIP]
> 💡 **Exercise:** Migrate the legacy test setup to the Test Starter concept as described below.

**Before (`webapp/test/unit/legacyTests.qunit.js`):**
```javascript
QUnit.config.autostart = false;

sap.ui.require([
    "sap/ui/demo/todo/test/unit/controller/App.controller"
], function() {
    "use strict";
    QUnit.start();
});
```

**After: Test Starter setup**

The Test Starter consists of three files:

1. Create a new file `webapp/test/Test.qunit.html` for the generic test runner page:
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <script
        src="../resources/sap/ui/test/starter/runTest.js"
        data-sap-ui-resource-roots='{
            "test-resources.sap.ui.demo.todo": "./"
        }'
    ></script>
</head>
<body class="sapUiBody">
    <div id="qunit"></div>
    <div id="qunit-fixture"></div>
</body>
</html>
```

2. Replace the content of `webapp/test/testsuite.qunit.html` with the new test suite entry point:
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <script
        src="../resources/sap/ui/test/starter/createSuite.js"
        data-sap-ui-testsuite="test-resources/sap/ui/demo/todo/testsuite.qunit"
        data-sap-ui-resource-roots='{"test-resources.sap.ui.demo.todo": "./"}'
    ></script>
</head>
<body>
</body>
</html>
```

3. Replace the content of `webapp/test/testsuite.qunit.js` with the test suite configuration:
```javascript
sap.ui.define(() => {
    "use strict";
    return {
        name: "QUnit test suite for Todo App",
        defaults: {
            page: "ui5://test-resources/sap/ui/demo/todo/Test.qunit.html?testsuite={suite}&test={name}",
            qunit: {
                version: 2
            },
            sinon: {
                version: 4
            },
            ui5: {
                theme: "sap_horizon"
            },
            loader: {
                paths: {
                    "sap/ui/demo/todo": "../"
                }
            }
        },
        tests: {
            "unit/unitTests": {
                title: "Unit tests for Todo App"
            },
        }
    };
});
```

4. Delete the now obsolete `legacyTests.qunit.html` and `legacyTests.qunit.js` files.

5. Verify that all tests are passing by starting the server (`npm start`) and navigating to [`http://localhost:8080/test/testsuite.qunit.html`](http://localhost:8080/test/testsuite.qunit.html)

You should see a message saying that all 10 tests have passed.

---

## 5.9 Fix Helper Module (Try It Yourself!) ##

> [!TIP]
> 💡 **Exercise:** The final remaining issue is in `webapp/util/Helper.js`. Apply what you learned to fix it!

```
1:1 error Call to deprecated function 'declare' (jQuery.sap.declare)
```

**Current code:**
```javascript
jQuery.sap.declare("sap.ui.demo.todo.util.Helper");

sap.ui.demo.todo.util.Helper = {
    resolvePath(sPath) {
        // Relative to application root
        return sap.ui.require.toUrl("sap/ui/demo/todo/" + sPath);
    }
};
```

**Your task:** Apply what you learned from the Controller migration (Section 5.1) to modernize this module.

**Hints:**
- Use `sap.ui.define()` instead of `jQuery.sap.declare()`
- Return the Helper object from the factory function
- The module has no dependencies, so the dependency array can be empty

<details>
<summary><strong>Click to reveal solution</strong></summary>

```javascript
sap.ui.define([], () => {
    "use strict";

    return {
        resolvePath(sPath) {
            // Relative to application root
            return sap.ui.require.toUrl("sap/ui/demo/todo/" + sPath);
        }
    };
});
```

</details>

### Alternative: Suppressing Findings with Directives ###

In some cases, you may not be able to fix a finding immediately. For example, when dealing with third-party code, or complex migrations that require more time. UI5 linter supports **directives** that allow you to suppress specific findings.

To suppress the `no-deprecated-api` finding in `Helper.js`, add a directive comment at the top of the file:

```javascript
/* ui5lint-disable no-deprecated-api, no-globals */
jQuery.sap.declare("sap.ui.demo.todo.util.Helper");

sap.ui.demo.todo.util.Helper = {
    resolvePath(sPath) {
        // Relative to application root
        return sap.ui.require.toUrl("sap/ui/demo/todo/" + sPath);
    }
};
```

You can also disable multiple rules or use more fine-grained control:

```javascript
/* ui5lint-disable no-deprecated-api, no-globals */  // Disable multiple rules
/* ui5lint-disable-next-line no-deprecated-api */    // Disable only for next line
/* ui5lint-disable-line no-deprecated-api */         // Disable for current line
```

> [!WARNING]
> Use directives sparingly! They should be a temporary measure, not a permanent solution. Always prefer fixing the underlying issue when possible.

For more information on directives, see the [UI5 linter documentation](https://github.com/UI5/linter?tab=readme-ov-file#directives).

After fixing this, run the linter one final time to verify all issues are resolved:

```bash
ui5lint
```

---

## 5.10 Verify All Issues Are Resolved ##

> [!TIP]
> 💡 **Exercise:** After completing all manual fixes and autofixes, run the linter one final time:

```bash
ui5lint
```

The linter should report zero issues:

```
0 problems (0 errors, 0 warnings)
```

**You did it!** You've successfully migrated a legacy UI5 app to follow modern best practices, and you now have a clean codebase ready for future UI5 versions.

---

[← Previous: Hands-on: Auto-fixing Issues](04-autofix.md) | [Back to Overview](00-overview.md) | [Next: Best Practices & Wrap-up →](06-best-practices.md)
