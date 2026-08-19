---
name: ajo-author-email-template
description: Create, update, and quality-check a complete Adobe Journey Optimizer email Content Template, including a server-generated Decision Policy offer block or a pending marker. Use for email authoring and template-source QA. Do not use for expression fragments, Journey-inline HTML, campaign activation, proofs, or legal approval.
---

# Author an AJO email Content Template

Follow `../../references/write-safety-and-recovery.md`, `../../references/email-template-standard.md`, and `../../references/operation-manifest-and-output.md`. The sandbox is fixed to `aepenablementfy21`.

## Required inputs

- Create: template name, supplied/approved subject and business copy, assets, links, footer facts, and full HTML requirements.
- Update: exact template ID and requested changes.
- Offer block: either no policy yet and one literal `<!-- offer -->` marker, or all of real `decisionPolicyId`, exact bound `placementName`, and fragment `referenceKey`.

Stop and request missing business, legal, or asset facts. Never fabricate them.

## Workflow

1. Call `ajo_get_capabilities`, verify Content writes, and state the fixed sandbox.
2. List templates only when the exact ID is unknown. Compare same-name candidates by ID, type, content, and lifecycle; never reuse by name alone.
3. Build a complete responsive document according to the email standard. Report every user-authorized placeholder.
4. Preferred policy path: accept a real UUID returned by `ajo_decisioning_create_decision_policy` or supplied from AJO, verify the exact placement was bound, and pass all three offer-block values to the template tool. Never write the loop manually.
5. Template-first contingency: use only `<!-- offer -->`; record the template as pending policy binding. Do not insert fake IDs or unsupported `{{#if}}`/`{{else}}` expressions.
6. For create, show the exact name, complete HTML/content facts, offer-block mode, risks, and confirmation phrase; obtain approval, create, record the template ID, and re-read it.
7. For update or policy backfill, call `ajo_content_get_template` immediately before approval, show the old/new normalized content and fresh ETag, obtain approval, update, and re-read.
8. QA `data.qa.subject`, HTML, text, headers, source shape, ETag, unresolved placeholders, marker/policy block, links, assets, accessibility, responsive structure, and supplied footer elements.
9. Apply the reviewed template in AJO UI only after QA. State that application creates a copy and later Journey-side edits are not represented by the stored template.

Return a source-QA report and operation receipt. Technical checks are not legal approval, personalized preview, Journey Simulation, proof, or delivery evidence.
