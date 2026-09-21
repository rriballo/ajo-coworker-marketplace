---
name: nordvell-brand-guidelines
description: Apply the standard NORDVELL text header, reusable email hierarchy, footer, voice, WPP Enterprise Solution POC attribution, and explicit bracket-placeholder convention before authoring or auditing NORDVELL email, fragment, or Decisioning content. Use for every NORDVELL content request. This skill performs no AJO mutation.
---

# Apply NORDVELL brand guidelines

Follow `../../references/nordvell-brand-standard.md`, `../../references/email-template-standard.md`, and `../../references/write-safety-and-recovery.md`. This skill performs no write and grants no mutation approval.

## Required inputs

- Content purpose, channel, product, audience-independent campaign facts, and supplied copy.
- Optional product or campaign assets with reachable URLs and provenance. The NORDVELL header always uses a text wordmark and requires no logo asset; when a body asset is unavailable, retain its module with a descriptive bracket placeholder.
- CTA intent and destination when the brief requires a CTA. Coworker writes the label where possible; use bracket placeholders for unavailable CTA content and do not create a fake clickable link.
- Optional supplied mandatory legal text. Preference, unsubscribe, and privacy destinations use the fixed `.example` POC URLs from the NORDVELL standard.
- For requested Decisioning content: each candidate role, eligibility intent, approved profile fields, and any claims or assets the candidate needs.

Never invent assets, destinations, products, prices, sizes, stock, store proximity, pickup, delivery, guarantees, legal terms, urgency, environmental claims, or personalization rationale. Use the standard uppercase square-bracket placeholders for unavailable POC inputs and list each one. Treat a missing real value as a blocker only when an Adobe operation requires it.

## Workflow

1. Classify the requested artifact and confirm it is NORDVELL POC content.
2. Apply the standard shell without hardcoding a campaign: fixed text header, reusable body hierarchy, and fixed footer/WPP POC attribution. Write subject, preheader, headings, narrative, support copy, CTA, and Decisioning presentation from the current brief.
3. Inventory every supplied fact and its provenance. Apply the fixed POC footer URLs without requesting replacements. Mark other unverified claims and missing destinations as unresolved rather than rewriting around them.
4. Review voice, hierarchy, imagery, accessibility, CTA restraint, and customer-facing terminology. Preserve requested modules with descriptive brackets when inputs are unavailable; never leave an unlabeled empty box, fake URL, or ambiguous token.
5. For Decisioning candidates, require mutually exclusive intent where appropriate and ensure the fallback remains true without profile, store, or inventory facts. Never claim that a profile qualifies or an item will be selected.
6. Return the content contract: standard-shell compliance, finished proposed copy, assets, links, candidate variants, every bracket placeholder, forbidden claims, unresolved blockers, and approvals still required.
7. Hand the approved contract to `ajo-author-email-template`, `ajo-manage-expression-fragments`, `ajo-manage-decision-item`, or `ajo-build-decisioning-experience` as appropriate. Obtain fresh approval in the receiving skill before every mutation.

## Boundaries

- The WPP attribution is fixed POC disclosure and visually secondary; it does not make WPP the NORDVELL manufacturer, retailer, guarantor, or fulfilment provider.
- Do not move WPP attribution into the header, hero, CTA, Decisioning candidate, or product claim.
- Do not alter the standard header or footer copy. Body copy is brief-specific and must not be inherited from another campaign.
- The complete-email shell applies to email templates, not standalone expression fragments or Decisioning-item content. Those artifacts inherit voice and content rules without duplicating a header or footer.
- The standard `.example` footer URLs are approved only for this POC and must never be described as operational or carried into production.
- Bracket placeholders may represent missing POC content but never Adobe IDs, ETags, audience/schema IDs, placement names, fragment keys, Decision Policy IDs, or executable personalization syntax.
- Brand review is not legal approval, template source QA, personalized preview, qualification, proof, activation, delivery, or performance evidence.
