# Troubleshooting

Start with the failing layer: installation, platform prerequisite, cross-scope execution, network, source control, UI visibility, tests, or application-specific behavior. Enable debug logging only long enough to reproduce the issue.

## The application will not import

- Confirm all required plugins/applications are installed first.
- Confirm the repository is a valid ServiceNow source-control export and was not assembled from mixed revisions.
- Inspect `git log` for commits that edited generated XML outside an instance.
- Do not edit `checksum.txt` or generated XML to force acceptance.
- Recover with a clean known-good revision or revert offending commits as described in [Architecture and maintenance](architecture-and-maintenance.md#recovering-a-generated-source-control-export).

## Installation script fails

- Verify the background script is running in `x_snc_devtools` scope.
- Confirm the cross-scope property and required privileges.
- Look at the first InstallerAPI error rather than later symptoms.
- Check missing plugins, tables, columns, properties, and dependency versions.
- Rerun only after understanding whether the failed step is idempotent.

## UI Actions are missing

Check, in order:

1. the corresponding `x_snc_devtools.ui.*` property;
2. current user roles;
3. UI Action active state, table, condition, and form/list flags;
4. current scope and record state;
5. classic UI versus Workspace/Polaris support;
6. required plugin or related record;
7. browser/session cache.

Use the [UI Action catalog](reference/ui-actions.md) to find the technical action name.

## `RunScriptInGlobalScope` or dependent tests fail

- Confirm `glide.record.legacy_cross_scope_access_policy_in_script=true` in global scope.
- Check restricted caller/cross-scope privilege records.
- Wait several minutes after the first privilege generation, then retry.
- Review evaluator and system logs.
- Resolve this before debugging the many helpers that depend on it.

## HTTP test or API call fails

- Test whether the instance can reach the target, including itself.
- Check DNS, outbound allow lists, proxy, TLS trust, firewall, and MID Server selection.
- Validate credential type and scope without logging the secret.
- Capture HTTP status and a redacted response.
- Check provider rate limits and source-control token expiry.

## ATF failures create leftover data

- Stop or finish active runs first.
- Use the test-data prefix to inventory records.
- Run `TestDataAPI.DeleteAllData()` for the precise prefix.
- Use `CleanUpATF()` only with understood scope and limits.
- Fix mandatory-field customizations using narrowly targeted `_ATF_` handling.

## Baselining fails

- Commit or intentionally discard all uncommitted application files.
- Confirm the version branch does not already exist.
- Validate provider credential and branch permissions.
- Run AppSanity and the default suite independently.
- Check the application manual's release-note heading/version/date convention.
- Confirm no baseline job is already running.
- Review shared-application configuration if using **Baseline Shared App**.

## Wrong-branch warning

The warning means the current source-control branch does not match the development expectations. Do not suppress it until confirming the application's current branch, the intended pipeline, and whether the instance is a clone with stale source-control configuration.

## Record Transporter mismatch

- Verify the package ID and owning scope.
- Check whether reset generated intended deletion records.
- Confirm target tables/columns and referenced records exist before unpack.
- Inspect package order and auto-unpack/auto-commit flags.
- Compare the target record with the packed payload for local conflicts.
- Reproduce on a clone before correcting production.

## Evaluator errors do not appear

Some AJAX-triggered server evaluator errors may not be logged normally. Temporarily set DevTools logging to debug, add narrow checkpoints around the failing call, inspect the evaluator log, and remove the diagnostics afterward.

## Performance problems

Disable transaction-runtime logging and debug verbosity, check global UI scripts and formatting callbacks, inspect synchronous HTTP calls, avoid repeatedly building full table/related-record inventories, and run active-transaction collection asynchronously where possible.

