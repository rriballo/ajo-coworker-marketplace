---
name: ajo-audit-decisioning
description: Read-only readiness and configuration audit of Adobe Journey Optimizer Decisioning items, collections, strategies, eligibility rules, formulas, placements, and expression-fragment references. Use for evidence-based Decisioning findings. Do not use for general inventory, message/template QA, mutations, or cleanup.
---

# Audit AJO Decisioning configuration

Remain read-only. The sandbox is fixed to `aepenablementfy21`; stop on mismatch.

## Inputs and scope

Require resource IDs, naming scope, or exact campaign version context sufficient to bound the audit. State exclusions before broad conclusions.

## Workflow

1. Call `ajo_get_capabilities`.
2. Inventory with `ajo_decisioning_list_items`, `ajo_decisioning_list_rules`, `ajo_decisioning_list_collections`, `ajo_decisioning_list_ranking_formulas`, `ajo_decisioning_list_strategies`, and `ajo_decisioning_list_placements`; follow pagination.
3. Exact-get resources before reporting detailed fields or ETags.
4. Validate item readiness and distinguish structural readiness, approval state, date-window eligibility, and unsimulated delivery eligibility.
5. Inspect every in-scope strategy and verify collection, rule, formula, ranking, and candidate-item dependencies.
6. Verify collection filters use supported exact `equals`, `in`, `and`, or `or` semantics.
7. For content references, verify expression-fragment type, completed publication, live content, and stable reference keys.
8. When policy scope evidence is needed, follow `../../references/campaign-scope-resolution.md`. The API cannot list/delete policies or enumerate every invisible campaign/Journey reference.
9. Identify duplicate/reusable candidates, but compare semantics and never infer equivalence from names.

## Output

Report findings first, ordered by severity. For each finding include resource type, name, ID, evidence, impact, and recommended next action. Then list confirmed strengths, blind spots, and testing gaps.

Do not repair findings. Message source, audience, and personalized preview checks belong to `ajo-audit-message-readiness`.
