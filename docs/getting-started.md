# Getting started

## Before you install

The application metadata declares scope `x_snc_devtools`, version `1.147.0`, ECMAScript mode `es_latest`, permissive runtime access tracking, and an `admin`-oriented operating model. The author's documentation lists the Zurich or Australia ServiceNow releases and access to GitHub as requirements for this version.

Prepare:

- a non-production ServiceNow instance for the first installation;
- an administrator account;
- a GitHub account, a fork of the repository, and a classic personal access token with repository access;
- a Basic Auth credential record in ServiceNow for source control;
- permission from your security owner to enable the legacy cross-scope policy described below;
- backups or a clone strategy appropriate to your release process.

Do not begin on production. Installation changes platform configuration and the test suite can create records.

## Security decision: cross-scope execution

Many DevTools capabilities depend on the global Boolean property:

```text
glide.record.legacy_cross_scope_access_policy_in_script=true
```

Create it in global scope if it does not exist. This enables techniques used by `RunScriptInScope()` and `RunScriptInGlobalScope()`. It is a material security and governance decision, not a routine preference. Record the approval, affected instances, owner, and rollback approach. See [Security and risk](security-and-risk.md).

## Install from source control

1. Fork the upstream repository into a repository controlled by your organization.
2. In ServiceNow, create the GitHub Basic Auth credential.
3. Open Studio and choose **Import From Source Control**.
4. Use the fork URL, credential, and intended development branch.
5. Complete the import and resolve all required application/plugin dependencies before proceeding.
6. In **System Definition > Scripts - Background**, switch the application scope to **DevTools** (`x_snc_devtools`).
7. Run:

   ```javascript
   x_snc_devtools.InstallApp('x_snc_devtools');
   ```

8. Confirm that the installation finishes without a fatal error.

The repository README lists platform dependencies including Service Portal - Core, Configuration Management (CMDB), UI Policy Rules, and System Applications Core. Source control does not install dependencies for you. Resolve missing dependencies first; if an application was imported before its dependencies, the documented recovery is to remove it, install/activate dependencies, then re-import.

## Validate the installation

On non-production instances:

1. Set `sn_atf.runner.enabled` to `true` if ATF execution is disabled.
2. Open **DevTools > Test Suite**.
3. Run the DevTools suite.
4. Review every failure; do not accept a partly working installation merely because the menu appears.
5. Open **DevTools > DevTools Options** and compare instance values with the [property catalog](reference/system-properties.md).
6. Open a safe application file and confirm the DevTools UI Actions expected for your role and UI.
7. Verify source-control branch and credential behavior on the development instance.

## Known validation conditions

- **`DevTools - HttpRequest` fails:** the instance may be unable to call itself. Check outbound networking, proxy, DNS, MID Server, and allow-list rules.
- **Domain-separated instance failures:** rerun from the top-level domain where appropriate.
- **`DevTools - RunScriptInGlobalScope` fails:** first-run cross-scope privileges can take time to become effective. Retry after several minutes. Diagnose this before downstream failures because many APIs depend on it.
- **TestDataAPI tests fail:** local mandatory-field customizations may block generated records. If needed, add narrowly scoped test-data handling only for records whose identifying text begins with the configured `_ATF_` prefix.
- **Test data sanity fails:** use the cleanup procedure in [Testing and quality](testing-and-quality.md).

## First-use checklist

- Review the [configuration profiles](configuration.md#recommended-profiles).
- Keep logging at `info` until active debugging is needed.
- Decide whether forced scope switching is acceptable for all developers.
- Confirm which user-management buttons are allowed by policy.
- Document who may baseline versions and modify dependency records.
- Protect baselined release branches in the source-control provider.
- Run the suite again after any upgrade.

