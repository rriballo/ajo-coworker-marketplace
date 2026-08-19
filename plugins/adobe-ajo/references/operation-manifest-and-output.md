# Operation manifest and output

Create a manifest before the first mutation and update it after every operation.

## Manifest fields

- Fixed sandbox: `aepenablementfy21`.
- Business objective and supplied facts.
- Target campaign/Journey identifiers and exact DRAFT message scope when applicable.
- Proposed resources with deterministic names, operation type, normalized configuration, dependencies, lifecycle target, and reuse decision.
- Decision Policy inputs: output count, all selection strategy IDs, optional manual item IDs, all approved fallback item IDs, placement name, and exact scope.
- Required approvals, external AJO steps, and unresolved placeholders.

## Operation states

Use `planned`, `approved`, `completed`, `blocked`, `outcome-unknown`, and `external-action-required`. Never represent request acceptance as completion for asynchronous work.

## Final receipt

Return a concise ledger containing each operation, environment, resource type/name/ID, before and after lifecycle, before and after ETag when applicable, references, async status, verification evidence, warnings, unresolved outcomes, and next action. Include resources reused without mutation and explain the semantic comparison.
