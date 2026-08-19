---
name: ajo-build-decisioning-experience
description: Plan and implement an Adobe Journey Optimizer Decisioning experience using fragments, draft items, exact-match collections, eligibility rules, ranking formulas, strategies, placements, and decision policies. Use for end-to-end offer or decisioning builds.
---

# Build an AJO Decisioning experience

Treat MCP confirmation arguments as operation guards, not evidence of human consent. Obtain explicit approval in the conversation before every write.

## Plan

1. Call `ajo_get_capabilities` and summarize the supported boundaries.
2. Clarify the business objective, channels, content roles, audience logic, item names, dates, ranking behavior, desired output count, and any existing AEP audience name or ID.
3. Discover reusable fragments, rules, items, collections, formulas, strategies, placements, and relevant AEP audience definitions before proposing new resources. Use `ajo_aep_get_audience` with the list response's system `id`; an existing audience's stored PQL can inform requirements but must not be copied into a Decisioning rule without confirming every XDM path.
4. Resolve the configured Decisioning catalog before creating items or collections.
5. Confirm that Coworker or AJO has already created the target Action campaign or journey. This MCP does not create campaigns or journeys.
6. If a campaign version ID is supplied, pass it directly as `campaignVersionId` to `ajo_campaign_resolve_scope`; optionally include the root `journeyId` for ownership verification. If only a Journey ID is known, call `ajo_journey_resolve_campaigns`. Treat `actionCount` as the action-node count and `associationCount` only as embedded campaign metadata. A positive `actionCount` with zero associations means Adobe omitted campaign identifiers, not that the action is absent; ask Coworker for the selected Action `campaignVersionId`. Never substitute a Journey ID, Journey version ID, node ID, or action UID. Require exact selection when multiple resolved actions exist. Root campaign IDs remain a compatibility fallback. If package or message selection is ambiguous, ask for the exact IDs and retry the resolver.
7. Present an ordered plan listing every proposed write, reference, lifecycle effect, resolved scope, and external AJO step. Do not write until the user approves the plan.

## Build

1. Create and publish required AJO expression fragments through the Content workflow.
2. Create reusable eligibility rules only when profile constraints are required. Use only XDM field paths the user confirms exist in the org profile schema. Never invent paths (for example `loyaltyDetails.tier` or `transactionSummary.monthlyAvgSpend`) and never reuse placeholder paths such as `membership.status` without user confirmation. If no valid path is available, pause and ask the user rather than writing a rule with guessed fields.
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
9. Create the Decision Policy with `ajo_decisioning_create_decision_policy`, providing at least one selection strategy and one fallback item, and pass the complete `scope` returned by `ajo_campaign_resolve_scope` unchanged. Then bind it to the email placement by name with `ajo_decisioning_bind_decision_policy_placement`, passing the same exact scope (resolve the name from `ajo_decisioning_list_placements`). Record the returned policy UUID; the campaigns service does not expose policy listing or deletion, and a policy cannot be reused across campaign message scopes.
10. Create and save the email as an AJO Content Template through the Content workflow, record its returned template ID, and use a Decision Policy placeholder rather than a fake block. Build a complete responsive document per the email structure guidance (preheader, header, hero, varied body blocks such as cards, split sections, and voucher cards, and a footer with unsubscribe), and place one `<!-- offer -->` marker where the selected offers should render:
   - Without a policy ID: include the `<!-- offer -->` marker comment where the offer block will render. The template saves with a plain comment and the run continues; report the template as pending a policy ID.
   - With a policy ID: after the Decision Policy exists and its real UUID is known, pass `decisionPolicyId`, the exact bound `placementName`, and `referenceKey` to the template tool. The server injects the valid placement-scoped `{{#each decisionPolicy.<id>.placement.<placementName>.items as |item|}}` block at save time.
   - Never hand-write `{{ }}` or `{% %}` syntax. AJO personalization is not Handlebars: `{{#if}}`, `{{else}}`, and placeholder identifiers such as `<POLICY_ID>` are rejected at save even inside HTML comments.

Before each write, show the resource, exact change, references, risk, and confirmation phrase. For updates, retrieve the same resource immediately beforehand and copy its current `metadata.etag`.

## Validate And Approve

1. Inspect every strategy and report unresolved dependencies.
2. Validate every item with `ajo_decisioning_validate_item_readiness`.
3. Obtain a separate explicit approval before approving each item.
4. Re-read every mutated resource and report its final ID, lifecycle, ETag, and warnings.
5. Re-read every mutated resource and report its final ID, lifecycle, ETag, and warnings. A template saved with the `<!-- offer -->` marker must be backfilled with `ajo_content_update_email_template` once the real Decision Policy UUID exists (from `ajo_decisioning_create_decision_policy`).
6. After content is assembled, re-read the recorded template ID with `ajo_content_get_template` and QA the normalized subject, HTML, text, headers, source shape, marker/policy block, and ETag. Apply the reviewed template in the AJO UI; this creates a copy, so later Journey-side edits do not update the stored template. Resolve the Journey with `ajo_journey_resolve_campaigns` when delivery identifiers are needed. Do not treat zero associations as no action when `actionCount` is positive, and never choose silently when `selectionRequired` is true. Call `ajo_campaign_preview_content` only when the resolver returns `simulationPreview.supported: true` with a non-null `previewCampaignId` and a test profile is available. Template QA is not profile-personalized rendering, and neither operation simulates the Journey path, branching, waits, events, consent, eligibility, proof, or delivery. Full Journey Simulation, proofing, and activation remain external AJO steps.

Never automatically retry an ETag mismatch. Never archive or delete as part of a build workflow.
