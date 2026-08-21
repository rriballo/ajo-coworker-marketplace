# Campaign scope resolution

Decision Policy writes target one exact DRAFT Action campaign email message. A Journey is ownership and discovery context, not policy scope.

## Known campaign version

1. Call `ajo_campaign_resolve_scope` with the Action `campaignVersionId`.
2. Optionally include the root `journeyId` for ownership verification.
3. Require a DRAFT version and preserve the complete returned `data.scope` unchanged for policy creation and placement binding.
4. If package or message selection is ambiguous, present candidates and request exact IDs.

## Journey-only discovery

1. Call `ajo_journey_resolve_campaigns` with the root Journey ID.
2. `actionCount` counts campaign action nodes. `associationCount` counts embedded plus deterministically recovered associations.
3. When Adobe omits embedded metadata, the resolver automatically queries Campaign Service with exact `metadata.sourceVersionId`. It correlates exact `metadata.sourceActionId` to action UID or node ID; it uses singleton fallback only when one compatible unresolved action and one candidate remain.
4. Inspect `recovery`, `actions`, `campaigns`, and `selectionRequired`. A positive `actionCount` with zero associations means no exact recovery was possible, not that no action exists.
5. If recovery is ambiguous, narrow with an exact `nodeId`, `actionUid`, `campaignId`, or select an exact returned campaign version. Never choose by name, channel, order, or draft status alone.
6. Ask the user for the Action `campaignVersionId` only after recovery has no match or remains unresolved. If supplied, resolve it directly.
7. Pass the selected `campaignVersionId` to `ajo_campaign_resolve_scope`.
8. Pass the returned `data.scope` unchanged to Decision Policy create and bind tools. For Journey-inline email actions, `scope.campaignId` can be a composite string; it is valid scope metadata but must not be used as a root campaign lookup or Simulation ID.

Never substitute a Journey ID, Journey version ID, node ID, action UID, package ID, message ID, or template ID for a campaign ID or campaign version ID. `journeyVersionId` is metadata only.

## Preview

- Use `previewCampaignId` only from `ajo_journey_resolve_campaigns` and only when `simulationPreview.supported` is true.
- `ajo_campaign_preview_content` requires one to ten approved test identities containing `{userId, namespace}`, an inbound IMS user token, and Manage Simulate Content permission. It has no S2S fallback.
- Do not use arbitrary production profiles. Minimize or redact personalized values in reports.
- Preview renders supported Action/API-triggered campaign content. It does not simulate Journey paths, waits, events, consent, eligibility, proofs, activation, or delivery. Orchestrated campaigns and standalone templates are unsupported.
