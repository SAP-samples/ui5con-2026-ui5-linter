# Section 4: Hands-on: Auto-fixing Issues (10 min)

[← Previous: Understanding Linter Rules](03-understanding-rules.md) | [Back to Overview](00-overview.md)

---

## 4.1 Using the Autofix Feature

> [!TIP]
> 💡 **Exercise:** Run the UI5 linter with the `--fix` flag to automatically apply fixes for certain issues:

```bash
ui5lint --fix
```

Still **43 problems** remaining! But soon there will be less. Let's first check what changed.

## 4.2 What Autofix Can Fix

The UI5 linter autofix can automatically transform several types of issues. Let's take a look at what changed in the app:

> [!TIP]
> 💡 **Exercise:** Observe what the autofix has changed. Compare before/after with:

```bash
git diff
```

### 1. Deprecated API Replacements

**`jQuery.sap.getModulePath()` → `sap.ui.require.toUrl()`** (`App.controller.js`)

```diff
- const sModulePath = jQuery.sap.getModulePath("sap/ui/demo/todo");
+ const sModulePath = sap.ui.require.toUrl("sap/ui/demo/todo");
```

The `jQuery.sap.getModulePath()` API was deprecated in UI5 1.58. The modern replacement `sap.ui.require.toUrl()` provides the same functionality for resolving module paths to URLs.

### 2. Bootstrap Attribute Naming (`index.html`)

Camel-case bootstrap attributes are deprecated in favor of kebab-case:

```diff
- data-sap-ui-bindingSyntax="complex"
```
```diff
- data-sap-ui-resourceRoots='{...}'
+ data-sap-ui-resource-roots='{...}'
```
```diff
- data-sap-ui-onInit="module:sap/ui/core/ComponentSupport"
+ data-sap-ui-on-init="module:sap/ui/core/ComponentSupport"
```
```diff
- data-sap-ui-compatVersion="edge"
+ data-sap-ui-compat-version="edge"
```

Note: The `bindingSyntax` attribute is removed entirely because `complex` binding syntax is the default since UI5 1.28.

### 3. Deprecated Manifest Properties (`manifest.json`)

**`synchronizationMode` removed:**
```diff
  "businessData": {
      "dataSource": "businessData",
-     "settings": {
-         "synchronizationMode": "None"
-     }
  }
```

The `synchronizationMode` parameter for OData V4 models is deprecated and should not be used.

**`sap.ui5/resources/js` removed:**
```diff
  "resources": {
      "css": [...]
-     "js": []
  }
```

The `js` section under `sap.ui5/resources` is deprecated. Use regular dependencies in `Component.js` instead.

### 4. Deprecated Event Names (`App.view.xml`)

```diff
- <Button ... tap="onClearCompleted"/>
+ <Button ... press="onClearCompleted"/>
```

The `tap` event was deprecated in favor of `press` for better cross-device compatibility.

## 4.3 What Autofix Cannot Fix

Some issues cannot be fixed automatically:

- Code outside `sap.ui.define`/`sap.ui.require` blocks
- Synchronous-to-asynchronous flow changes
- Complex multi-step API migrations
- Manifest.json structural changes

### Blocked Autofixes

Some autofixes are **blocked by other issues** that must be fixed manually first. A notable example in this demo app:

**`no-ambiguous-event-handler`** - The event handler `tap="onClearCompleted"` should become `press=".onClearCompleted"` (note the `.` prefix). However, notice that autofix only changed `tap` to `press` (which was an independent **no-deprecated-api** finding), but did **not** add the `.` prefix:

```diff
- tap="onClearCompleted"
+ press="onClearCompleted"   ← Still missing the "." prefix!
```

**Why?** The `.` prefix indicates that the handler is a method on the controller. UI5 linter can only safely add this prefix when the controller uses the modern `sap.ui.define()` pattern. Since `App.controller.js` still uses the legacy `sap.ui.controller()` pattern (line 11), the linter cannot verify the controller structure and skips this part of the fix.

**Resolution:** After manually migrating the controller to `sap.ui.define()` (see Section 5.1), running autofix again will add the missing `.` prefix. This demonstrates that **fixing issues in the correct order matters**.

---

[← Previous: Understanding Linter Rules](03-understanding-rules.md) | [Back to Overview](00-overview.md) | [Next: Hands-on: Manual Fixes →](05-manual-fixes.md)
