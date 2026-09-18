# Audience read contract

Audience access is definition-only and read-only.

1. Call `ajo_aep_list_audiences` and follow pagination.
2. Select the system ID from `data.children[].id`.
3. Call `ajo_aep_get_audience` with that `id`, never an external or similarly named `audienceId`.
4. Report stored PQL, schema, merge policy, evaluation modes, origin, lifecycle, labels, dependencies, and dependents when present.

These tools do not expose audience members, estimates, qualification results, or profile eligibility simulation.

For Decisioning eligibility, use the exact verified system IDs with `ajo_decisioning_create_rule` `audienceEligibility` and an explicit `and` or `or` operator. The server exact-gets every ID and generates only `inSegment("<system-id>")` PQL. Never use audience names, external `audienceId` values, or copied stored audience PQL. Adobe persists item eligibility as one rule reference, so creating the audience-backed rule and attaching its returned ID to an item or strategy are separate approved writes. Re-read the target after attachment.

Structured Profile conditions follow `eligibility-rule-pql-contract.md`. Raw PQL remains a separate advanced mode and its local diagnostics do not prove syntax, semantics, field existence, or qualification. Never supply more than one of raw `expression`, `audienceEligibility`, or `eligibilityAst`.
