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

## Why the two steps must be chained

There is no API to edit the exact journey version the UI has open. Each write to the
journey authoring backend creates a NEW journey **version** on the same container. The
content-template tool applies to a specific version UID. So:

- Set the surface and apply the content **against the same new version UID**, back to back,
  in one pass. Do not set the surface, stop, and later apply content to a stale version —
  the surface will not be on whatever version you target next.
- Applying content to a version whose email node has no surface fails with
  `Inline campaign has no email surface`, and the rendered email shows
  **"Channel configuration not selected"**.
- After finishing, hand the user the URL of the FINAL version you wrote (not the original),
  and tell them to continue editing from that version so the UI doesn't diverge again.

## Critical: the surface is not UI-visible until content is applied

Setting `surfaceId` on the node via the API stores the value but does NOT populate the
"Email channel configuration" field in the AJO Actions panel, and does NOT make the surface
selectable/visible in the UI. AJO only renders the surface once an inline **message is
provisioned** on the node — which happens in Step 4 (applying the content template). So a
surface-only write always looks "empty" in the UI even though the data persisted. Never
promise a UI-visible surface without completing Step 4. This is why this skill always does
both steps; there is no useful "surface-only" outcome.

## Inputs to gather

- Journey (resource/version ID) and the sandbox.
- The email node ID (a `campaign`/`email` node) and its existing `actionUID`.
- The channel surface ID to set (a message-preset / branding-preset UUID).
- The content template ID and the email subject (subject is REQUIRED for HTML templates
  that store no subject).

## Step 1 — Read the journey and locate the email node

Read the journey (`ajo__get_journey` with `id` + `sn`). From `ui.nodes`, find the node with
`type: "campaign"` (channel `email`). Record: node id, `data.actionUID`, current
`surfaceId`/`surfaceType`, and the journey container id (`journey` field). Note the current
node's `surfaceType` value — mirror it when you write (the UI uses `"surfaceId"`; older
saves used `"brandingPresetId"`).

## Step 2 — Verify the surface (read-only)

Call `ajo__get_channel_configuration` with the surface `configId` + `sn`. Confirm:
`status: "active"`, it is an email surface, and `sandbox.name` matches. Note its recipient
address expression (typically `personalEmail.address`). Stop if it is not active or is in a
different sandbox.

## Step 3 — Set the surface (mutation — confirm first)

Present the exact change and get approval. Then POST a new journey version via
`api_request(service="journey_authoring", path="authoring/journeyVersions", method="POST")`.
Build the body by reproducing the journey's exact node/edge graph (faithful copy — same node
IDs, edges, timezone, batchDefinition, keyNamespace, etc.) with the email node's `data`
carrying:

```
"surfaceId": "<surface-uuid>",
"surfaceType": "surfaceId",           # match the node's current surfaceType
"channel": "email",
"channelOverrides": [
  {"name":"address","expression":{"plainText":"personalEmail.address","parameters":[]},
   "channel":"email","isDefaultValue": true}
],
"trackingOptions": {"clickTrackingEnabled": true}
```

Keep the existing `actionUID`; leave `messageId` empty. Include `"journey": "<container-id>"`
in the body and do NOT include a top-level `uid`. From the response, capture the new
**version UID** (`createdElement.uid` or `result.uid`) and confirm `surfaceId` persisted on
the email node.

Node-shape reference (the backend rejects generic types): entry read-audience node is
`type:"segmentTrigger"` (name/icon `segmentTrigger`); email node is `type:"campaign"`
(name/icon `campaign`, `data.nodeType:"campaign"`). Do NOT use `readAudience`/`message` —
they fail `ERR_MODEL_4` and disconnect the graph.

## Step 4 — Apply the content template (mutation — confirm first)

Immediately, against the version UID from Step 3, call
`tadforge-bind-template-to-journey-action` with:
`sandbox`, `journeyVersionId` = the new version UID, `nodeId` = the email node id
(NOT the actionUID), `templateId`, and `subject`. Success returns `bound: true,
verified: true` plus a provisioned `messageId`.

If it returns `Inline campaign has no email surface` — even when a prior version already
shows `surfaceId` on the node — the binding tool does not see a bound surface on the version
you targeted. Recover by doing Step 3 and Step 4 back-to-back on a FRESH version: re-read the
current draft, POST a new version with the surface (Step 3), capture its new version UID, then
immediately call this tool against that new UID. Do not bind against an older version. This
recovery is expected, not a failure — a surface set on an earlier version often needs to be
re-applied on the version you actually bind.

**"Latest template" caution:** if the user asks for the "latest" template and it resolves to
an off-brand or `NOT FOR SEND` / placeholder POC template, flag that to the user and confirm
before binding — it will become the message content.

## Step 5 — Report

Give the user the Experience Platform URL of the FINAL version (Step 3's version UID):
`https://experience.adobe.com/#/@<org-slug>/sname:<sandbox>/journey-optimizer/journeys/journey/<version_uid>`
Hyperlink the journey name once. Tell them to continue from THIS version. Flag anything
still unresolved honestly: empty entry audience → journey still shows
`Missing identity namespace` / `no valid segment` and isn't publishable; any bracket
placeholders in the template are still POC values.

## Guardrails

- Confirm before every mutation; never retry a non-idempotent POST blindly — reconcile by
  re-reading first.
- Never invent surface, template, node, or version IDs. Verify the surface before setting it.
- Setting the surface + content does not bind offers/Decision Policy, does not resolve the
  entry audience, and is not proof, preview, or delivery.
