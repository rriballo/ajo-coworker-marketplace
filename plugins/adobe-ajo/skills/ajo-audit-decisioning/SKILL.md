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
8. If a target Action campaign ID is supplied, resolve its draft or live message scope with `ajo_campaign_resolve_scope`; verify the optional journey ID and report the exact version, package, and message IDs.
9. Identify duplicate or apparently reusable resources, but do not assume matching names are semantically equivalent.
10. Report findings by severity with resource type, name, ID, evidence, impact, and recommended next action.

State these blind spots in the report: this MCP cannot simulate profile eligibility or delivery, inspect every campaign or journey policy reference after binding, proof content, create campaigns or journeys, or activate campaigns and journeys. It can author Decision Policies and bind placements when a valid campaign message scope has been resolved.
