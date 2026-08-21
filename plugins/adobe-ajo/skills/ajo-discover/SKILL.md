---
name: ajo-discover
description: Read-only inventory, lookup, and relationship discovery for Adobe Journey Optimizer Content Library, Decisioning, Journey campaign actions, campaign scope, and AEP audience definitions. Use to answer what exists, retrieve exact stored state, find reusable candidates, or resolve IDs. Do not use for formal readiness audits or mutations.
---

# Discover AJO resources

## Required context

- The sandbox is fixed to `aepenablementfy21`. Stop if any input or result indicates another sandbox.
- Clarify the resource type, business question, known IDs, naming clues, and desired detail.

## Workflow

1. Call `ajo_get_capabilities` when current server guidance is unavailable.
2. Inventory with the exact relevant tools and follow Adobe pagination: `ajo_decisioning_list_items`, `ajo_decisioning_list_rules`, `ajo_decisioning_list_collections`, `ajo_decisioning_list_ranking_formulas`, `ajo_decisioning_list_strategies`, `ajo_decisioning_list_placements`, `ajo_content_list_fragments`, `ajo_content_list_templates`, or `ajo_aep_list_audiences`.
3. Use the matching exact `get` tool before reporting full configuration, content, lifecycle, or ETag. Never invent filters or IDs.
4. For strategies, call `ajo_decisioning_inspect_strategy` to explain collection, eligibility-rule, and ranking dependencies.
5. Distinguish fragment draft, publication status, and live content. A visual fragment is not a valid Decisioning item content reference.
6. For template source, list only when the ID is unknown, then call `ajo_content_get_template` and report normalized `data.qa` fields, marker/policy-block state, and ETag.
7. Resolve Journey and campaign identifiers according to `../../references/campaign-scope-resolution.md`.
8. Read audience definitions according to `../../references/audience-read-contract.md`.
9. Present candidate resources with exact ID, lifecycle, normalized configuration, references, scope, and meaningful differences. A name match is not semantic equivalence.

## Stop and handoff

Stop on sandbox mismatch, unresolved recovery ambiguity, incomplete pagination, or conflicting IDs. Do not stop merely because embedded campaign metadata is unavailable; `ajo_journey_resolve_campaigns` must attempt exact Campaign Service recovery first. Return a concise inventory and recommend the next read or destination skill.

Do not create, update, publish, approve, archive, bind, or delete. Discovery approval never authorizes a later mutation; the destination skill must preflight and request fresh approval.
