---
name: ajo-manage-decision-item
description: Create, update, classify, attach content to, validate, and approve Adobe Journey Optimizer Decisioning items. Use for adding an item to an existing dynamic collection and policy chain, refreshing item content, changing item metadata or eligibility, or returning an item to draft and reapproving it. Do not use for full greenfield builds, collection or strategy changes, bulk imports, cleanup, or policy creation.
---

# Manage an AJO Decisioning item

Follow `../../references/write-safety-and-recovery.md` and `../../references/operation-manifest-and-output.md`. Delegate fragment authoring to `ajo-manage-expression-fragments` and rule authoring to `ajo-author-eligibility-rule`. The sandbox is fixed to `aepenablementfy21`.

## Required inputs

- Scenario: create one item, update one existing item, refresh its expression-fragment references, or return it to draft and reapprove it.
- New item: name, supplied business facts, optional description and date window, priority, eligibility intent, truthful content role, and any exact tenant custom attributes used by an existing collection filter.
- Existing item: exact item ID and the requested metadata, eligibility, lifecycle, or fragment-reference changes.
- Existing-chain onboarding: exact strategy or collection ID when known, plus the business reason the item should enter that chain. Never assume a tag or name controls membership.
- Optional content: exact published expression-fragment ID and stable reference key such as `hero`, `body`, or `footer`.

## Classify and preflight

1. Call `ajo_get_capabilities`; verify the Decisioning write gate. Verify the Content write gate only when fragment creation, update, or publication is required.
2. Call `ajo_decisioning_get_sandbox_summary`; require its effective sandbox to be exactly `aepenablementfy21` and state it before any write. Stop on a mismatch.
3. Call `ajo_decisioning_resolve_catalog_configuration`. Stop if the catalog cannot be resolved exactly.
4. Classify the request before proposing writes:
   - `existing-chain-addition`: create one item for an existing dynamic collection and leave the collection, strategy, placement, Decision Policy, campaign, and Journey unchanged.
   - `item-maintenance`: update allowlisted metadata, dates, priority, or the single eligibility-rule reference on an existing item.
   - `content-refresh`: attach, replace, or remove published expression-fragment references on an existing item.
   - `lifecycle-only`: validate and approve a draft item, or return an approved item to draft.
5. Create the operation manifest. Record reused resources and explicitly mark untouched upstream resources. Plan approval does not authorize a write.

## Existing-chain evidence

1. Resolve the exact chain from supplied IDs. Use `ajo_decisioning_get_strategy` and `ajo_decisioning_inspect_strategy` when a strategy ID is known; exact-get its referenced collection with `ajo_decisioning_get_collection`.
2. Read the persisted collection filter. Collection inclusion is supported only through exact `equals`, `in`, `and`, and `or` predicates; wildcards, partial matches, `$contains`, and inferred tags are unsupported.
3. Map every collection predicate to a proposed persisted item field. Treat placement tags as unrelated to item collection membership. Use tenant `customAttributes` only when the filter targets those exact tenant fields and the configured namespace supports them.
4. If the item cannot satisfy the existing filter, stop. Do not silently update the collection or strategy and do not rebuild the chain; route that work to a separately approved resource-maintenance or full-build workflow.
5. Explain that exact filter matching establishes configuration evidence only. It does not prove profile qualification, ranking outcome, propagation, policy selection, or delivery.

## Reuse eligibility and content

1. Search with `ajo_decisioning_list_items` and compare exact reads before creating an item. Never reuse by name alone or create a suffixed duplicate to bypass a collision.
2. For an existing eligibility rule, call `ajo_decisioning_get_rule` and `ajo_decisioning_verify_rule`; confirm persisted semantics and references. Rule existence does not prove attachment or profile qualification.
3. For a new or changed rule, invoke `ajo-author-eligibility-rule`. Rule creation is a separate approved write and the resulting verified rule ID must still be attached through the item create or update operation.
4. For existing content, call `ajo_content_get_fragment`, `ajo_content_get_fragment_publication_status`, and `ajo_content_get_live_fragment`. Only a published expression fragment with completed publication can be attached.
5. For new or changed content, invoke `ajo-manage-expression-fragments`. A skill handoff invalidates prior mutation approval.

## Create one item

1. Present the exact `ajo_decisioning_create_item` payload, including all collection-driving `customAttributes`, the verified `eligibilityRuleId` when applicable, lifecycle impact, and exact confirmation phrase. Obtain fresh approval.
2. Create the item once. New items are always draft. On timeout or unknown outcome, stop and reconcile by exact search/read; never create again blindly.
3. Call `ajo_decisioning_get_item` with the returned ID. Verify name, dates, priority, custom attributes, catalog, draft lifecycle, and `eligibilityRuleAttached` when a rule was supplied.
4. Do not recreate or update the existing collection, strategy, placement, policy, campaign, or Journey merely because this item was added.

## Update an existing item

1. Call `ajo_decisioning_get_item`. If it is approved, explain that returning it to draft removes it from current delivery eligibility, obtain separate approval, call `ajo_decisioning_return_item_to_draft`, and re-read it.
2. Fresh-get the draft item immediately before `ajo_decisioning_update_item`. Show the allowlisted diff and exact ETag, then obtain approval and update once.
3. The current update tool supports only name, description, start/end dates, priority, and `eligibilityRuleId`. It cannot update tenant `customAttributes`. If collection membership requires changing a custom attribute, stop and report a server-capability gap; do not replace the item implicitly.
4. Re-read the item and verify every requested persisted change, including `eligibilityRuleAttached` when applicable.

## Manage fragment references

1. Ensure the item is draft. If approved, use the separately approved return-to-draft sequence above.
2. To remove a reference, fresh-get the item, present the exact reference key and ETag, obtain approval, call `ajo_decisioning_remove_ajo_fragment`, and re-read.
3. To attach a reference, first verify the expression fragment's completed publication and live state. Fresh-get the item, present the exact item/key/fragment mapping and ETag, obtain approval, call `ajo_decisioning_attach_ajo_fragment`, and re-read.
4. Replacing a reference is two independently approved mutations: remove, re-read, then attach with the new ETag. Do not reuse approval or ETags across them.

## Validate and approve

1. Call `ajo_decisioning_validate_item_readiness`. Resolve every structural, date, rule, or fragment blocker before requesting approval.
2. If the item is intended for an existing chain, re-read its collection and strategy evidence after the final item mutation. Do not claim candidate membership beyond exact persisted-filter comparison.
3. When approval is requested, fresh-get the item, show lifecycle and delivery impact, obtain new approval, call `ajo_decisioning_approve_item`, and re-read it.
4. Report structural readiness, lifecycle/date readiness, collection-filter evidence, rule attachment, and fragment publication separately. Never claim that a profile qualifies or that the policy will select or deliver the item.

## Completion

Return the operation ledger from `../../references/operation-manifest-and-output.md`, including every reused and mutated resource, final item lifecycle and ETag, exact collection-filter evidence, unresolved limitations, and propagation or external verification steps. Never modify collections, strategies, placements, policies, campaigns, or Journeys in this skill.
