---
name: ajo-cleanup
description: Plan and execute explicitly requested destructive cleanup of Adobe Journey Optimizer Decisioning resources in safe reverse dependency order. Never invoke automatically.
disable-model-invocation: true
---

# Clean up AJO Decisioning resources

This is a destructive, manual-only workflow. Do not infer deletion intent from phrases such as “tidy,” “replace,” “start over,” or “remove duplicates.” Require the user to identify the exact resources they want removed.

1. Inventory every target with read tools and show names, IDs, lifecycle state, and current ETags.
2. Inspect dependencies visible through the Decisioning persistence API.
3. Warn that campaign and journey Decision Policy references are not visible to this server. Require the user to verify and remove those references in AJO.
4. Present a cleanup plan in reverse dependency order:
   - Strategies.
   - Collections.
   - Ranking formulas and eligibility rules.
   - Items.
5. Explain that placements have no documented delete tool and Content fragments cannot be deleted through the public Content API.
6. Obtain explicit human approval separately for every archive or delete operation.
7. Immediately before each mutation, retrieve the same resource and copy its fresh `metadata.etag`.
8. Supply the exact confirmation phrase required by that tool only after approval.
9. Stop on ETag mismatch, incomplete dependency scanning, unknown references, or any unexpected upstream response. Never retry automatically.
10. Re-read or relist after every mutation and produce an audit summary of completed, blocked, and externally required actions.

Archival is irreversible. Never archive as a substitute for a failed delete unless the user explicitly approves archival after understanding that consequence.
