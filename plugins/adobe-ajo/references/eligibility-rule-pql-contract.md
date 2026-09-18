# Eligibility rule PQL contract

Use this contract with `ajo-author-eligibility-rule`. The sandbox remains `aepenablementfy21`.

## Evidence first

- `ajo_decisioning_inspect_schema` inspects the Offer Item schema. Never use it as evidence for Profile fields.
- List Profile schemas with `ajo_aep_list_profile_schemas`, select one exact `data.results[].id`, then call `ajo_aep_get_profile_schema` in resolved form.
- Search fields with `ajo_aep_search_profile_schema_fields`. Copy exact `pqlPath`, type, array status, schema ID, version, and ETag into the rule plan.
- Stop on ambiguous schemas, missing fields, array fields, incompatible types, or schema changes.

## Eligibility modes

Choose exactly one:

1. `eligibilityAst`: preferred for supported profile comparisons, string-set comparisons, exact audience membership, and AND/OR groups.
2. `audienceEligibility`: preferred when the complete condition is membership in one to thirty exact AEP audiences.
3. `expression`: advanced raw PQL for unsupported constructs after every field and assumption is externally confirmed.

Never submit multiple modes. An unrestricted item needs no rule.

## Structured subset

`eligibility-ast/v1` supports:

- `comparison`: `eq`, `ne`, `gt`, `gte`, `lt`, or `lte` with a string, finite number, or boolean literal.
- `stringSet`: `equals` or `notEquals` with one or more strings and explicit case sensitivity. This generates `stringCompare(...)`.
- `audience`: one exact AEP system ID. This generates `inSegment(...)`.
- `group`: `and` or `or` with two to twenty children.

The compiler limits depth and node count, validates scalar field types against the selected resolved Profile schema, exact-verifies audience IDs, and generates deterministic PQL. Arrays, dates, event windows, arbitrary functions, null checks, field-to-field comparisons, and negation are unsupported in v1; use raw mode only after explicit review.

## Raw mode

Raw diagnostics check blank input, UTF-8 size, control characters, quoted strings, escapes, balanced parentheses, and parenthesis depth. They are not a complete PQL parser and do not prove:

- Full syntax or function validity.
- Boolean semantics or type correctness.
- Tenant field existence.
- Audience existence unless independently exact-read.
- Qualification for any profile.

Never convert a failed local check into different PQL silently. Stop and show the diagnostic.

## Evidence taxonomy

Report these independently:

- `tenantSchemaChecked`: structured fields resolved in one exact Profile schema.
- `audiencesResolved`: every referenced AEP system ID exact-read successfully.
- `conditionPersistedExactly`: Adobe returned the exact generated or supplied expression.
- `visualEditorCompatible`: Adobe supplied compatible `segmentModel` evidence; absence is not PQL rejection.
- `eligibilityRuleAttached`: an exact item or strategy read confirms the rule reference.
- `profileEligibilitySimulated`: always false in this MCP.

Do not collapse them into a generic "valid" claim.

## Mutation boundary

Run `ajo_decisioning_validate_eligibility` before proposing a write. Compare existing rules semantically. Obtain exact approval for create or update, then call `ajo_decisioning_verify_rule`. Rule creation does not attach it; item or strategy attachment is a separate payload, approval, ETag when applicable, and exact-read verification.
