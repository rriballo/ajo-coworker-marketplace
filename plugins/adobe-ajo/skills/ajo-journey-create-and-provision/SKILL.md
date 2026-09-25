---
name: ajo-journey-create-and-provision
description: Orchestrate first-time creation of one AJO DRAFT Journey with a Read Audience entry and one campaign-backed email action, then verify the complete Journey/campaign/package/message identity chain before optional surface, content, Decisioning, or simulation handoffs. Use only when a dedicated provisioning mutation is advertised. Do not use generic Journey graph writers, raw API requests, version cloning, proofing, publishing, or activation.
---

# Create and provision an AJO Journey email action

Operate only in sandbox `aepenablementfy21`. Follow:

- `../../references/journey-creation-and-provisioning.md`
- `../../references/write-safety-and-recovery.md`
- `../../references/operation-manifest-and-output.md`
- `../../references/campaign-scope-resolution.md`

This skill owns first-time creation orchestration. It does not make read-only or post-provisioning tools behave like a provisioning API.

## Use when

- The requested Journey does not exist and must start from one exact AEP audience.
- One campaign-backed email action must be provisioned with a DRAFT campaign version, email package, and message.
- The active catalog advertises a dedicated mutation that returns the complete identity chain required by the provisioning reference.

## Do not use when

- The Journey and email action already exist; use `ajo-journey-set-email-surface-and-content` for incremental work.
- Only `list_journeys`, `get_journey`, action-content, channel-configuration, or simulation tools are available.
- The proposed fallback is a raw `api_request`, direct `authoring/journeyVersions` POST, reconstructed graph, or cloned version.
- The request is to publish, activate, send a proof, or delete.

## Required inputs

- Journey name and approved description.
- Exact audience system ID and exact identity namespace.
- Schedule, timezone, throttling rate, delta mode, reentrance, and wait behavior.
- Whether post-provisioning work is requested: surface, content, Decisioning, simulation, or none.
- Supplied campaign/content facts needed by later skills. These do not authorize later writes.

## Preflight

1. Call `ajo_get_capabilities` and inspect the active tool list/schema.
2. Require a dedicated provisioning mutation that explicitly creates and returns the root Journey, DRAFT Journey version, entry node, email action/node, action UID, DRAFT campaign version, email package, and message. If absent, stop with `external-action-required` and name the missing capability.
3. Exact-get the audience definition. Never choose the newest audience, a same-name audience, or external `audienceId` without exact user selection.
4. Validate the identity namespace and all scheduling/behavior inputs required by the advertised mutation.
5. Call `list_journeys` only to detect collisions. Do not reuse or overwrite by name alone.
6. Create the operation manifest and show the exact provisioning payload, expected identities, risks, and confirmation. Plan approval is not write approval.

## Provision

1. Obtain fresh approval for the exact dedicated provisioning mutation.
2. Invoke it once. Never call a generic Journey writer as fallback and never retry an unknown outcome.
3. Record all returned IDs immediately.
4. Call `get_journey` with the returned root ID and require the expected Read Audience entry and email action.
5. Call `ajo_journey_resolve_campaigns`, then `ajo_campaign_resolve_scope`, and require the returned DRAFT campaign version/package/message to match the provisioning receipt.
6. Stop if the campaign association is absent, incomplete, ambiguous, or inconsistent. A Journey container or action UID alone is not success.

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
- Provisioning mutation and exact post-read evidence.
- Each downstream handoff and its independently approved status.
- Any `outcome-unknown`, `external-action-required`, or AJO UI reconciliation step.

Keep all resources DRAFT. Do not claim proof, publication, activation, delivery, or profile qualification.
