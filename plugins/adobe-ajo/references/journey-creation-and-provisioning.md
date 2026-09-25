# Journey creation and provisioning

Creating a Journey is not equivalent to posting a graph. A usable email action requires a consistent identity chain across Journey Authoring and Campaign Service.

## Required capability

Use first-time creation only when the active tool catalog advertises a dedicated Journey creation/provisioning mutation whose schema explicitly creates and returns:

- Root Journey ID and DRAFT Journey version ID.
- Read Audience/segment-trigger node and exact audience system ID.
- Email campaign action node ID and action UID.
- Associated DRAFT campaign version ID.
- Email package ID and message ID.

`list_journeys`, `get_journey`, channel-configuration reads, action-content reads/writes, and Journey Simulation tools do not satisfy this requirement. They operate on resources that already exist.

If no qualifying mutation is advertised, return `external-action-required`. Never substitute a generic API request, raw `authoring/journeyVersions` POST, prose-to-graph reconstruction, or version clone.

## Required inputs

- Fixed sandbox `aepenablementfy21`.
- Stable Journey name and description.
- Exact AEP audience system ID, selected by exact read rather than name alone.
- Exact identity namespace.
- Schedule mode and timestamps, including timezone where applicable.
- Throttling rate, delta mode, reentrance behavior, and any wait behavior.
- Email channel intent and whether surface/content/Decisioning should follow provisioning.

Do not invent defaults when the provisioning tool requires a value. Show every chosen value before approval.

## Provisioning contract

1. Call `ajo_get_capabilities` and inspect all active tool schemas.
2. Confirm one dedicated provisioning mutation has the complete output contract above. Do not infer support from a generic Journey writer.
3. Exact-get the selected audience and namespace evidence before planning.
4. Search for same-name Journeys. A name collision is not permission to reuse, overwrite, or create a suffixed duplicate.
5. Build the exact creation manifest and obtain fresh approval for the provisioning mutation.
6. Invoke the mutation once. Never retry automatically after a timeout, 5xx, malformed response, or lost connection.
7. Record every returned identity immediately.
8. Call `get_journey` and the custom MCP Journey/campaign resolvers. Require one exact DRAFT association with the returned campaign version, package, and message.
9. Stop if identities are missing, ambiguous, inconsistent, or point outside the fixed sandbox.

## Post-provisioning handoffs

After the identity chain is verified:

- Surface assignment: hand off to `ajo-journey-set-email-surface-and-content` under a new approval.
- Stored template authoring: hand off to `ajo-author-email-template`.
- Existing classic inline-message content: use `get_action_content` before any full replacement with `update_action_content`; re-read afterward. Open Message v2 is unsupported by those tools.
- Decisioning: resolve exact DRAFT campaign scope, then hand off to `ajo-build-decisioning-experience`.
- Simulation: start only after provisioning and requested content are complete. Simulation does not prove delivery, consent, capping, proofing, or activation.

Each handoff invalidates prior mutation approval.

## Recovery

- On an unknown creation outcome, mark `outcome-unknown` and reconcile with exact Journey and campaign reads. Do not create another container or version.
- If Journey exists but campaign/package/message provisioning is incomplete, stop. Do not repair it with guessed IDs or raw graph updates.
- If post-provisioning surface, content, or Decisioning work fails, preserve the verified identity chain and resume only the failed downstream stage.

## Evidence boundary

Successful creation requires both the mutation receipt and exact post-reads. A Journey container alone, a graph node alone, or an action UID without campaign version/package/message IDs is incomplete provisioning.
