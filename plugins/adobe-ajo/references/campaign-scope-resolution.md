# Campaign scope resolution

Decision Policy writes target one exact DRAFT Action campaign email message. A Journey is ownership and discovery context, not policy scope.

## Known campaign version

1. Call `ajo_campaign_resolve_scope` with the Action `campaignVersionId`.
2. Optionally include the root `journeyId` for ownership verification.
3. Require a DRAFT version and preserve the complete returned `data.scope` unchanged for policy creation and placement binding.
4. If package or message selection is ambiguous, present candidates and request exact IDs.

## Journey-only discovery

1. Call `ajo_journey_resolve_campaigns` with the root Journey ID.
2. For a newly created Journey, call `ajo_journey_validate_structure` first. Require connected public topology and complete campaign-backed email provisioning; inspect limitations because this does not validate private canvas state.
3. `actionCount` counts campaign action nodes. `associationCount` requires an embedded or recovered campaign version. `provisionedActionCount` requires package/message identities. A bare embedded campaign ID is an incomplete placeholder.
4. When Adobe omits usable metadata, the resolver automatically queries Campaign Service with exact `metadata.sourceVersionId`. It correlates exact `metadata.sourceActionId` to action UID or node ID; it uses singleton fallback only when one compatible unresolved action and one candidate remain.
5. Inspect `recovery`, `actions`, `provisioningState`, `scopeUsable`, `campaigns`, and `selectionRequired`. Action presence does not prove provisioning.
6. If recovery is ambiguous, narrow with an exact `nodeId`, `actionUid`, `campaignId`, or select an exact returned campaign version. Never choose by name, channel, order, or draft status alone.
7. Ask the user for the Action `campaignVersionId` only after recovery has no match or remains unresolved. If supplied, resolve it directly.
8. Pass only an action with `scopeUsable=true` and its selected `campaignVersionId` to `ajo_campaign_resolve_scope`.
9. Pass the returned `data.scope` unchanged to Decision Policy create and bind tools. For Journey-inline email actions, `scope.campaignId` can be a composite string; it is valid scope metadata but must not be used as a root campaign lookup or Simulation ID.

Never substitute a Journey ID, Journey version ID, node ID, action UID, package ID, message ID, or template ID for a campaign ID or campaign version ID. `journeyVersionId` is metadata only.

## Newly provisioned actions

- A creation receipt is incomplete until it contains Journey root/version, action node/action UID, DRAFT campaign version, package, and message IDs.
- Exact-read the Journey with `get_journey`, then call `ajo_journey_resolve_campaigns` and `ajo_campaign_resolve_scope` before surface, content, or Decision Policy work.
- Require all returned identities to match the provisioning receipt. Never manufacture a campaign association from the Journey graph.
- If any identity is missing or inconsistent, stop; do not create another Journey/version or guess cross-service IDs.

## Surface and content preservation

- Surface assignment and template/content binding do not authorize Decision Policy writes.
- Resolve the exact DRAFT campaign version and email package before either mutation. Record the current package surface, version `modifiedAt`, message IDs, and policy references.
- Use `ajo_campaign_list_surfaces` and `ajo_campaign_get_surface` before `ajo_campaign_set_package_surface`. The surface mutation targets one exact package only.
- Re-resolve the same scope after mutation. Any changed campaign/version/package/message identity or Decision Policy reference is a stop condition.
- If policy create/bind is later requested, pass the complete unchanged `data.scope` to the policy tool under a new approval.

## Preview

- Use `previewCampaignId` only from `ajo_journey_resolve_campaigns` and only when `simulationPreview.supported` is true.
- `ajo_campaign_preview_content` requires one to ten approved test identities containing `{userId, namespace}`, an inbound IMS user token, and Manage Simulate Content permission. It has no S2S fallback.
- Do not use arbitrary production profiles. Minimize or redact personalized values in reports.
- Preview renders supported Action/API-triggered campaign content. It does not simulate Journey paths, waits, events, consent, eligibility, proofs, activation, or delivery. Orchestrated campaigns and standalone templates are unsupported.
