# Dependencies and technical debt

## Application dependencies

DevTools stores dependency records in `x_snc_devtools_dependency`. A dependency connects a source application to a required application/package and can carry version requirements, repository and documentation details, and a reason.

On a `sys_app` form, switch to the DevTools view and use the Dependencies related list. The **Dependencies** action renders both a tree and a flattened deployment plan.

For each dependency, document:

- the technical application identity, not only its display name;
- the minimum compatible version or supported range;
- why the dependency exists;
- its authoritative repository and documentation;
- whether it is installed from Store, App Repo, source control, or another channel;
- any plugin or licensing prerequisite.

Before deployment, refresh/synchronize dependency information, inspect cycles and missing versions, then install in dependency-first order. `InstallApp()` uses this model to run installation scripts in order, but it cannot activate every plugin or resolve licensing on your behalf.

Useful APIs include `AppGetDependencies()`, `GetDependencyListFromTree()`, `GetDependencyTreeErrorCount()`, `CompareDependencyStatus()`, `CompareVersion()`, and `AppSyncAllDependencies()`.

## Platform applications and pipeline names

A platform application is the root of an instance's dependency tree. Use stable pipeline labels (`dev`, `test`, `prod`) from `GetInstancePipelineName()` instead of hard-coding instance subdomains. This keeps shared code portable across clones, sandboxes, and multiple production instances.

## Shared applications

Shared Application records distinguish upstream ownership from local stewardship. Keep maintainer, documentation, and repository fields current. If local changes diverge from upstream, record the reason and the future merge/backport work.

## Technical debt records

`x_snc_devtools_technical_debt` extends `sys_metadata`, so debt records travel with the application. Use them for unfinished, defective, deprecated, or operationally burdensome behavior that must be visible at deployment time.

Each record should have a precise short description, detailed impact, type, owner or backlog URL, and a retirement condition. Do not use technical debt records to conceal active incidents or security vulnerabilities.

### Feature switch pattern

For debt associated with a feature that must be disabled or conditionally enabled, assign an ID and Boolean switch:

```javascript
if (x_snc_devtools.GetTechnicalDebtSwitchState(
    'x_your_scope',
    'legacy_calculation'
)) {
    // Deliberately selected temporary behavior.
}
```

The function returns `false` if the record does not exist. Choose switch semantics carefully: the code should make it obvious whether `true` enables a workaround, hides a feature, or selects legacy behavior. Add an ATF test for both states.

## Update sets as migration debt

DevTools can pack a completed update set and its siblings into Record Transporter packages. This is explicitly a fallback for changes that cannot yet be represented as application files. The process creates technical debt reminders. Use it as a bridge away from update sets, not as the default deployment model.

