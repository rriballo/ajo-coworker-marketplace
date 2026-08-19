---
name: ajo-manage-expression-fragments
description: Create, compare, update, publish, inspect, and optionally attach Adobe Journey Optimizer expression fragments. Use for reusable TEXT, HTML, or JSON fragment content and Decisioning item content references. Do not use for email Content Templates, visual fragments, broad Decisioning builds, or cleanup.
---

# Manage AJO expression fragments

Follow `../../references/write-safety-and-recovery.md` and `../../references/operation-manifest-and-output.md`. The sandbox is fixed to `aepenablementfy21`.

## Required inputs

- Create: name, description when needed, subtype `TEXT`, `HTML`, or `JSON`, and exact expression content.
- Update: exact fragment ID and proposed full replacement content.
- Optional attachment: exact draft item ID and stable reference key such as `hero`, `body`, or `footer`.

## Workflow

1. Call `ajo_get_capabilities`; verify the Content write gate and, for attachment, the Decisioning write gate.
2. List and compare existing fragments. Never reuse by name alone; show exact ID, subtype, lifecycle, draft/live content, and differences.
3. Create only `type=expression`, channel `shared` fragments. Obtain approval for the exact payload, then re-read the created fragment.
4. For update, fresh-get the fragment, show the old/full proposed expression and ETag, obtain approval, update, and re-read.
5. Before publication, fresh-get the exact draft and obtain separate approval. Record the accepted publication status locator.
6. Poll publication status at bounded intervals until complete, error, or workflow deadline. At the deadline report pending; after transport ambiguity resume polling and never publish again blindly.
7. Get the live fragment and verify the published content.
8. For item attachment, ensure the item is draft. If approved, fresh-get it, separately approve return-to-draft, execute, and re-read before attachment.
9. Fresh-get the draft item, present the key/fragment mapping and ETag, obtain approval, attach, re-read, and validate readiness.
10. If reapproval is requested, fresh-get and obtain a new approval before `ajo_decisioning_approve_item`.

The public Content API has no fragment delete. Visual fragments are not valid Decisioning item references. Return a resumable operation receipt and stop on any mismatch or unknown outcome.
