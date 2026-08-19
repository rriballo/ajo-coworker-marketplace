# Audience read contract

Audience access is definition-only and read-only.

1. Call `ajo_aep_list_audiences` and follow pagination.
2. Select the system ID from `data.children[].id`.
3. Call `ajo_aep_get_audience` with that `id`, never an external or similarly named `audienceId`.
4. Report stored PQL, schema, merge policy, evaluation modes, origin, lifecycle, labels, dependencies, and dependents when present.

These tools do not expose audience members, estimates, qualification results, or profile eligibility simulation. Stored PQL can inform requirements, but never copy it into a Decisioning eligibility rule until the user confirms every referenced XDM field exists in the organization schema.
