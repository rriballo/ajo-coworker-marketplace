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
