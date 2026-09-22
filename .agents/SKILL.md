# Project Agent Instructions — claudeprojekti (Salesforce DX)

## Role

You are assisting with development on this Salesforce DX project (`force-app/main/default`). Treat this as a Ceili Oy client codebase: prioritize correctness, security (CRUD/FLS, sharing), governor-limit safety, and long-term maintainability over speed of delivery.

See also:
- [flow-rules.md](./flow-rules.md) — Flow Builder design rules
- [apex-checks.md](./apex-checks.md) — Apex code quality checklist

## General conventions

- **Development language is always English** — API names, code, comments, automation logic, and Description fields. End-user-facing labels may be in Finnish.
- **Fill in the Description field** for every custom component (object, field, Flow, Flow element, Apex class/method doc). Include what it does; append changes rather than overwriting history.
- **Names are descriptive, unique, and distinct from standard** Salesforce names. Avoid generic names (`Custom_Field__c`, `Information_1__c`) and avoid abbreviations except established ones (`Dao`, `Cmp`, `Evt`, or Flow resource prefixes like `var`, `con`, `txt`).
- Prefer **Custom Metadata Types** over Custom Settings for deployable configuration; use Custom Settings only for user/org-specific runtime data.
- Follow the **One Entry Point Rule** per object: use either Flow or Apex Triggers to drive automation, not both.

## Before proposing a solution

1. Check whether the change belongs in Flow or Apex (see [flow-rules.md](./flow-rules.md) §"Flow isn't always the best idea").
2. Check naming against the conventions above before creating new API names.
3. For anything touching DML/SOQL, apply the checklist in [apex-checks.md](./apex-checks.md).

## Build & verification

- `sf project deploy start --dry-run` to validate before deploying.
- `sf apex run test --test-level RunLocalTests` after any Apex change.
- `npm run lint` / `npm run prettier:verify` before considering front-end (LWC) work done.
- `sf code-analyzer run --severity-threshold 3` for a broader quality/security pass when touching non-trivial Apex.

## MCP

This project wires in the official Salesforce DX MCP server (`@salesforce/mcp`, see [.mcp.json](../.mcp.json)) for org, metadata, data, and user tools plus Apex test execution. It defaults to `DEFAULT_TARGET_ORG` — set the target org first with `sf config set target-org <alias>` (or `sf org login web`) if no default is set.
