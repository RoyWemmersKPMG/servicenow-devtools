# Architecture and maintenance

## Repository layout

This repository is a ServiceNow source-control export:

- `sys_app_*.xml` — application record and version metadata;
- `update/` — application files serialized as update records;
- `dictionary/` — table dictionaries used by the export;
- `author_elective_update/` — elective update records;
- `sn_source_control.properties` — source-control export metadata;
- `checksum.txt` — ServiceNow-generated integrity data;
- `docs/` — this hand-written wiki and generated catalogs;
- `tools/Generate-WikiReference.ps1` — rebuilds catalogs from XML.

The application layer contains hundreds of Script Includes, UI Scripts, syntax macros, ATF tests, UI Actions, business rules, properties, ACLs, modules, and custom tables. See the [metadata inventory](reference/metadata-inventory.md).

## Custom data model

The principal DevTools tables are:

- `x_snc_devtools_dependency` — application dependency declarations;
- `x_snc_devtools_shared_application` — local stewardship for external apps;
- `x_snc_devtools_technical_debt` — deployable debt records and switches;
- `x_snc_devtools_transporter_package` — package metadata;
- `x_snc_devtools_transporter_record` — packed data and actions;
- `x_snc_devtools_test` — internal/test support;
- `x_snc_devtools_cmdb_ci_appl_servicenow` — CMDB class representing ServiceNow apps.

## Generated-file policy

Do not hand-edit application XML, dictionary files, or `checksum.txt`. Make application changes in ServiceNow and let source control export them. Mixing files from unrelated exports or editing generated content can make an import fail integrity validation.

Markdown in `docs/` and the reference generator are intentionally repository-maintained documentation. After importing a newer DevTools version, regenerate the reference pages and review hand-written guidance for behavioral changes.

## Regenerating the reference

From the repository root in PowerShell:

```powershell
./tools/Generate-WikiReference.ps1
```

Then review:

```powershell
git diff -- docs/reference
```

Catalog changes reveal added, removed, renamed, or re-described APIs and metadata. They do not prove backward compatibility; inspect implementation and tests for important consumers.

## Upgrade procedure

1. Read upstream release notes between the installed and target versions.
2. Back up the instance/application and protect current release branches.
3. Import/merge the new application export in a development instance through the supported ServiceNow source-control workflow.
4. Resolve dependencies and platform-family changes.
5. Run installation scripts.
6. Regenerate this wiki's reference pages.
7. Review API removals, access changes, defaults, UI Actions, and data model changes.
8. Run the complete DevTools suite and consuming-application suites.
9. Validate install, upgrade, source-control, baseline, transporter, and security flows on a clone.
10. Promote through the normal pipeline.

## Recovering a generated source-control export

If an instance rejects the repository because generated files came from mixed revisions or were edited externally, prefer recoverable Git history operations:

- identify offending commits with `git log` and create revert commits in reverse chronological order; or
- start from a known-good ServiceNow-generated snapshot and create a new commit that represents that complete snapshot.

Avoid rewriting shared history. Never manufacture a checksum. Coordinate any hard reset or history repair with repository owners and preserve a backup branch first.

## Contributing documentation

- Put task-oriented explanation in the appropriate hand-written page.
- Put no manual edits in `docs/reference/`; change the generator or application metadata and regenerate.
- Link to technical names and include safe, minimal examples.
- State UI, role, plugin, scope, and production limitations.
- Do not include real credentials, sys_ids from customer instances, or personal data.
- Verify every command and code sample on a disposable or development instance.

## Upstream and ownership

The application metadata identifies Sascha Wildgrube as maintainer and links to the [upstream repository](https://github.com/saschawildgrube/servicenow-devtools) and [original documentation](https://www.wildgrube.com/servicenow-devtools). Organizations should fork into a controlled repository, document local ownership, and decide how upstream changes are reviewed and merged.

