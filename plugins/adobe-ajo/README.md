# Adobe Journey Optimizer Plugin

This plugin connects CX Coworker to the remote AJO MCP server and provides safe workflow skills above its atomic tools.

## Fixed Environment

All skills target only the `aepenablementfy21` sandbox. A conflicting sandbox value is a stop condition, not a prompt to select another environment.

## Skills

- `ajo-discover`: read-only inventories, exact lookup, relationships, and ID resolution.
- `ajo-build-decisioning-experience`: coordinated end-to-end Decisioning build with a late policy commit point.
- `ajo-manage-expression-fragments`: expression-fragment authoring, publication, and optional item attachment.
- `ajo-author-email-template`: complete email Content Template authoring and source QA.
- `ajo-audit-decisioning`: read-only Decisioning dependency/readiness audit.
- `ajo-audit-message-readiness`: read-only template, campaign preview, and audience-definition evidence.
- `ajo-cleanup`: explicit manual-only destructive cleanup.

Shared references define campaign scope resolution, audience reads, email standards, write recovery, and operation receipts.

## Operating Model

- Skills call `ajo_get_capabilities`, enforce the fixed sandbox, and verify server write gates.
- Plan approval is not mutation approval. Every exact write requires fresh human approval.
- Updates, lifecycle changes, attachments, archives, and deletes use a fresh ETag from the same resource.
- Create timeouts and unknown asynchronous outcomes stop for reconciliation; non-idempotent operations are never retried blindly.
- Decision Policies support multiple selection strategies, optional manually pinned items, multiple approved fallback items, and an output count. The current tool requires at least one strategy and one fallback.
- A Journey is discovery context. Policy writes use one exact DRAFT Action campaign message scope returned by `ajo_campaign_resolve_scope`.
- The plugin never stores Adobe credentials.

## Product Boundaries

The MCP manages supported Content Library resources and Decisioning resources, resolves Action campaign message scope, creates Decision Policies, and binds placements. Campaign/Journey creation, copied Journey-message HTML retrieval, full Journey Simulation, proofing, and activation remain external AJO steps.

## Connection

Transport: Streamable HTTP, stateless POST requests.

Required headers are generated from:

```text
Authorization: Bearer ${ADOBE_IMS_TOKEN}
x-gw-ims-org-id: ${ADOBE_IMS_ORG_ID}
```

Replace the short-lived IMS user token after `401`. A missing organization header also causes authorization failure at the Adobe Runtime gateway.
