# ServiceNow DevTools knowledge base

This wiki documents DevTools `1.147.0`, a scoped ServiceNow application (`x_snc_devtools`) that combines reusable server and client APIs, developer UI improvements, application lifecycle automation, ATF helpers, dependency management, and deployment utilities.

DevTools is community software maintained by Sascha Wildgrube. It is not an officially supported ServiceNow product and is provided without warranty. Several capabilities intentionally cross application-scope boundaries; read [Security and risk](security-and-risk.md) before installation.

## Start here

- [Getting started](getting-started.md) — prerequisites, installation, validation, and first-use checklist
- [Configuration](configuration.md) — properties, recommended profiles, and feature switches
- [Daily developer workflows](daily-developer-workflows.md) — navigation, form actions, code generation, and troubleshooting tools
- [Application lifecycle](application-lifecycle.md) — scaffold, install, baseline, share, and deploy applications
- [Testing and quality](testing-and-quality.md) — ATF, Instance Scan, generated tests, and cleanup
- [Dependencies and technical debt](dependencies-and-technical-debt.md) — dependency models, deployment order, shared apps, and feature switches
- [Record Transporter](record-transporter.md) — package non-metadata records for application deployment
- [Using the API](using-the-api.md) — calling conventions, recipes, classes, client APIs, and API selection
- [Security and risk](security-and-risk.md) — cross-scope execution, credentials, destructive operations, and production controls
- [Troubleshooting](troubleshooting.md) — symptom-based diagnosis and recovery
- [Architecture and maintenance](architecture-and-maintenance.md) — repository layout, generated files, upgrades, and contributing

## Generated reference

These pages are rebuilt from the XML in `update/` by running `./tools/Generate-WikiReference.ps1` from PowerShell:

- [Script Includes](reference/script-includes.md)
- [UI Scripts](reference/ui-scripts.md)
- [Syntax Editor Macros](reference/syntax-editor-macros.md)
- [System properties](reference/system-properties.md)
- [UI Actions](reference/ui-actions.md)
- [ATF tests](reference/atf-tests.md)
- [Metadata inventory](reference/metadata-inventory.md)

## What DevTools is for

Use DevTools when you need to:

- reuse tested utility functions instead of repeatedly writing GlideRecord, type conversion, rendering, URL, HTML, or collection helpers;
- make classic ServiceNow development screens quicker to navigate;
- bootstrap a consistent application structure and installation script;
- run repeatable application installation and post-install configuration;
- model and visualize dependencies and determine deployment order;
- baseline source-controlled application versions;
- create and run ATF coverage and Instance Scan checks;
- transport a small, deliberate set of data records with an application;
- document technical debt as application metadata;
- integrate with source control providers, credentials, HTTP endpoints, and LLM prompting.

DevTools is not a replacement for ServiceNow governance, code review, access controls, backup, release management, or target-instance validation.

## Known limitations

- Several UI enhancements target the classic platform UI and do not automatically support Polaris or Workspace.
- Some Script Includes do not have a matching syntax macro.
- The application carries its own Technical Debt records, including known test, coding-pattern, and data-model gaps; review **DevTools > Technical Debt** before adopting affected APIs.
- Related-record and scope-move helpers cannot anticipate every relationship introduced by future ServiceNow releases.
- Auto-generated reference descriptions explain purpose but are not complete contracts for parameters, side effects, performance, or authorization.

## License

Copyright 2020–2026 Sascha Wildgrube. DevTools is licensed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0). Consult the upstream project for the authoritative license notice and release history.

## Documentation sources

This knowledge base is based on the checked-in application metadata and the author's [original DevTools documentation](https://www.wildgrube.com/servicenow-devtools). The repository is the authority for what is present in this checkout; the running instance is the authority for installed plugins, property values, roles, and local customizations.

