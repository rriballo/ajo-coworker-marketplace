---
name: ajo-discover
description: Discover and explain Adobe Journey Optimizer Content Library and Decisioning resources. Use for inventories, relationship questions, reusable-resource searches, and current-state analysis. This workflow is read-only.
---

# Discover Adobe Journey Optimizer resources

Use only read-only AJO MCP operations during this workflow.

1. Call `ajo_get_capabilities` if the current session does not already contain the server guidance.
2. Clarify the sandbox, business objective, resource type, naming clues, and desired level of detail.
3. Use the relevant `ajo_content_list_*` or `ajo_decisioning_list_*` tools. Preserve Adobe pagination cursors exactly and never invent property filters.
4. Retrieve a resource with its corresponding `get` tool before making claims about its full configuration.
5. For a strategy, call `ajo_decisioning_inspect_strategy` to resolve its collection, eligibility rule, and ranking formula relationships.
6. For an item, use `ajo_decisioning_validate_item_readiness` when the user asks whether it can be approved or delivered.
7. Distinguish draft resource state from published/live state for Content fragments.
8. For an email template, report whether its HTML still contains the pending `<!-- offer -->` marker (no Decision Policy block yet) or an injected policy block.
9. When the user provides an Action campaign ID, use `ajo_campaign_resolve_scope` to resolve its draft or live `versionId`, email `packageId`, and `messageId`. Supply a journey ID when available so campaign ownership is verified. If selection is ambiguous, request exact package/message IDs rather than guessing.
10. Return a concise inventory with resource names, IDs, lifecycle state, relationships, missing dependencies, resolved campaign scope, and recommended next reads.

Do not create, update, publish, approve, archive, or delete resources in this skill. If the user requests a change, present the findings first and move to the appropriate write-oriented skill only after explicit approval.
