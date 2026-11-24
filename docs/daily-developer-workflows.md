# Daily developer workflows

DevTools adds a broad **DevTools** application menu, shortcuts to hidden or scattered platform pages, and context-aware UI Actions. Most features are aimed at administrators in the classic platform UI; visibility and behavior may differ in Polaris, Workspace, UI Builder, or Service Portal.

## Finding your way around

The application menu groups links for:

- logs, evaluator output, background scripts, and execution history;
- Studio, Workflow Studio, UI Builder, Service Portal configuration, and ATF;
- configuration properties and repositories;
- Script Includes, UI Scripts, syntax macros, and test-step configurations;
- instance diagnostics, active transactions, cache, node logs, and replication;
- Record Transporter packages;
- applications, shared applications, dependencies, and technical debt.

Use these as shortcuts, not as a substitute for understanding the underlying platform module and permissions.

## High-value form actions

- **Save** saves with awareness of the record's scope, reducing lost work when another browser tab changed the session scope.
- **List** saves and opens the corresponding table list.
- **Up** resolves a logical parent for supported metadata records.
- **Parent Table** follows table inheritance.
- **Related Records** renders a tree/list of records related to the current record. Review the result critically for new or unusual platform structures.
- **Find Sys ID** locates a record from a sys_id.
- **What Runs / What Runs on Table** helps identify logic associated with a record or table.
- **Create Test / Go to Test / Run Test** supports test-first navigation for supported application files.
- **Make Code…** actions emit InstallerAPI-style code for forms, lists, related lists, sections, and UI Policy Actions.

The full list is in the [UI Action catalog](reference/ui-actions.md).

## Using syntax editor macros

DevTools ships hundreds of macros for API calls, class skeletons, installers, debugging, and platform patterns.

1. Work in the correct application scope.
2. In a supported script editor, enter the macro name and invoke macro completion.
3. Replace all placeholders.
4. Review API access, table names, queries, and destructive behavior.
5. test in a non-production instance.

Search the [macro catalog](reference/syntax-editor-macros.md) by function or task name. Macro output is a starting point, not a safe-to-run final script.

## Debugging records and scripts

Useful APIs include `RenderValue()`, `GetType()`, `GetCallStack()`, `GetCurrentRecord()`, `GetRelatedRecordsTree()`, `GetRelatedRecordsList()`, and logging helpers. A typical background-script diagnostic is:

```javascript
var gr = new GlideRecord('incident');
if (gr.get('REPLACE_WITH_SYS_ID')) {
    gs.info(x_snc_devtools.RenderValue(gr));
}
```

Never log secrets, credential objects, tokens, personal data, or unrestricted record payloads. Prefer specific fields and remove temporary diagnostics.

## Working safely with generated code

The Make Code features can produce repeatable installer definitions from an existing configuration. Before committing the result:

- verify that record sys_ids are stable or intentionally fixed;
- explain the reason for the installation change;
- ensure the operation is idempotent;
- separate required configuration from environment-specific data;
- run the installer twice on a disposable instance to detect duplicate creation;
- test upgrade behavior, not only clean installation.

## User helper actions

DevTools can create test/technical users, create a user from email, and generate or send credentials. These are privileged operations. Confirm naming, role assignment, group assignment, SMTP state, password policy, audit requirements, and data-handling policy before use. Prefer the test-user helpers only in non-production.

