# Adobe AJO CX Coworker Marketplace

CX Coworker marketplace for the Adobe Journey Optimizer MCP integration.

Version 2 separates fragment and email authoring, adds message-readiness auditing, enforces the fixed `aepenablementfy21` sandbox, and introduces shared write-recovery and campaign-scope contracts.

## Configuration

Provide these values through the host environment or managed secret configuration:

```text
ADOBE_IMS_TOKEN=<fresh Adobe IMS user access token>
ADOBE_IMS_ORG_ID=<your IMS organization ID>
AJO_MCP_URL=<your deployed MCP endpoint>
```

Do not commit bearer tokens, tenant identifiers, or environment-specific MCP endpoints. The plugin intentionally defines no defaults for these values.

See `plugins/adobe-ajo/README.md` for plugin usage and operating boundaries.
