---
name: ajo-build-decisioning-experience
description: Plan and execute an end-to-end Adobe Journey Optimizer Decisioning build with fragments, items, rules, collections, formulas, strategies, placements, a campaign-scoped Decision Policy, and an email Content Template. Use for coordinated offer builds. Do not use for isolated content edits, read-only audits, cleanup, campaign/Journey creation, proofs, or activation.
---

# Build an AJO Decisioning experience

Follow `../../references/write-safety-and-recovery.md`, `../../references/operation-manifest-and-output.md`, and `../../references/campaign-scope-resolution.md`. Delegate detailed fragment and template work to their focused skills.

## Required inputs

- Business objective, channel, supplied offer facts/content, item names, date windows, output count, ranking and eligibility requirements.
- Target Action `campaignVersionId`, or a root Journey/campaign identifier from which it can be resolved.
- Decision Policy composition: one to thirty selection strategies, zero to thirty manual item IDs, one to thirty approved fallback item IDs, and an `itemCount` from one to thirty. The current MCP requires at least one strategy and one fallback.

The sandbox is always `aepenablementfy21`. Stop on any mismatch.

## Preflight and manifest

1. Call `ajo_get_capabilities`; verify both required write gates and state the fixed sandbox.
2. Resolve the Decisioning catalog.
3. Inventory reusable resources with `ajo_decisioning_list_items`, `ajo_decisioning_list_rules`, `ajo_decisioning_list_collections`, `ajo_decisioning_list_ranking_formulas`, `ajo_decisioning_list_strategies`, and `ajo_decisioning_list_placements`, plus relevant Content reads. Follow pagination.
4. If audience context is needed, follow `../../references/audience-read-contract.md`. Never convert stored PQL into a rule until every XDM field is confirmed by the user.
5. Resolve the exact DRAFT campaign message scope. Campaign/Journey creation remains external.
6. Create the operation manifest, including deterministic names, semantic reuse comparisons, dependencies, policy composition, scope, and external steps.
7. Present the ordered plan. Plan approval does not authorize any write.

## Build dependencies

For every mutation, present the exact payload and obtain separate approval.

1. Create and publish required expression fragments through `ajo-manage-expression-fragments`.
2. Create eligibility rules only when required and only with user-confirmed XDM paths. Never guess paths or reuse placeholder paths such as `membership.status` without confirmation.
3. Create items as drafts, then attach published expression fragments under stable reference keys using fresh item ETags.
4. Create exact-match collections using only validated `equals`, `in`, `and`, and `or` filters. Wildcards, partial matching, and `$contains` are unsupported.
5. Create ranking formulas only when static priority is insufficient.
6. Create each strategy with an exact collection and optional confirmed rule/formula. Strategies never contain placements.
7. Reuse or create the email placement after semantic comparison.

## Validate before policy commit

1. Inspect every selected strategy and resolve all dependencies.
2. Validate every created, manual, fallback, and known candidate item relevant to the build.
3. Fresh-get and separately approve each required draft item. Re-read each afterward and require approved fallback items.
4. Re-read strategies, placement, and exact DRAFT scope.
5. Present a final policy preflight containing scope, all strategy IDs, manual item IDs, approved fallback IDs, placement, and output count. Stop on any unresolved dependency.

## Policy and template commit

1. Obtain separate approval and call `ajo_decisioning_create_decision_policy` with the complete scope unchanged. Record the UUID immediately. On an unknown outcome, stop for AJO UI reconciliation; never retry blindly.
2. Obtain a new approval and call `ajo_decisioning_bind_decision_policy_placement` with the same scope and exact placement name.
3. Preferred path: invoke `ajo-author-email-template` after binding and save once with the real policy UUID, placement name, and fragment reference key.
4. Contingency path only for a pre-existing template: use the literal `<!-- offer -->` marker, then fresh-get and separately approve backfill after policy binding.
5. Never hand-author personalization syntax. The MCP-generated Decision Policy loop is the sole permitted loop.

## Completion

Re-read all mutated resources and the stored template. Return the operation ledger from `../../references/operation-manifest-and-output.md`. Supported campaign preview may follow the campaign reference contract, but template QA and preview do not simulate Journey execution, eligibility, proofing, activation, or delivery.

Never archive or delete during this workflow.
