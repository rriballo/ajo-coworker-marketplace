# Adobe Journey Optimizer Plugin

This plugin connects CX Coworker to the remote AJO MCP server and provides safe workflow skills above its atomic tools.

## Fixed Environment

All skills target only the `aepenablementfy21` sandbox. A conflicting sandbox value is a stop condition, not a prompt to select another environment.

## Skills

- `ajo-discover`: read-only inventories, exact lookup, relationships, and ID resolution.
- `ajo-author-eligibility-rule`: Profile-schema-backed structured eligibility, audience rules, advanced raw-PQL diagnostics, and persisted-rule verification.
- `ajo-build-decisioning-experience`: coordinated end-to-end Decisioning build with a late policy commit point.
- `ajo-manage-decision-item`: incremental item creation, existing-chain onboarding, metadata/content maintenance, readiness, and lifecycle approval without rebuilding upstream resources.
- `ajo-manage-expression-fragments`: expression-fragment authoring, publication, and optional item attachment.
- `ajo-author-email-template`: complete email Content Template authoring and source QA.
- `ajo-audit-decisioning`: read-only Decisioning dependency/readiness audit.
- `ajo-audit-message-readiness`: read-only template, campaign preview, and audience-definition evidence.
- `ajo-cleanup`: explicit manual-only destructive cleanup.
- `ajo-journey-set-email-surface-and-content`: assign one verified email branding surface to one exact existing DRAFT package and optionally apply content under a separate approval.
- `nordvell-brand-guidelines`: standard NORDVELL POC text header, reusable email hierarchy, fixed footer, voice, Decisioning-content boundaries, secondary WPP Enterprise Solution attribution, and explicit bracket placeholders without campaign hardcoding.

Shared references define campaign scope resolution, audience reads, eligibility-rule PQL, email standards, the standard NORDVELL POC presentation shell, write recovery, and operation receipts.

## Operating Model

- Skills call `ajo_get_capabilities`, enforce the fixed sandbox, and verify server write gates.
- Plan approval is not mutation approval. Every exact write requires fresh human approval.
- Updates, lifecycle changes, attachments, archives, and deletes use a fresh ETag from the same resource.
- Create timeouts and unknown asynchronous outcomes stop for reconciliation; non-idempotent operations are never retried blindly.
- Decision Policies support multiple selection strategies, optional manually pinned items, multiple approved fallback items, and an output count. The current tool requires at least one strategy and one fallback.
- Eligibility is explicit: unrestricted, an existing rule, schema-backed `eligibilityAst`, exact audience membership, or advanced raw PQL. The Profile schema tools are distinct from Offer Item schema inspection. Validation and verification separate schema, audience, persistence, visual-model, attachment, and profile-simulation evidence. Rule creation and attachment are separate approved writes.
- A Journey is discovery context. Policy writes use one exact DRAFT Action campaign message scope returned by `ajo_campaign_resolve_scope`.
- Email package surface assignment uses exact surface list/get evidence and a separate Campaign authoring gate. Surface and template binding are separate mutations with separate approvals; neither authorizes policy work.
- The plugin never stores Adobe credentials.

## Product Boundaries

The MCP manages supported Content Library resources and Decisioning resources, resolves Action campaign message scope, assigns an existing email branding surface to one exact DRAFT package when the Campaign authoring gate is enabled, creates Decision Policies, and binds placements. Campaign/Journey/version or channel-configuration creation, copied Journey-message HTML retrieval, full Journey Simulation, proofing, and activation remain external AJO steps.

## Connection

Transport: Streamable HTTP, stateless POST requests.

Required headers are generated from:

```text
Authorization: Bearer ${ADOBE_IMS_TOKEN}
x-gw-ims-org-id: ${ADOBE_IMS_ORG_ID}
```

Replace the short-lived IMS user token after `401`. A missing organization header also causes authorization failure at the Adobe Runtime gateway.
