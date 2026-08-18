---
name: ajo-audit-decisioning
description: Audit Adobe Journey Optimizer Decisioning items, collections, strategies, rules, formulas, placements, and fragment references for readiness and configuration problems. This workflow is read-only.
---

# Audit AJO Decisioning configuration

Remain read-only. Do not repair findings during the audit.

1. Call `ajo_get_capabilities` and identify the audit scope.
2. Inventory the relevant resources and follow pagination to avoid incomplete conclusions.
3. Re-read individual resources before reporting detailed fields or ETags.
4. Validate item readiness and separate:
   - Structural readiness for approval.
   - Current approval status.
   - Current date-window eligibility.
   - Delivery limitations that cannot be simulated.
5. Inspect strategy dependencies and verify collection, eligibility-rule, and ranking-formula references.
6. Review collection filters for supported exact `equals`, `in`, `and`, and `or` semantics.
7. For content references, verify the fragment is an expression fragment, publication completed, and live content is available.
8. If a target Action campaign version ID is supplied, resolve its message scope with `ajo_campaign_resolve_scope`; verify the optional journey version ID and report the exact version, package, and message IDs. Do not confuse version IDs with root campaign or journey IDs.
9. When audience targeting is in scope, retrieve the AEP audience definition by system `id` and report its stored expression, schema, merge policy, evaluation mode, origin, lifecycle, labels, and dependencies. Do not expose or infer members.
10. If only a Journey ID is supplied, use `ajo_journey_resolve_campaigns` to report its campaign action nodes. Require exact selection when multiple root campaigns exist. When a supported selected root campaign ID and test profile are available, use `ajo_campaign_preview_content` to inspect rendered variants. Treat rendering as message-content evidence only, not Journey path simulation or proof of audience qualification or delivery.
11. Identify duplicate or apparently reusable resources, but do not assume matching names are semantically equivalent.
12. Report findings by severity with resource type, name, ID, evidence, impact, and recommended next action.

State these blind spots in the report: Journey campaign discovery plus campaign preview renders action content but cannot simulate Journey paths, branching, waits, events, consent, profile eligibility, proofs, or delivery; full Journey Simulation remains in the AJO UI. Audience reads do not return members or estimates; the MCP cannot inspect every campaign or journey policy reference after binding, send proofs, create campaigns or journeys, or activate them. It can author Decision Policies and bind placements when a valid campaign message scope has been resolved.
