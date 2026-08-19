---
name: ajo-cleanup
description: Plan and execute explicitly requested destructive cleanup of exact Adobe Journey Optimizer Decisioning resources in reverse dependency order. Never invoke automatically or interpret tidy, replace, start over, or remove duplicates as deletion consent.
disable-model-invocation: true
---

# Clean up AJO Decisioning resources

This is a destructive, manual-only workflow. Follow `../../references/write-safety-and-recovery.md` and `../../references/operation-manifest-and-output.md`. The sandbox is fixed to `aepenablementfy21`.

## Required inputs

Require immutable IDs for every requested target, explicit deletion/archive intent, and external verification of campaign/Journey policy references when relevant. Names alone are insufficient.

## Workflow

1. Call `ajo_get_capabilities`; verify the Decisioning write gate and fixed sandbox.
2. Inventory targets with `ajo_decisioning_list_items`, `ajo_decisioning_list_rules`, `ajo_decisioning_list_collections`, `ajo_decisioning_list_ranking_formulas`, `ajo_decisioning_list_strategies`, and `ajo_decisioning_list_placements`, followed by exact gets. Show name, ID, lifecycle, configuration, references, and ETag.
3. Inspect visible dependencies. Warn that campaign/Journey Decision Policy references are not fully visible. Require recorded user attestation or external AJO verification that those references were removed.
4. If an Action campaign version ID is supplied, use `ajo_campaign_resolve_scope` only to identify message scope; it does not enumerate policy references.
5. Present reverse dependency order: strategies, collections, formulas/rules, then items. Placements have no delete tool and Content fragments cannot be deleted through the public API.
6. Before every archive/delete, fresh-get the exact resource, show its current state and ETag, and obtain separate approval.
7. Execute one mutation, then re-read/relist before continuing. Stop if new shared dependencies appear.

## Resume and unknown outcomes

- Already absent after exact verification is an idempotent completed state.
- A transport failure or unknown delete outcome is not absence. Stop and reconcile before continuing or retrying.
- Never archive as a substitute for failed deletion without new explicit approval and acknowledgement that archival is irreversible.

Return a ledger of completed, already absent, blocked, outcome-unknown, and externally required actions.
