# Security and risk

DevTools is an administrator toolkit, not a security boundary. Its value comes partly from powerful APIs and broad platform access, so installation should have an explicit risk owner.

## Cross-scope execution

`RunScriptInScope()` and `RunScriptInGlobalScope()` rely on `glide.record.legacy_cross_scope_access_policy_in_script=true`. This can allow code to perform operations unavailable through ordinary scoped APIs.

Controls:

- obtain written security approval before enabling it;
- enable only on the instances where DevTools is authorized;
- restrict who can edit or execute scripts and application files;
- never pass user-controlled or remotely supplied script text;
- monitor evaluator errors, cross-scope privileges, and sensitive table changes;
- review every consumer of the two APIs;
- include the property in environment compliance checks.

Turning the property off can break DevTools and dependent applications. Test that rollback on a clone before relying on it.

## Credentials and secrets

DevTools integrates with GitHub/Azure DevOps, HTTP credentials, connection aliases, user-password generation, and credential-pollution checks.

- Store secrets in ServiceNow credential records or approved connection aliases.
- Never place PATs, passwords, API keys, OAuth tokens, or private endpoints in Script Includes, properties, transported records, logs, or test output.
- Give source-control tokens the smallest viable repository access and rotate them.
- Limit access to credential helper APIs and user-generation UI Actions.
- Keep credential-pollution warnings enabled and investigate every finding.
- Confirm SMTP, recipient identity, and password-reset policy before sending credentials.

## Destructive and irreversible capabilities

High-risk APIs include application deletion, database-index changes, record deletion, update-set deletion/packing, source-control commits and baselines, scope moves, package reset/unpack, broad ATF cleanup, and AI deactivation.

Before use:

1. identify the exact records, app, branch, or package;
2. create a recoverable backup or clone;
3. preview with a read-only query or inventory;
4. test in non-production;
5. require peer approval for production;
6. record the expected and observed result.

`InstanceDeactivateAI()` is especially consequential: the original documentation states that it requires `maint`, has no automated undo, clears endpoint configuration, and deactivates multiple AI capabilities. Use only through a ServiceNow-supported, tested change with a manual recovery plan.

## Data exposure

Rendering GlideRecords, HTTP payloads, call stacks, related-record trees, active transactions, and diagnostic output can expose personal data or system internals. Use narrow queries, redact values, limit log retention, and do not paste production output into external tools.

LLM prompting must follow the organization's AI data classification and provider policy.

## Production hardening

- Disable unused UI buttons and developer shortcuts.
- Restrict admin and script-editing roles.
- Keep development source-control credentials off production where possible.
- Review application dependencies and inbound restricted-caller access.
- Monitor baseline, install, user-creation, credential, and transporter activities.
- Run a property/configuration compliance report after clones and upgrades.
- Retest the application after each ServiceNow family upgrade.

