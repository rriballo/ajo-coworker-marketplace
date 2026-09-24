---
name: ajo-journey-set-email-surface-and-content
description: Set the email channel surface on an AJO journey's campaign/email node
  and apply a content template to it, in one consistent pass. Use when a journey's
  email node shows "Channel configuration not selected" or has an empty surface/message,
  or when asked to configure the email surface and/or add message content to a journey.
metadata:
  author: user
---

# AJO — Set email surface + apply content on a journey node

Configures an Adobe Journey Optimizer journey's email (campaign) node: sets the channel
**surface**, then applies a **content template**. Sandbox is `aepenablementfy21` unless the
user says otherwise. Every write here mutates a live draft — confirm before each mutation.

## Reuse the existing journey and draft version

Attach the surface and content to the journey already created for this task. Keep its
container ID, draft version UID, and email node ID throughout this workflow.

- Do not create, clone, rebuild, or POST a new journey or journey version to set a surface.
- Use the exact draft version ID supplied by the user or returned by the preceding journey
  creation step. Never select a journey by name alone; ask if the target is ambiguous.
- Set the surface and apply the content against that same existing draft version.
- If the requested surface is already set, skip the surface mutation.
- If available tools cannot update the surface in place, stop and report the limitation.
  Ask the user to set the surface on that same draft in the AJO UI, then re-read it before
  continuing. Do not work around the limitation by creating another version.

## Verify the surface and provisioned message together

An unprovisioned email node may still show "Channel configuration not selected" even when
`surfaceId` is stored. Applying the template provisions the inline message when needed.
Verify both the saved surface and the message after binding; an empty UI field alone is
not a reason to recreate the journey or its version.

## Inputs to gather

- Journey (resource/version ID) and the sandbox.
- The email node ID (a `campaign`/`email` node) and its existing `actionUID`.
- The channel surface ID to set (a message-preset / branding-preset UUID).
- The content template ID and the email subject (subject is REQUIRED for HTML templates
  that store no subject).

## Step 1 — Read the journey and locate the email node

Read the journey (`ajo__get_journey` with `id` + `sn`). From `ui.nodes`, find the node with
`type: "campaign"` (channel `email`). Record: node id, `data.actionUID`, current
`surfaceId`/`surfaceType`, existing `messageId`, version UID, and the journey container id
(`journey` field). Confirm the target is a draft before proceeding. Note the current
node's `surfaceType` value — mirror it when you write (the UI uses `"surfaceId"`; older
saves used `"brandingPresetId"`).

## Step 2 — Verify the surface (read-only)

Call `ajo__get_channel_configuration` with the surface `configId` + `sn`. Confirm:
`status: "active"`, it is an email surface, and `sandbox.name` matches. Note its recipient
address expression (typically `personalEmail.address`). Stop if it is not active or is in a
different sandbox.

## Step 3 — Set the surface (mutation — confirm first)

If the node already has the requested surface, skip this mutation and continue to Step 4.

Inspect the available tool schema/documentation for a supported update of the existing
draft version's email node. Do not invent a PUT/PATCH endpoint or assume an operation
updates in place. If only journey/version creation is available, stop for the UI update
described above.

For a supported in-place update:
- Re-read the target for a fresh ETag where supported. Present the exact surface change,
  target IDs, and required confirmation string, then obtain explicit approval.
- Set `surfaceId` to the verified surface UUID and preserve the node's `surfaceType`
  convention. Use the verified recipient expression if the operation requires one.
- Preserve the container ID, version UID, node ID, `actionUID`, existing `messageId`, graph,
  audience, timing, channel overrides, tracking options, and decisioning references except
  for fields explicitly required and approved for the surface change.
- Never clear an existing `messageId` to force reprovisioning.
- Re-read the same version and verify the surface persisted and all target IDs are unchanged.
  If the operation unexpectedly returns a different version/container, stop and report it;
  do not continue binding to the new target.

## Step 4 — Apply the content template (mutation — confirm first)

After re-reading the existing draft and obtaining fresh approval for this mutation, call
`tadforge-bind-template-to-journey-action` with:
`sandbox`, `journeyVersionId` = the existing draft version UID, `nodeId` = the email node id
(NOT the actionUID), `templateId`, and `subject`. Success returns `bound: true,
verified: true` plus a provisioned `messageId`.

For email, this tool uses the surface stored on the node; its `surfaceId` override is for
push/SMS and is ignored for email. Do not use that override as an email-surface update.

If it returns `Inline campaign has no email surface`, re-read the same draft version and
check the sandbox, node ID, surface, and message state. Correct the surface in place using
Step 3 or request a UI correction on that draft. Obtain fresh approval before retrying.
Never create a journey or version as recovery. Reconcile timeouts or unknown outcomes
before retrying any mutation.

After binding, read back the same draft. Verify its container/version/node IDs, requested
surface, template content, and provisioned message. If a pre-existing message ID or its
decisioning scope changed unexpectedly, stop and report the discrepancy before further writes.

**"Latest template" caution:** if the user asks for the "latest" template and it resolves to
an off-brand or `NOT FOR SEND` / placeholder POC template, flag that to the user and confirm
before binding — it will become the message content.

## Step 5 — Report

Give the user the Experience Platform URL of the existing draft version verified above:
`https://experience.adobe.com/#/@<org-slug>/sname:<sandbox>/journey-optimizer/journeys/journey/<version_uid>`
Hyperlink the journey name once. Report whether reuse and surface/content verification
succeeded; only claim success after read-back. Keep the journey in Draft. Flag anything
still unresolved honestly: empty entry audience → journey still shows
`Missing identity namespace` / `no valid segment` and isn't publishable; any bracket
placeholders in the template are still POC values.

## Guardrails

- Confirm before every mutation; never retry a non-idempotent POST blindly — reconcile by
  re-reading first.
- Never create a journey or journey version in this skill, including during error recovery.
- Never invent surface, template, node, or version IDs. Verify the surface before setting it.
- Setting the surface + content does not bind offers/Decision Policy, does not resolve the
  entry audience, and is not proof, preview, or delivery.
