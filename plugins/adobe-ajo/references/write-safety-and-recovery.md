# Write safety and recovery

## Fixed environment

- The only permitted sandbox is `aepenablementfy21`.
- State this sandbox before a write. If configuration, tool output, or user input indicates another sandbox, stop. Do not offer another sandbox as a choice.
- Call `ajo_get_capabilities` and verify the applicable Content or Decisioning write gate before planning a mutation.

## Approval contract

- Plan approval authorizes planning only.
- Obtain fresh human approval for every exact mutation payload. A confirmation argument is a tool guard, not evidence of consent.
- A handoff to another skill invalidates prior mutation approval. The destination skill repeats preflight and approval.
- Before approval, show the operation, resource type and name, exact ID when known, normalized payload or diff, references, lifecycle effect, risk, and required confirmation phrase.

## Reuse and collisions

- Never reuse a resource by name alone.
- Compare exact ID, lifecycle, normalized content or configuration, references, campaign scope when relevant, and differences from the request.
- Ask the user to confirm reuse when more than one candidate exists or the candidate is not semantically identical.
- Use stable, business-meaningful names. Do not create a suffixed duplicate merely to bypass a collision.

## ETags and verification

- Immediately before an update, lifecycle transition, attachment, archive, or delete, call the matching get tool and copy `metadata.etag` exactly.
- Never retry an ETag mismatch automatically.
- Re-read or relist after every mutation and record the returned ID, lifecycle, ETag, job state, warnings, and next dependency.

## Ambiguous outcomes

- A transport timeout or unknown response does not mean failure.
- Stop and mark the operation `outcome-unknown`. Do not repeat a create, publish, policy create, or other non-idempotent operation until reconciled by exact read/search or Adobe UI verification.
- For asynchronous fragment publication, record the publication resource or status locator and poll with bounded intervals. When the workflow deadline is reached, report `pending`; resume status polling instead of publishing again.
- Decision Policies cannot be listed or deleted through this MCP. An unknown policy-create outcome requires AJO UI reconciliation before any retry.

## Stop conditions

Stop and ask when the sandbox differs, a write gate is disabled, required IDs or facts are missing, reuse is ambiguous, campaign scope is not DRAFT, an ETag changes, an upstream result is unexpected, or an outcome cannot be reconciled.
