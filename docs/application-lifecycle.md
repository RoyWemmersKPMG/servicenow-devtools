# Application lifecycle

DevTools is designed around source-controlled applications with scripted, repeatable installation rather than manual update-set deployment.

## Scaffold a regular application

1. Create the scoped application.
2. Create a single-function Script Include named `AppBuilder` in that application.
3. Insert the `AppBuilder` syntax macro or use this pattern:

   ```javascript
   function AppBuilder() {
       x_snc_devtools.AppBuilder(
           'x_your_scope',
           'x_optional_parent_scope',
           'Optional menu separator',
           20000
       );
   }
   ```

4. Remove optional arguments that do not apply.
5. Run `AppBuilder()` as a background script in the target application scope.
6. Review every generated application file and adjust ownership, descriptions, roles, menus, tests, and installation behavior.
7. Commit only after ATF and sanity checks pass.

`AppBuilder()` is intended to be mostly idempotent, but generated output still needs review after upgrades.

## Platform application pattern

A platform application represents the target instance as a whole and declares dependencies on the applications that comprise it. There should normally be one per platform estate or deployment pipeline.

```javascript
function AppBuilder() {
    x_snc_devtools.AppBuilderPlatform('x_your_platform_scope');
}
```

After generation:

- add all required application dependencies and minimum versions;
- implement `DevToolsGetInstancePipelineName()` to map technical instance identities to stable names such as `dev`, `test`, and `prod`;
- place environment-specific installation behavior in `AppInstall()` rather than scattering hostname checks through business logic;
- review the generated Nightly QA and other automation before activation.

## Installation scripts

An application can provide a single-function Script Include named `AppInstall`. Run it through:

```javascript
x_snc_devtools.InstallApp('x_your_scope');
```

`InstallApp(vApp, bStopOnError)` resolves dependencies and runs their installers before the requested application's installer. Build installers with `InstallerAPI`; its methods cover properties, records, dictionary configuration, choices, forms, lists, UI Policies, activation, transported packages, and other repeatable operations.

Installer rules:

- make every step safe to rerun;
- fail clearly when a prerequisite is absent;
- use the `strReason` arguments to explain why a change is required;
- avoid embedding credentials or instance URLs;
- distinguish clean install, upgrade, and post-install repair;
- call `Finish()` and review its errors;
- test on a clone representative of the target.

## Baseline a release

The **Baseline Version** action automates a release boundary. It checks for uncommitted work and an existing version branch, runs app sanity/default/baseline hooks, removes the work-in-progress suffix, creates a branch named for the version, increments the minor version, and restores the work-in-progress suffix.

Before starting:

- all ATF tests in the default suite pass;
- Instance Scan/CodeSanity findings are resolved or accepted;
- `AppInstall()` has been tested on clean and upgrade paths;
- dependencies and minimum versions are correct;
- release notes include the current version where the manual uses a Release Notes section;
- no secrets or environment-specific credentials are in application files;
- the branch naming rule and provider permissions are confirmed.

After baselining, protect the version branch against direct push and deletion. Treat it as immutable release evidence.

## Shared applications

Create a **Shared Application** record for externally maintained apps. It lets your organization override maintainer, documentation, and repository information and changes baseline behavior so upstream versions are not treated as your own new release.

For every local modification, decide explicitly whether to:

- contribute it upstream and wait for a release;
- carry a local patch with a documented rebase/backport obligation; or
- fork and assume full maintenance ownership.

Record that decision as technical debt or release documentation.

## Moving application files between scopes

`GetRelatedRecordsTree()` and `GetRelatedRecordsList()` can help inventory a complex structure, while `AppBuilderMoveToScope()` can move it. This is high risk. Platform features may link records in ways the helper does not know, and table names embed scope.

Before a move, back up application files and business data, inspect the full related-record tree, keep `bMoveFromAllScopes=false` and `bMoveDataModel=false` unless a reviewed migration demands otherwise, and validate the result on a disposable clone.

