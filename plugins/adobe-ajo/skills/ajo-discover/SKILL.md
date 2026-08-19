---
name: ajo-discover
description: Discover and explain Adobe Journey Optimizer Content Library, Decisioning, Journey campaign actions, saved template source content, campaign preview, and AEP audience definitions. Use for inventories, relationship questions, reusable-resource searches, audience details, and current-state analysis. This workflow is read-only.
---

# Discover Adobe Journey Optimizer resources

Use only read-only AJO MCP operations during this workflow.

1. Call `ajo_get_capabilities` if the current session does not already contain the server guidance.
2. Clarify the sandbox, business objective, resource type, naming clues, and desired level of detail.
3. Use the relevant `ajo_content_list_*`, `ajo_decisioning_list_*`, or `ajo_aep_list_audiences` tools. Preserve Adobe pagination values exactly and never invent property filters.
4. Retrieve a resource with its corresponding `get` tool before making claims about its full configuration.
5. For a strategy, call `ajo_decisioning_inspect_strategy` to resolve its collection, eligibility rule, and ranking formula relationships.
6. For an item, use `ajo_decisioning_validate_item_readiness` when the user asks whether it can be approved or delivered.
7. Distinguish draft resource state from published/live state for Content fragments.
8. For email source QA, use `ajo_content_list_templates` only when the template ID is unknown, then call `ajo_content_get_template` with the exact ID. Report normalized `data.qa` subject, HTML, text, headers, source shape, and ETag, and whether HTML still contains the pending `<!-- offer -->` marker or an injected policy block.
9. When the user provides an Action campaign version ID, use `ajo_campaign_resolve_scope` with `campaignVersionId`; supply the associated `journeyVersionId` when available so ownership is verified. Root campaign IDs remain a compatibility fallback. If selection is ambiguous, request exact package/message IDs rather than guessing.
10. For audiences, list definitions first and use `ajo_aep_get_audience` with `data.children[].id`, not `audienceId`. Report stored PQL/schema, merge policy, evaluation modes, origin, lifecycle, labels, and dependencies when present. Never claim these reads expose members, size estimates, or profile qualification.
11. When only a Journey ID is known, call `ajo_journey_resolve_campaigns`. If multiple action candidates are returned, show them and ask the user to select an exact `nodeId` or `campaignId`; never guess. This discovery does not retrieve Journey-inline HTML.
12. Treat the saved Content Template as the source of record for API-based HTML QA. Applying it to a Journey message creates a copy, so later Journey-side edits are outside this evidence and must not be attributed to the stored template.
13. Call `ajo_campaign_preview_content` only when the resolver returns a non-null `previewCampaignId` and `simulationPreview.supported` is true, and a test-profile identity is available. If Simulation is unsupported or its user token/permission is missing, retain the template QA findings and report the remediation.
14. Journey resolution, template retrieval, and campaign preview do not simulate Journey paths, branching, waits, events, consent, eligibility, proofs, or delivery. Full Journey Simulation remains an AJO UI workflow.
15. Return a concise inventory with resource names, IDs, lifecycle state, relationships, missing dependencies, Journey campaign candidates, saved-template QA findings, supported preview variants, audience details, and recommended next reads.

Do not create, update, publish, approve, archive, or delete resources in this skill. If the user requests a change, present the findings first and move to the appropriate write-oriented skill only after explicit approval.
