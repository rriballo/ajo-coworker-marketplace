---
name: ajo-audit-message-readiness
description: Read-only audit of an AJO saved email Content Template, Decision Policy marker/binding evidence, Journey campaign-action resolution, supported personalized campaign preview, and related AEP audience definition metadata. Use for message-source and preview readiness. Do not use for Decisioning resource audits, Journey-inline HTML retrieval, mutations, proofs, or activation.
---

# Audit AJO message readiness

Remain read-only. The sandbox is fixed to `aepenablementfy21`; stop on mismatch.

## Required inputs

- Exact Content Template ID or enough metadata to locate it.
- Optional root Journey ID, Action campaign version ID, and an explicitly approved non-production test identity for supported preview.
- Optional AEP audience name/system ID when audience-definition evidence is relevant.

## Workflow

1. Call `ajo_get_capabilities`.
2. Retrieve the exact template and audit it against `../../references/email-template-standard.md`: normalized subject/HTML/text/headers, ETag, source shape, placeholders, marker/generated policy block, links/assets, accessibility, responsive structure, and supplied footer content.
3. Resolve campaign/Journey evidence according to `../../references/campaign-scope-resolution.md`. Distinguish action nodes from embedded or recovered associations, report the recovery status and candidates, and never claim a positive `actionCount` with zero associations means no action.
4. If a supported preview is requested, require `simulationPreview.supported`, its non-null `previewCampaignId`, the inbound user token/permission, and one to ten approved test identities. Redact sensitive rendered values.
5. If audience context is requested, follow `../../references/audience-read-contract.md` and report definitions only.
6. Separate each evidence class: stored template source, campaign-rendered preview, audience definition, campaign scope, and external AJO UI state.

## Output

Return severity-ranked findings with evidence and remediation, followed by blind spots. Applying a template creates a copy; stored-template QA excludes later Journey-message edits. Neither template QA nor campaign preview proves Journey path execution, audience qualification, consent, proofs, activation, or delivery. Full Journey Simulation and copied-message validation remain AJO UI steps.
