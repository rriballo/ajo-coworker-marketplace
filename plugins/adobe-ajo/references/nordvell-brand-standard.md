# NORDVELL brand and email shell standard

## Scope

This standard governs NORDVELL content created for the WPP Enterprise Solution proof of concept. NORDVELL remains the customer-facing brand. WPP appears only in the fixed POC attribution defined below; never present WPP as the product manufacturer, retailer, guarantor, or fulfilment provider.

Use the shell in this document for every NORDVELL POC email. The shell fixes brand presentation and document hierarchy, not campaign content. Coworker must turn the current brief into finished copy where facts are available and use explicit square-bracket POC placeholders where they are not. Do not inherit campaign-specific defaults.

## Brand character

- Write with calm confidence: concise, practical, warm, and useful rather than promotional or urgent.
- Prefer short sentences, concrete product guidance, and restrained calls to action.
- Use a premium Nordic outdoor visual language: generous space, strong product photography, quiet natural colors, dark text, and one clear action.
- Do not use exaggerated scarcity, unsupported sustainability language, excessive punctuation, all-caps body copy, or unverified performance, stock, delivery, price, pickup, guarantee, or weather claims.
- Internal terms such as audience, profile, rule, offer, strategy, Decision Policy, MCP, AJO, and qualification must never appear in customer-facing copy.

## Standard shell

### Header

Always render a simple text header first:

1. Text wordmark: `NORDVELL`.
2. Strapline: `Built for Nordic weather.`

Never request, insert, or reserve space for a logo image. Keep the text wordmark primary. Do not add navigation, promotional badges, WPP branding, or campaign copy to the header.

### Body

Always preserve this document hierarchy:

1. Hidden preheader written from the current brief.
2. Header with the standard text wordmark and strapline.
3. Main content with one clear campaign headline and concise narrative written from the current brief.
4. Optional hero media when a real suitable asset is available.
5. Optional Decisioning section when the brief requests personalized or selected content.
6. Optional supporting information that is relevant and substantiated for the current brief.
7. One primary CTA when the requested action and destination are known.
8. Standard footer.

Coworker chooses the most suitable body composition within this hierarchy. It may use a hero, split section, cards, product detail, reassurance band, practical guidance, or Decisioning block when supported by the brief. Do not force Aurora Parka copy, `While you wait`, free returns, guarantees, fit guidance, or any other campaign-specific statement into unrelated emails.

Preserve the requested structure even when inputs are incomplete. Author subject, preheader, headings, body, CTA labels, and alt text naturally from the brief where possible. For anything unavailable, use a concise uppercase square-bracket placeholder that states what is missing, such as `[HERO IMAGE]`, `[PRODUCT BENEFIT]`, `[CTA DESTINATION]`, `[STORE NAME]`, or `[LEGAL ADDRESS]`. A missing image remains a styled visual placeholder; a CTA with a missing destination remains visibly labeled but must not use `#` or pretend to be clickable.

Decisioning content must remain inside a dedicated visual block whose heading fits the current message. Profile attributes, store details, proximity, pickup, inventory, shipping, product availability, images, and URLs may be used only when backed by approved data or content. Use bracket placeholders to show missing POC content or data requirements, but do not present them as actual personalized values or qualification evidence. The unrestricted fallback must remain truthful without profile or inventory data. Never expose internal IDs, field paths, or template syntax to the recipient.

## POC placeholder convention

- Square-bracket placeholders are explicitly authorized for NORDVELL POC content whenever information, copy, media, links, personalization values, or legal details are unavailable.
- Use short descriptive uppercase labels: `[SUBJECT LINE]`, `[PREHEADER]`, `[HERO IMAGE]`, `[IMAGE ALT TEXT]`, `[PRODUCT NAME]`, `[PREFERRED SIZE]`, `[STORE NAME]`, `[CTA LABEL]`, `[CTA DESTINATION]`, and `[LEGAL ADDRESS]`.
- Keep the surrounding design complete so the placeholder clearly shows where and how the final content belongs.
- Do not invent a value alongside a placeholder. Do not describe a placeholder as approved, personalized, available, or operational.
- Inventory every placeholder in the content contract and source-QA report. Placeholders are accepted for this POC and are not automatic save blockers unless a requested Adobe operation requires the real value.
- Never substitute bracket placeholders for technical Adobe identifiers or executable syntax. `decisionPolicyId`, `placementName`, `referenceKey`, resource IDs, ETags, audience IDs, and schema IDs must be real verified values. Before a Decision Policy exists, use only the supported invisible `<!-- offer -->` marker.

### Footer

Always finish with this exact visible copy and order:

```text
NORDVELL
Built for Nordic weather.

This NORDVELL experience is presented as part of a WPP Enterprise Solution proof of concept.

Manage preferences | Unsubscribe | Privacy
```

Use these fixed POC destinations:

- `Manage preferences` -> `https://nordvell.example/preferences`
- `Unsubscribe` -> `https://nordvell.example/unsubscribe`
- `Privacy` -> `https://nordvell.example/privacy`

These reserved `.example` destinations are intentional POC values, not unresolved placeholders, and may be saved in NORDVELL POC templates without additional link approval. Never represent them as operational links or reuse them in production. Do not replace them with `#` or silently change them. Add supplied mandatory legal text beneath the links; when it is unavailable, use a descriptive bracket placeholder such as `[LEGAL ENTITY AND POSTAL ADDRESS]` without changing the fixed copy above. Footer presence and link checks are technical evidence, not legal approval.

The WPP sentence is deliberate POC attribution. It must be visually secondary to NORDVELL, must not appear in the header or main campaign narrative, and must be removed or replaced through an approved brand-contract change before production use.

## Layout and accessibility

- Follow `email-template-standard.md` for complete-document HTML, email compatibility, responsive behavior, accessibility, and source QA.
- Use a centered container no wider than 600px, meaningful image alt text, adequate contrast, readable mobile typography, and touch-safe links.
- Preserve hierarchy across image-blocked, dark-mode, and narrow-screen conditions.
- Do not make the message image-only. Fixed copy must remain live text.

## Evidence and approval

Return a NORDVELL content contract before authoring: standard-shell compliance, campaign facts used, authored content, assets and provenance, personalization evidence, Decisioning variants, CTA destinations, every bracket placeholder, use of the standard POC footer, unresolved blockers, and required approvals. Brand compliance does not prove legal approval, profile qualification, inventory, preview, proof, activation, delivery, or performance.
