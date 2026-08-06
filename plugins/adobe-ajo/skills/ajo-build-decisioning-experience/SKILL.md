---
name: ajo-build-decisioning-experience
description: Plan and implement an Adobe Journey Optimizer Decisioning experience using fragments, draft items, exact-match collections, eligibility rules, ranking formulas, strategies, and placements. Use for end-to-end offer or decisioning builds.
---

# Build an AJO Decisioning experience

Treat MCP confirmation arguments as operation guards, not evidence of human consent. Obtain explicit approval in the conversation before every write.

## Plan

1. Call `ajo_get_capabilities` and summarize the supported boundaries.
2. Clarify the business objective, channels, content roles, audience logic, item names, dates, ranking behavior, and desired output count.
3. Discover reusable fragments, rules, items, collections, formulas, strategies, and placements before proposing new resources.
4. Resolve the configured Decisioning catalog before creating items or collections.
5. Present an ordered plan listing every proposed write, reference, lifecycle effect, and external AJO step. Do not write until the user approves the plan.

## Build

1. Create and publish required AJO expression fragments through the Content workflow.
2. Create reusable eligibility rules only when profile constraints are required.
3. Create Decisioning items as drafts. Do not embed unvalidated fragment references during item creation.
4. Attach each published expression fragment to its draft item under a stable key such as `hero`, `body`, or `footer`.
5. Create collections with validated exact predicates only:
   - `equals`: one exact field value.
   - `in`: one of several exact values.
   - `and` or `or`: nested validated conditions.
   Partial matching, wildcards, and `$contains` are unsupported.
6. Create ranking formulas only when static item priority is insufficient.
7. Create strategies referencing a collection and optional eligibility rule or formula. Strategies never reference placements.
8. Create an email placement only when one does not already exist for the intended channel configuration.

Before each write, show the resource, exact change, references, risk, and confirmation phrase. For updates, retrieve the same resource immediately beforehand and copy its current `metadata.etag`.

## Validate And Approve

1. Inspect every strategy and report unresolved dependencies.
2. Validate every item with `ajo_decisioning_validate_item_readiness`.
3. Obtain a separate explicit approval before approving each item.
4. Re-read every mutated resource and report its final ID, lifecycle, ETag, and warnings.
5. Explain that Decision Policy creation, strategy-to-placement binding, simulation, proofing, and activation must be completed in AJO outside this MCP.

Never automatically retry an ETag mismatch. Never archive or delete as part of a build workflow.
