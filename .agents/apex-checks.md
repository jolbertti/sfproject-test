# Apex Code Quality Checklist

Condensed from Ceili's Apex development standards. Apply these when writing or reviewing Apex in this project.

## Hard rules (never violate)

1. **No SOQL or DML inside loops.** Always bulkify: operate on `List`/`Set`/`Map` collections, single DML/query per transaction path.
2. **Enforce CRUD/FLS before querying or writing.** Use `WITH SECURITY_ENFORCED` (or `WITH USER_MODE` on newer API versions), `Security.stripInaccessible()`, or explicit `Schema.sObjectType.X.isAccessible()/isUpdateable()` checks. Classes default to `with sharing`; document any `without sharing` use.
3. **Escape user input in dynamic SOQL** with `String.escapeSingleQuotes()` — never concatenate raw user input into a query string.
4. **Never hard-code IDs.** Use dynamic queries, Custom Metadata, or named constants instead.
5. **Always handle exceptions from DML and callouts** with try/catch, custom exception types, and meaningful messages — no silent failures.

## Design & structure

- Layered architecture: `*Controller` (LWC/Aura entry), `*Service` (business logic), `*Selector` (SOQL), `*TriggerHandler` (one trigger per object, logic lives in the handler, not the trigger body).
- Prefer `private`/`protected` visibility; only expose what callers actually need.
- Use Custom Metadata Types for deployable configuration; Custom Settings only for user/org-specific runtime values.
- Named Credentials for all external endpoints — never hardcode URLs, tokens, or secrets.
- Recursion guards (static boolean/counter) in trigger handlers that could re-enter.

## Async & scale

- `@future` for simple async/callouts; **Queueable** for chained/complex async; **Batch Apex** for >50k records (tune batch size: smaller for callout-heavy work, default 200 for simple DML).
- Use `Database.Stateful` only when cross-batch state is actually needed.
- Never implement both `Database.Batchable` and `Schedulable` in the same class.

## Testing

- Minimum 75% coverage, aim for 100% — but coverage is not the goal, **meaningful assertions** are.
- Cover positive, negative, and bulk (200+ record) scenarios.
- Use `@TestSetup`, `Test.startTest()/stopTest()`, `System.runAs()` for permission scenarios, `Test.setMock()` for callouts.
- Never use `@SeeAllData=true`. Use `Assert.*` methods (not deprecated `System.assert*`) with descriptive failure messages.

## Common smells to flag in review

- DML/SOQL in a loop
- Hardcoded IDs or magic numbers (use named constants)
- Methods doing too much (single responsibility; keep methods focused)
- Missing null checks on query results / parameters
- String concatenation in loops (use `String.join()`)
- Duplicate logic that belongs in a shared Service/Selector method

## Build & verification

- `sf apex run test --test-level RunLocalTests` after any Apex change — must pass with no regressions.
- `sf code-analyzer run --severity-threshold 3` (or higher scrutiny for security-sensitive changes) before considering the change done.
