# Adobe Journey Optimizer Plugin

This plugin connects CX Coworker to the remote `adobe-ajo-mcp` server and provides workflow skills above its atomic MCP tools.

## Operating Model

- MCP tools perform typed, independently guarded Adobe API operations.
- Skills teach Coworker how to discover and sequence those operations.
- Every write requires an enabled server-side write gate, an intent summary, and explicit human approval.
- Updates and destructive operations require a fresh ETag from the same resource.
- The plugin never stores Adobe credentials.

Start a new task by asking Coworker to call `ajo_get_capabilities`. For an end-to-end implementation, invoke the `ajo-build-decisioning-experience` skill. Use `ajo-cleanup` only through an explicit user invocation.

## Product Boundaries

The MCP can manage supported Content Library expression fragments, Decisioning persistence resources, resolve Action campaign message scope, and create Decision Policies with placement binding. Campaign or journey creation, profile simulation, proofing, and activation remain in Adobe Journey Optimizer or Coworker campaign workflows.

## Connection

Transport: Streamable HTTP, stateless POST requests.

Required headers are generated from:

```text
Authorization: Bearer ${ADOBE_IMS_TOKEN}
x-gw-ims-org-id: ${ADOBE_IMS_ORG_ID}
```

Replace the short-lived IMS user token when the server returns `401`. A missing organization header also results in `401` at the Adobe Runtime gateway.
