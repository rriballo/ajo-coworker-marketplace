---
name: ajo-journey-create-and-provision
description: Orchestrate native AJO Journey Create for a new DRAFT Journey, then independently validate public graph connectivity and the complete Journey/campaign/package/message identity chain before optional surface, content, Decisioning, or simulation handoffs. Do not use generic Journey graph writers, raw API requests, version cloning, proofing, publishing, or activation.
---

# Create and provision an AJO Journey email action

Operate only in sandbox `aepenablementfy21`. Follow:

- `../../references/journey-creation-and-provisioning.md`
- `../../references/write-safety-and-recovery.md`
- `../../references/operation-manifest-and-output.md`
- `../../references/campaign-scope-resolution.md`

This skill owns first-time creation orchestration through Adobe's native Journey Create capability. It does not make read-only or post-provisioning tools behave like a provisioning API, and native creation success is not sufficient evidence of valid topology or campaign-backed email provisioning.

## Use when

- The requested Journey does not exist and must start from one exact AEP audience.
- One campaign-backed email action must be provisioned with a DRAFT campaign version, email package, and message.
- Adobe's native Journey Create skill is available with the required permissions, or a dedicated provisioning mutation is advertised.

## Do not use when

- The Journey and email action already exist; use `ajo-journey-set-email-surface-and-content` for incremental work.
- Neither native Journey Create nor a dedicated provisioning capability is available.
- The proposed fallback is a raw `api_request`, direct `authoring/journeyVersions` POST, reconstructed graph, or cloned version.
- The request is to publish, activate, send a proof, or delete.

## Required inputs

- Journey name and approved description.
- Exact audience system ID and exact identity namespace.
- Schedule, timezone, throttling rate, delta mode, reentrance, and wait behavior.
- Whether post-provisioning work is requested: surface, content, Decisioning, simulation, or none.
- Supplied campaign/content facts needed by later skills. These do not authorize later writes.

## Preflight

1. Confirm native Journey Create availability and permissions. Call `ajo_get_capabilities` for the custom post-create validation and downstream tools.
2. If native Journey Create is unavailable, require a dedicated provisioning mutation that explicitly creates and returns the root Journey, DRAFT Journey version, entry node, email action/node, action UID, DRAFT campaign version, email package, and message. If neither path exists, stop with `external-action-required`.
3. Exact-get the audience definition. Never choose the newest audience, a same-name audience, or external `audienceId` without exact user selection.
4. Validate the identity namespace and all scheduling/behavior inputs required by the advertised mutation.
5. Call `list_journeys` only to detect collisions. Do not reuse or overwrite by name alone.
6. Create the operation manifest and show the exact provisioning payload, expected identities, risks, and confirmation. Plan approval is not write approval.

## Provision

1. Obtain fresh approval for the exact native Journey Create plan or dedicated provisioning mutation.
2. Invoke the selected supported creation path once. Never call a generic Journey writer as fallback and never retry an unknown outcome.
3. Record all returned IDs immediately.
4. Call `get_journey` with the returned root ID, then call `ajo_journey_validate_structure`. Require `publicTopologyConnected=true` and inspect every warning; this public check does not validate Adobe's private canvas model or hidden event configuration.
5. Require `campaignBackedActionsProvisioned=true` and `readyForSurfaceContentDecisioning=true`. A bare embedded `campaignId` is only a placeholder.
6. Call `ajo_journey_resolve_campaigns`; select only an exact action with `scopeUsable=true`. Then call `ajo_campaign_resolve_scope` and require the DRAFT campaign version/package/message to match the provisioning evidence.
7. Stop if topology is disconnected, private authoring alerts remain, the event configuration is unverified, or the campaign association is absent, incomplete, ambiguous, or inconsistent. A Journey container, graph node, campaign ID, or action UID alone is not success.

## Optional handoffs

Every handoff requires a new preflight and approval.

- Surface: invoke `ajo-journey-set-email-surface-and-content` in `surface-only` mode using the verified campaign version and package.
- Content: author source through `ajo-author-email-template`; for an advertised classic-inline content workflow, exact-read with `get_action_content`, present the complete replacement, call `update_action_content` once, then exact-read again. Applying source content creates a message copy.
- Decisioning: invoke `ajo-build-decisioning-experience` only after exact DRAFT message scope exists.
- Simulation: use Journey Simulation tools only after requested content is present. Poll async jobs and analyze the run; do not treat simulation as proof or activation.

## Output

Return the standard operation receipt containing:

- Audience and identity namespace evidence.
- Journey root/version, entry node, email node/action UID, campaign version, package, message, and optional surface IDs.
- Native Journey Create or provisioning mutation receipt plus `ajo_journey_validate_structure` evidence.
- Public topology connectivity, entry-configuration evidence, per-action `provisioningState`, and exact `scopeUsable` campaign identities.
- Each downstream handoff and its independently approved status.
- Any `outcome-unknown`, `external-action-required`, or AJO UI reconciliation step.

Keep all resources DRAFT. Do not claim proof, publication, activation, delivery, or profile qualification.
