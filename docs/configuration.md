# Configuration

Open **DevTools > DevTools Options** to manage the application's scoped properties. The [generated property catalog](reference/system-properties.md) contains every property and its repository default.

Changing a property can alter global UI behavior for administrators, source-control workflows, or background processing. Test changes on non-production and record intentional differences between environments.

## Recommended profiles

### Conservative evaluation profile

Use this while introducing DevTools to an established environment:

- keep `x_snc_devtools.logging.verbosity=info`;
- keep credential-pollution and wrong-branch warnings enabled;
- keep evaluator error display enabled for a short window;
- enable only the UI buttons the team will actively use;
- leave field formatting disabled unless a supported classic-UI use case is configured;
- review `force_scope` with the development team before enabling globally;
- restrict baselining to release managers through process and role governance.

### Development profile

The repository defaults enable most developer buttons, scope enforcement, baseline actions, dependency views, test creation, and record-navigation helpers. This is appropriate only where administrators understand the behavior and changes are source controlled.

### Production profile

Minimize interactive development features. Disable UI actions that create users, generate credentials, delete update sets, baseline versions, pack update sets, or generate configuration code unless there is a documented production need. Keep diagnostic warnings only if their performance and information-disclosure impact is acceptable.

## Important property groups

### Logging and diagnostics

- `x_snc_devtools.logging.verbosity`: use `info` normally and `debug` temporarily while reproducing a problem.
- `x_snc_devtools.debug.log.transaction_runtime`: adds transaction-runtime diagnostics; enable only for targeted investigation.
- `x_snc_devtools.ui.show_evaluator_errors.minutes`: controls how recently detected evaluator errors are shown to admins. `0` effectively disables the recency display.
- `x_snc_devtools.ui.deprecation_warning`: surfaces calls into deprecated DevTools code.

Return verbose diagnostics to normal after investigation to avoid noise and unintended data exposure.

### Scope and source control

- `x_snc_devtools.ui.force_scope`: changes the selected scope to match a record when loading or saving supported forms. Individual users can opt out from their User record with **Do NOT enforce record scope**.
- `x_snc_devtools.ui.show_wrong_branch_warning.active`: warns when a development instance is linked to an unexpected branch.
- `x_snc_devtools.sourcecontrol.cooldown.seconds`: delays repeated source-control actions where provider or platform timing requires it.

### UI feature switches

Properties under `x_snc_devtools.ui.*_button` control actions such as Baseline, Dependencies, Find Sys ID, Go to Test, List, Save, Up, Related Records, What Runs, user creation, credential generation, and update-set packing. A property is only one visibility condition; roles, record state, UI type, plugins, and action conditions also matter.

### Quality and documentation

- `x_snc_devtools.code.auto_generate_documentation`: lets a business rule update class Script Include descriptions from code changes.
- `x_snc_devtools.instancescan.prevent_useless_checks`: suppresses Instance Scan script checks where the script field is known to be irrelevant.
- `x_snc_devtools.baseline.message` and `.baseline.sharedapp.message`: confirmation checklists shown before version baselining.

### Field formatting

`x_snc_devtools.ui.formatting=true` enables custom numeric formatting in supported classic forms and lists. Polaris is not supported by the original implementation. Formatting also requires a DevTools extension-point implementation that returns per-table field configuration. Treat the callback as a hot path and cache appropriately.

### AI prompting

`x_snc_devtools.prompt.capability` identifies the configured Generic Prompt Capability used by `Prompt()`. It does not by itself install Now Assist, select an LLM, or create connection credentials. See [Using the API](using-the-api.md#llm-prompting).

## Related global properties

These are not shipped as scoped DevTools properties but are used by features:

- `glide.record.legacy_cross_scope_access_policy_in_script`: required for cross-scope execution; security review mandatory.
- `sn_atf.runner.enabled`: must permit ATF execution.
- `glide.email.smtp.active`: required before **Generate and Send Credentials** can deliver mail.
- `devtools.createuserbyemail.groups`: optional comma-separated group sys_ids assigned by **Create User by Email**.

## Verifying effective values

Repository defaults are not proof of runtime state. On each instance, export or review the effective property values, compare them to the intended environment profile, and check for global properties with similar names. After changing a cached property, use the least disruptive cache refresh suitable for that property and verify behavior in a new session.

