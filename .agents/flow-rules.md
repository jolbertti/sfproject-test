# Flow Design Rules

Condensed from Ceili's Flow development standards. Apply these whenever creating or reviewing a Flow in this project.

## Hard rules (never violate)

1. **Never perform DML (Get/Create/Update/Delete Records) inside a Loop.** Collect records into a collection variable inside the Loop, then perform a single DML element outside it.
2. **Never hard-code Salesforce IDs** (Record IDs, Record Type IDs, User IDs). Look them up dynamically (Get Records by DeveloperName/unique key) or use a Constant / Custom Metadata Type. Use the Entity Definition object (`KeyPrefix`) instead of hardcoded ID-prefix logic when detecting object type dynamically.
3. **Every Get/Create/Update/Delete Records element must have a Fault Path.** Fault paths must produce a specific, actionable message (what happened, why, what to do next) — never a generic "an error occurred."
4. **One Entry Point Rule**: use either Flow or Apex Triggers per object, not both.
5. **Never build or test directly in Production.** Always sandbox/dev org first.

## Design choices

- **Before-Save flows** for updates to the *same* record that triggered the flow (no extra DML, faster). **After-Save flows** only when you need the saved record's Id or must touch related records.
- Use **Transform** elements instead of Loop+Assignment when mapping one collection to another.
- Use **Subflows** for logic reused across multiple flows (calculations, validations, screen actions).
- Use **Schedule-Triggered Flows** / **Asynchronous Paths** for non-time-critical or bulk work, to stay under sync governor limits.
- Consider Apex instead of Flow when: CPU time regularly exceeds ~10s, logic requires complex string/regex handling, or you'd need deeply nested Decision/Loop structures. Consider a hybrid (Flow orchestration + Invocable Apex for heavy lifting) when volume or algorithmic complexity grows.

## Documentation requirement

- **Flow Description**: business purpose, trigger condition, high-level process — filled in for every Flow.
- **Element Description**: every Get/Decision/Assignment/etc. element gets a description explaining what and why (these are read by Agentforce/AI assistants too, so they matter beyond human readers).
- Naming conventions for Flows, elements, and resources are Ceili-specific — check the `ceili-best-practices` skill (`flow-documentation.md`) rather than inventing names ad hoc.

## Before activating a Flow in Production — checklist

- [ ] No DML inside Loops
- [ ] All DML elements have Fault Paths with actionable messages
- [ ] No hard-coded IDs
- [ ] Before-Save vs. After-Save choice is correct for the use case
- [ ] Transform used instead of Loop where applicable
- [ ] Flow Description and all element Descriptions filled in
- [ ] Tested in sandbox with valid data, edge cases, and invalid data (debug + Flow Tests where applicable)
- [ ] Cross-object automation dependencies (other Flows/Triggers on the same object) reviewed
