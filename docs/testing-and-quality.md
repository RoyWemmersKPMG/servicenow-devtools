# Testing and quality

DevTools ships a large ATF suite, custom test-step configurations, test-data helpers, application sanity checks, and Instance Scan integration. Use all of them on non-production instances as a release gate.

## Running the DevTools suite

1. Confirm `sn_atf.runner.enabled=true`.
2. Use the top-level domain on domain-separated instances where test setup requires it.
3. Open **DevTools > Test Suite** and run the suite.
4. Diagnose foundational failures first: installation, cross-scope execution, HTTP self-access, and test-data setup.
5. Rerun the failed test, then the whole suite.

The [ATF test catalog](reference/atf-tests.md) is generated from this checkout.

## Creating tests for application files

On supported records, use **Create Test**. DevTools can create a test, add it to the application's default suite, and generate a starting script. **Go to Test** opens the associated test; **Run Test** and **Run Test Suite** shorten the feedback loop.

Server-side helpers include:

```javascript
x_snc_devtools.CreateTestForRecord(current);
x_snc_devtools.AddScriptTestStep(testSysId);
var source = x_snc_devtools.MakeTestScriptForRecord(current);
```

Generated tests are scaffolds. Add meaningful assertions, negative paths, authorization coverage, cleanup, and upgrade-regression cases.

## Test data

`TestDataAPI` creates consistently named users, groups, roles, companies, tasks, requests, configuration items, database views, Script Includes, and Service Portal widgets. Use a unique prefix per suite or run, and delete everything in teardown.

Example cleanup for the conventional DevTools prefix:

```javascript
var testData = new x_snc_devtools.TestDataAPI('_ATF_DEVTOOLS_');
var records = testData.GetAllData();
gs.info('Deleting test data: ' + x_snc_devtools.RenderValue(records));
testData.DeleteAllData(records);
```

If local business rules make additional fields mandatory, handle only identifiable ATF records and keep production behavior unchanged.

## Cleaning incomplete ATF runs

`CleanUpATF()` removes test results and rollback contexts. Its broad mode is destructive and should be used only when no tests are still running. Prefer the narrow in-progress cleanup and explicit limits when possible. Confirm active executions before deleting result or rollback data.

## Application sanity and Instance Scan

`AppSanity()` normalizes known problematic application metadata, including copied test references, unnecessary field ACLs, and property privacy. Because platform behavior changes by release, review its effects after each DevTools upgrade.

`InstanceScanSuiteOnApp(suiteSysId, app, detailed)` runs a selected scan suite against an application. The property `x_snc_devtools.instancescan.prevent_useless_checks` avoids known irrelevant script checks. Extend those rules only through the DevTools extension point and return the complete rules object; an incorrect extension can remove or over-broaden filtering.

## Release quality gate

A release candidate should satisfy:

- clean source-control status;
- full default ATF suite passing;
- no unexplained test-data remnants;
- Instance Scan findings resolved or formally accepted;
- install and upgrade paths tested;
- dependencies present at compatible versions;
- no credential pollution warnings;
- release notes and technical debt updated;
- version branch protection ready.

