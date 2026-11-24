# Using the API

DevTools exposes server-side single-function Script Includes, class-style Script Includes, a client-callable proxy, UI Scripts, and editor macros. The [Script Include catalog](reference/script-includes.md) is the searchable authority for names, scope access, and descriptions in this checkout.

## Calling conventions

From another application scope, call a public API by its qualified name:

```javascript
var type = x_snc_devtools.GetType(value);
var text = x_snc_devtools.RenderValue(value);
```

Instantiate a public class with `new`:

```javascript
var installer = new x_snc_devtools.InstallerAPI('My Application');
```

`package_private` includes are internal implementation details. Do not call them from consumer applications. A public API can still be powerful, synchronous, destructive, or dependent on plugins; public means callable, not universally safe.

## Core value recipes

### Safe nested access

```javascript
var payload = { request: { caller: { email: 'dev@example.com' } } };
var email = x_snc_devtools.GetValue(payload, 'request', 'caller', 'email');
x_snc_devtools.SetValue('high', payload, 'request', 'priority');
```

### Compare and clone structured values

```javascript
var copy = x_snc_devtools.Clone(payload, true);
var same = x_snc_devtools.IsEqual(payload, copy, false);
```

### Render diagnostics

```javascript
gs.info(x_snc_devtools.RenderValue(payload));
```

Do not render objects containing secrets or personal data into logs.

## HTTP requests

`HttpRequest(vInput)` accepts either a URL or a parameter object with URL, method, body, auth/credential, response mode, headers, and optional MID Server. Prefer credential references over inline secrets, set explicit timeouts where the underlying configuration allows, validate TLS, and avoid logging bodies.

```javascript
var body = x_snc_devtools.HttpRequest({
    url: 'https://api.example.com/health',
    method: 'get',
    headers: [{ name: 'Accept', value: 'application/json' }]
});
```

For repeated provider operations, prefer the purpose-built `GitHubAPI` or `AzureDevOpsAPI` class and review its method list in the catalog.

## Installation and application APIs

- `AppBuilder()` / `AppBuilderPlatform()` create conventional scaffolding.
- `InstallApp()` resolves dependency installers.
- `InstallerAPI` applies repeatable configuration.
- `AppSanity()` normalizes known application problems.
- `AppVersionBaseline()` automates version baselining and is high impact.
- `SourceControlCommitToCurrentBranch()` commits current changes and must be governed like any other source-control write.

See [Application lifecycle](application-lifecycle.md) before using these APIs.

## Testing APIs

- `TestDataAPI` creates and cleans prefixed test data.
- `TestAPI` supports scripted assertions and application-specific tests.
- `CreateTestForRecord()`, `MakeTestScriptForRecord()`, and `AddScriptTestStep()` generate scaffolding.
- `InstanceScanSuiteOnApp()` runs a scan suite for an app.

## Cross-scope APIs

`RunScriptInScope(scope, script, ...)` and `RunScriptInGlobalScope(script, ...)` place the supplied script in a function body. Assign to `result` to return a value; local declarations do not escape. These APIs bypass normal scope friction and require the legacy cross-scope property. Use only after security review and never with untrusted script text.

```javascript
var answer = x_snc_devtools.RunScriptInGlobalScope(
    "result = String(param1).toUpperCase();",
    'safe input'
);
```

## Business-process generation

`BusinessProcessGetExample()` returns the expected JSON shape. `BusinessProcessValidate(definition, true)` returns detailed errors, warnings, and notes. `AppBuilderImplementBusinessProcess(app, definition)` generates a task-derived table, states, roles, ACLs, business rules, UI actions, and a `BusinessProcessAPI` subclass.

Treat generation as a design accelerator. Review state authorization, ACLs, cancellation paths, re-entry, concurrency, audit behavior, translations, and data migration before use.

## LLM prompting

`Prompt(text)` uses the configured Generic Prompt Capability. Install and configure the required Now Assist capability, choose the approved model, and configure its connection/credential alias first. Do not send secrets, regulated data, or records outside the approved AI data boundary. Add failure handling for unavailable capability, policy rejection, timeout, and malformed output.

## Client-side APIs

Global UI Scripts provide client equivalents for selected value helpers plus navigation, dialogs, DOM observation, progress tracking, and server requests. The `DevToolsClientAPI` Script Include is the GlideAjax-facing proxy for supported operations.

Classic UI support does not imply Workspace, Polaris, or Service Portal support. Check the [UI Script catalog](reference/ui-scripts.md), test in the actual experience, and avoid depending on global browser names when a supported component API exists.

## Choosing an API

1. Search the catalog by task and inspect the exact description.
2. Confirm `public` access and server/client context.
3. Inspect the implementation and ATF test in this version.
4. Identify platform/plugin, role, property, and cross-scope prerequisites.
5. Exercise success, missing-record, authorization, and error paths in a sub-production instance.
6. Pin the DevTools minimum version in the consumer application's dependency record.

