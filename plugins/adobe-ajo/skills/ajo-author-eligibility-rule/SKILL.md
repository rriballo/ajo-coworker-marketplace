---
name: ajo-author-eligibility-rule
description: Plan, validate, create, update, or verify Adobe Journey Optimizer Experience Decisioning eligibility rules using exact Profile-schema evidence, structured eligibility AST, exact AEP audience IDs, or advanced raw PQL. Use for rule authoring and rule QA. Do not use for ranking formulas, audience creation, item attachment, cleanup, or profile simulation.
---

# Author an AJO eligibility rule

Follow `../../references/eligibility-rule-pql-contract.md`, `../../references/audience-read-contract.md`, and `../../references/write-safety-and-recovery.md`.

The sandbox is always `aepenablementfy21`. Stop on any mismatch.

## Required inputs

- Business eligibility statement and desired rule name.
- Exact eligibility mode: existing rule reuse, Profile attributes, audience membership, or advanced raw PQL.
- Explicit boolean grouping and case sensitivity.
- For update, exact rule ID and a fresh ETag from the same rule.

## Read-only authoring

1. Call `ajo_get_capabilities` and inventory existing rules with complete pagination.
2. If the condition is audience-only, list and exact-get every AEP system ID. Never use audience names, external `audienceId`, or copied segmentation PQL.
3. If the condition uses Profile attributes, list Profile schemas, select and exact-get one resolved XDM Individual Profile schema, and search every required field. Never use the Offer Item schema as Profile evidence.
4. Translate supported conditions into `eligibility-ast/v1`. Preserve the user's AND/OR meaning explicitly; never guess precedence, field paths, types, dates, arrays, or case sensitivity.
5. Use `ajo_decisioning_validate_eligibility`. For structured mode require `tenantSchemaChecked: true`; for audience references require `audiencesResolved: true`.
6. Use raw PQL only for constructs outside the structured subset. Report its diagnostics as lexical guardrails, not complete syntax, semantic, schema, or qualification validation.
7. Compare reusable rules by exact condition and evidence, not name alone.
8. Present the business interpretation, selected schema and ETag, each `pqlPath` and type, exact audience IDs, deterministic or raw PQL, diagnostics, unresolved assumptions, and limitations.

## Mutation

Plan approval is not write approval.

1. Present the exact create or update payload and obtain fresh explicit approval.
2. Call `ajo_decisioning_create_rule` or `ajo_decisioning_update_rule` once. On an unknown or mismatched outcome, stop and reconcile; never retry blindly.
3. Call `ajo_decisioning_verify_rule` using the exact expected source. Require matching rule ID, `exdRule`, PQL envelope, and `conditionPersistedExactly: true`.
4. Report `visualEditorCompatible` separately. Missing visual-model evidence is not proof that Adobe rejected the PQL.
5. Record the returned rule ID and ETag.

## Handoff

Rule creation never attaches it. Return the verified rule ID to `ajo-build-decisioning-experience` or another explicit workflow. Item or strategy attachment requires a separate payload and approval, followed by an exact read requiring `eligibilityRuleAttached: true`.

Never claim a profile qualifies. This MCP does not simulate rule evaluation.
