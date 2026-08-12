---
name: ajo-manage-content
description: Create, update, publish, inspect, and attach Adobe Journey Optimizer expression fragments. Use for reusable TEXT, HTML, or JSON content and hero, body, or footer item references.
---

# Manage AJO expression fragments

Content writes require `AJO_CONTENT_WRITES_ENABLED=true`. Decisioning attachment writes separately require `AJO_DECISIONING_WRITES_ENABLED=true`. Obtain explicit human approval before each operation.

1. Search existing fragments and reuse suitable published content when possible.
2. Create expression fragments with `type=expression`, channel `shared`, and subtype `TEXT`, `HTML`, or `JSON` as appropriate.
3. Re-read a fragment before updating or publishing and copy its fresh `metadata.etag`.
4. Update expression content with the full-update tool. Present the old and proposed expression before requesting approval.
5. Publish only after the user reviews the exact draft and approves publication. Publication is asynchronous and is not safe to retry blindly.
6. Poll publication status until `complete` or `error`; do not treat request acceptance as completed publication.
7. Retrieve the live fragment to verify what is actually published.
8. Before attachment, ensure the target Decisioning item is in draft. If approved, separately request approval to return it to draft.
9. Attach the published expression fragment under a stable semantic key such as `hero`, `body`, or `footer`, using a fresh item ETag.
10. Validate item readiness and request separate approval before approving the item again.

The public Content API exposes no fragment delete operation. Never claim that temporary fragments can be automatically cleaned up. AJO visual fragments are not valid Decisioning item references; use expression fragments.

## Email content templates

AJO personalization is not Handlebars. Never put `{{ }}` or `{% %}` syntax in template HTML; the save engine rejects unsupported constructs and placeholder identifiers such as `<POLICY_ID>` even inside HTML comments.

- When the Decision Policy UUID is unknown, save the template with an `<!-- offer -->` marker comment where the offer block should render, and report the template as pending a policy ID.
- When the user created the Decision Policy in the AJO UI and supplies its real UUID, pass `decisionPolicyId`, the exact bound `placementName`, and `referenceKey` to the create or update template tool; the server injects the valid placement-scoped `{{#each decisionPolicy.<id>.placement.<placementName>.items as |item|}}` block. Never write that block by hand.
- When the policy was created through this MCP, use the policy UUID and placement name returned by the policy workflow. The template tool does not resolve campaign scope; the policy must already be bound to the placement in the target campaign message.

## Email structure and blocks

Build complete render-ready email documents, not fragments. A full template consists of a preheader, header, hero, a body composed of two to four content blocks, and a footer. Write concrete brand-consistent copy (real headlines, CTAs, offer names, and dates), never lorem ipsum, and keep sentences short.

1. Preheader: one hidden line of text under the subject line that summarizes the value (for example "Member-only savings inside"). Use a `display:none` div with a fallback character for threaded clients.
2. Header: brand logo (or styled brand wordmark) with `alt` text, a light background band, and optionally a slim top row for a date or product category.
3. Hero: full-width image or gradient band with a headline (28–36px), one or two lines of supporting copy (16px), and a single primary CTA button. Keep the hero scannable at a glance.
4. Body blocks: pick two to four of the following, varied, not repeated identical layouts:
   - Card row (2–3 cards): image, title, short description, and text CTA link; stack vertically on mobile.
   - Split section: image left / copy right (or reversed) for feature storytelling, with its own CTA.
   - Voucher section: a dashed-border voucher card showing the code, value, validity window, and short terms; center it and give it enough whitespace.
   - CTA band: one full-width centered button with a clear action, repeated once deeper in the email.
   - Offer-decision block: the `<!-- offer -->` marker (or `decisionPolicyId`) placed where selected offers render, for example after the hero or as the final block before the footer.
   - Testimonial or social-proof strip with a quote and author when it strengthens the story.
5. Footer: unsubscribe link, mailing address, social icons (text links with `alt`), privacy link, and copyright. Unsubscribe must be present and visible (CAN-SPAM).

Technical rules for every template:

- Full document: `<!DOCTYPE html>`, `<html>`, `<head>` with `<meta name="viewport">`, `<meta charset="utf-8">`, `<title>`, and a `@media` query that stacks multi-column rows and grows tap targets to at least 44px on small screens.
- Layout: table-based or hybrid (tables + divs); container no wider than 600px centered with `align="center"`; `role="presentation"` on layout tables.
- Styling: inline `style=""` on every element; fallback `bgcolor` behind images; web-safe font stack (Arial, Helvetica, sans-serif) with optional Google Font link; body text 14–16px and buttons with 12–16px padding and generous line-height.
- Media: every image needs `alt`, explicit `width`/`height`, and `src` that is a real, reachable asset URL; no lazy-loading or JavaScript.
- Email-client safety: include `mso` conditional comments and `border="0"` margins for Outlook; prefer solid colors that keep contrast when HTML images are disabled.
