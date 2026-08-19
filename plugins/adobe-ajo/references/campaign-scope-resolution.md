# Campaign scope resolution

Decision Policy writes target one exact DRAFT Action campaign email message. A Journey is ownership and discovery context, not policy scope.

## Known campaign version

1. Call `ajo_campaign_resolve_scope` with the Action `campaignVersionId`.
2. Optionally include the root `journeyId` for ownership verification.
3. Require a DRAFT version and preserve the complete returned `data.scope` unchanged for policy creation and placement binding.
4. If package or message selection is ambiguous, present candidates and request exact IDs.

## Journey-only discovery

1. Call `ajo_journey_resolve_campaigns` with the root Journey ID.
2. `actionCount` counts campaign action nodes. `associationCount` counts only nodes for which Adobe embedded campaign metadata.
3. A positive `actionCount` with zero associations means metadata is unavailable, not that no action exists. Ask for the Action `campaignVersionId` and resolve it directly.
4. If multiple associations exist, require an exact candidate selection. Never choose silently.
5. Pass the selected `campaignVersionId` to `ajo_campaign_resolve_scope`.

Never substitute a Journey ID, Journey version ID, node ID, action UID, package ID, message ID, or template ID for a campaign ID or campaign version ID. `journeyVersionId` is metadata only.

## Preview

- Use `previewCampaignId` only from `ajo_journey_resolve_campaigns` and only when `simulationPreview.supported` is true.
- `ajo_campaign_preview_content` requires one to ten approved test identities containing `{userId, namespace}`, an inbound IMS user token, and Manage Simulate Content permission. It has no S2S fallback.
- Do not use arbitrary production profiles. Minimize or redact personalized values in reports.
- Preview renders supported Action/API-triggered campaign content. It does not simulate Journey paths, waits, events, consent, eligibility, proofs, activation, or delivery. Orchestrated campaigns and standalone templates are unsupported.
