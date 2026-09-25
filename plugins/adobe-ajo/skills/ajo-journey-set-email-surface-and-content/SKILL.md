---
name: ajo-journey-set-email-surface-and-content
description: Assign an existing verified email branding surface to one exact package in an existing AJO DRAFT campaign version and optionally apply a Content Template as a separate mutation. Use for surface-only, content-only, or combined work. Do not create or clone Journeys, campaign versions, surfaces, or policies.
metadata:
  author: user
---

# Set an email surface and optionally apply content

Operate only in sandbox `aepenablementfy21`. A conflicting sandbox is a stop condition. Follow:

- `../../references/write-safety-and-recovery.md`
- `../../references/operation-manifest-and-output.md`
- `../../references/campaign-scope-resolution.md`

This skill targets one exact existing DRAFT campaign version and one exact email package. Surface assignment and content/template binding are independent mutations and require separate approvals.

## Use when

- An existing Journey email action reports that no channel configuration is selected.
- The user wants to assign an existing email branding preset to one exact package.
- The user wants surface-only, content-only, or combined surface then content work.

## Do not use when

- The request is to create a Journey, campaign, version, or channel configuration.
- The target is LIVE, published, ambiguous, or outside the fixed sandbox.
- The only available operation would recreate or clone the Journey/version.
- The request is to create/bind a Decision Policy, send a proof, simulate a Journey path, or activate.

## Required inputs

- Mode: `surface-only`, `content-only`, or `combined`.
- Root Journey ID when Journey ownership/discovery is needed.
- Exact `campaignVersionId` and exact email `packageId`.
- For surface work: exact requested `surfaceId`, type `brandingPresetId`, fresh current package surface, and fresh version `modifiedAt`.
- For content work: exact Journey draft version UID, exact node ID, template ID, and subject when the HTML template has no stored subject.
- Existing message ID and Decision Policy scope/references when present, so preservation can be verified.

## Preflight

1. Call `ajo_get_capabilities`.
2. Verify the fixed sandbox and require the exact Campaign authoring gate/capability for surface mutation. Content or Decisioning gates do not authorize it.
3. Resolve the target:
   - With a root Journey ID, call `ajo_journey_resolve_campaigns`, inspect `recovery`, `actions`, `campaigns`, and `selectionRequired`, then select one exact association.
   - Pass its exact `campaignVersionId` and package selector to `ajo_campaign_resolve_scope`.
   - Never substitute a Journey ID, Journey version ID, node ID, action UID, package ID, message ID, or template ID for a campaign version ID.
4. Require DRAFT and record the exact campaign/version/package/message IDs, current surface, `modifiedAt`, ETag when returned, message ID, and Decision Policy references in the operation manifest.
5. If the requested surface is already assigned, mark surface work as a no-op and do not call the mutation.

## Surface workflow

1. Call `ajo_campaign_list_surfaces` with `channel: email` and `type: brandingPresetId`.
2. Select by exact returned ID, never by name alone.
3. Call `ajo_campaign_get_surface` with the exact ID. Require matching identity, email channel, and type. Treat active/status evidence from Adobe as required when returned.
4. Re-run `ajo_campaign_resolve_scope` immediately before approval and copy the package's current surface. Obtain the exact version `modifiedAt` from the corresponding fresh version evidence. Stop if either changed.
5. Present the exact diff, preserved package/message/policy identifiers, race-window warning, and confirmation:
   `set surface <surfaceId> on package <packageId> in version <campaignVersionId>`
6. After fresh human approval, call `ajo_campaign_set_package_surface` once with the exact DRAFT version/package/surface, current-surface preconditions, `expectedModifiedAt`, intent, and confirmation.
7. Never retry automatically. A timeout, 5xx, or failed post-read can mean the mutation committed; mark `outcome-unknown` and reconcile through exact read or AJO UI.
8. Re-run `ajo_campaign_resolve_scope` and verify:
   - Same campaign/version/package/message identities.
   - Requested package surface persisted.
   - Non-target packages/messages and Decision Policy references did not change.

The mutation changes only the package surface. It does not apply a template, provision content, create or bind a policy, proof, publish, or activate.

## Content workflow

Content work requires a new approval after a fresh target read. Surface approval never authorizes content binding.

1. Verify the exact stored Content Template and its source QA evidence before applying it.
2. Call `tadforge-bind-template-to-journey-action` only when that deployment-specific tool is advertised. Use:
   - `sandbox: aepenablementfy21`
   - `journeyVersionId`: existing draft version UID
   - `nodeId`: email node ID, never `actionUID`
   - `templateId`
   - required subject when applicable
3. For email, do not use a push/SMS `surfaceId` override as a surface mutation.
4. Re-read the same draft and verify the surface and provisioned `messageId`. Do not claim copied inline HTML was inspected unless an advertised capability returns it; final copied-message validation remains in AJO UI.
5. If an existing message ID or Decision Policy scope changed unexpectedly, stop before further writes.

## Decisioning preservation

- Surface/content approval does not authorize Decision Policy creation or placement binding.
- Snapshot pre-existing campaign scope and policy references before each mutation and compare them afterward.
- If policy work is requested, hand off the unchanged resolved scope to the Decisioning build workflow and obtain fresh approval.
- A composite Journey-inline `scope.campaignId` is valid metadata but is not a root campaign lookup or Simulation ID.

## Output

Return the standard operation receipt with:

- Fixed sandbox and mode.
- Journey/campaign version/package/message/node/action identifiers.
- Before/after surface and version `modifiedAt`/ETag evidence.
- Content template ID and provisioned message ID when applicable.
- Policy-scope preservation result.
- Each separately approved mutation and confirmation.
- Verification result, warnings, outcome-unknown state, and required AJO UI checks.

Keep the Journey/campaign version in DRAFT. Do not claim proof, simulation, delivery, or activation.
