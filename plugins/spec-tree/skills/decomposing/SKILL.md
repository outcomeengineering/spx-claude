---
name: decomposing
description: >-
  ALWAYS invoke this skill when breaking down, splitting, scoping, or structuring spec tree nodes.
  NEVER decompose specs without this skill.
allowed-tools: Read, Glob, Grep, Write, Edit
---

<objective>

Decompose an existing Spec Tree node into child nodes when it contains multiple independent concerns. Determines enabler vs outcome for each child, applies sparse integer ordering, redistributes assertions, and validates decomposition quality.

</objective>

<quick_start>

**PREREQUISITE**: Check for `<SPEC_TREE_FOUNDATION>` marker. If absent, invoke `/understanding` first.

Read the reference material from the understanding skill before decomposing:

- `understanding` skill → `references/decomposition-semantics.md` — when to decompose, enabler vs outcome, depth, shared enabler extraction
- `understanding` skill → `references/ordering-rules.md` — sparse integer ordering, insertion, fractional indexing
- `understanding` skill → `references/node-types.md` — enabler and outcome structure, directory layout

Then follow the workflow below.

</quick_start>

<workflow>

<step name="load_context">

**Step 1: Load tree context**

Check for `<SPEC_TREE_CONTEXT>` marker matching the target node. If absent, invoke `/contextualizing`.

Read the target node's spec file. Note:

- Current hypothesis (outcome) or enables statement (enabler)
- All assertions, grouped by type (scenario, mapping, conformance, property, compliance)
- Total assertion count
- Any existing child nodes

</step>

<step name="assess_need">

**Step 2: Assess whether decomposition is needed**

Apply the triggers from `decomposition-semantics.md`:

| Trigger              | Threshold                                              |
| -------------------- | ------------------------------------------------------ |
| Assertion count      | More than ~7 across all types                          |
| Context payload      | Exceeds an agent's reliable working set                |
| Independent concerns | Contains assertions with no relationship to each other |
| Separate validation  | Parts could be validated independently                 |

**Do NOT decompose if:**

- A single coherent hypothesis covers all assertions
- Assertions are tightly coupled and meaningless in isolation
- Decomposition would create children with only 1–2 trivial assertions

If decomposition is not warranted, say so and stop. Do not force structure.

</step>

<step name="identify_concerns">

**Step 3: Identify independent concerns**

This is the creative step. Read through the assertions and group them by concern — a concern is a coherent set of assertions that:

- Share a common subject (the same subsystem, behavior, or capability)
- Would be validated together
- Would be meaningless if split further

For each group, draft a one-line summary of what that concern is about.

**Seam-finding heuristics:**

- Assertions about different data types or domains → separate concerns
- Assertions about different user-facing behaviors → separate concerns
- Assertions about setup/infrastructure vs behavior → enabler vs outcome
- Assertions that could fail independently → separate concerns
- Assertions connected by "and" in the parent hypothesis → candidate split points

Present the concern groupings to the user before proceeding.

</step>

<step name="extract_enablers">

**Step 4: Extract shared enablers**

Before assigning outcome nodes, check: do any concern groups share infrastructure?

Per `decomposition-semantics.md` → `shared_enabler_extraction`:

- Two or more groups share a dependency → extract as enabler
- The shared piece is infrastructure, not user-facing value
- Removing it would break multiple siblings

Enablers get **lower indices** than the outcomes that depend on them.

</step>

<step name="assign_types">

**Step 5: Assign node types**

For each concern group, apply the decision table from `decomposition-semantics.md`:

| Question                                                             | If yes  |
| -------------------------------------------------------------------- | ------- |
| Does it deliver user-facing value (directly or indirectly)?          | Outcome |
| Does it exist only to serve other nodes?                             | Enabler |
| Would you remove it if all its dependents were retired?              | Enabler |
| Can you express a three-part hypothesis (output → outcome → impact)? | Outcome |

When unclear, default to **outcome**.

</step>

<step name="assign_indices">

**Step 6: Assign sparse integer indices**

Apply ordering rules from `ordering-rules.md`:

1. Count the total number of children (N).
2. Apply the distribution formula: `i_k = 10 + floor(k * 89 / (N + 1))`
3. Enablers that other nodes depend on get lower indices.
4. Independent outcomes can share the same index.
5. A node that depends on another gets a higher index.

**Dependency encoding:**

- Lower index constrains higher index (and its descendants)
- Same index = independent of each other
- ADRs/PDRs at lower indices constrain all higher siblings

</step>

<step name="redistribute_assertions">

**Step 7: Redistribute assertions**

Move assertions from the parent spec into the correct child nodes:

- Each assertion goes to the child whose concern it belongs to
- **Cross-cutting assertions stay in the parent** — these span multiple children
- If an assertion could go in either of two children, it's probably cross-cutting

Per `decomposition-semantics.md` → `cross_cutting_assertions`: if the parent accumulates too many cross-cutting assertions, extract a shared enabler at a lower index.

Update test links in assertions to reflect the new `tests/` location in each child.

</step>

<step name="write_specs">

**Step 8: Write child specs**

For each child node:

1. Create the directory: `{index}-{slug}.{enabler|outcome}/`
2. Create the spec file: `{slug}.md`
3. Create the `tests/` directory
4. Write the spec content:

**For outcomes:** Use the outcome template from `understanding` → `templates/nodes/outcome-name.md`. Write the three-part hypothesis:

- **Output** — what the software does (tested by assertions)
- **Outcome** — measurable change in user behavior
- **Impact** — business value

**For enablers:** Use the enabler template from `understanding` → `templates/nodes/enabler-name.md`. Write the enables statement.

5. Add assertions redistributed from the parent.

**Revise the parent spec:** The parent's hypothesis may need to become a summary of its children. Remove assertions that moved to children. Keep only cross-cutting assertions.

</step>

<step name="validate">

**Step 9: Validate decomposition quality**

Check each criterion:

- [ ] No child has only 1–2 trivial assertions (too granular)
- [ ] No child exceeds ~7 assertions (recursive decomposition signal)
- [ ] Every enabler has at least two dependents (not orphaned)
- [ ] Cross-cutting assertions in parent are minimal
- [ ] Children collectively cover the parent's scope (nothing lost)
- [ ] Enablers have lower indices than their dependents
- [ ] Independent nodes share the same index or have no ordering relationship
- [ ] Spec files use atemporal voice (no temporal language)
- [ ] Directory names follow `{NN}-{slug}.{enabler|outcome}` convention
- [ ] Spec files are `{slug}.md` (no type suffix, no numeric prefix)

If a child exceeds ~7 assertions, flag it for recursive decomposition — but do not decompose recursively in the same invocation without user confirmation.

</step>

</workflow>

<anti_patterns>

**Decomposing by implementation layer.** Don't create children like "frontend," "backend," "database." Decompose by user-facing concern or shared infrastructure.

**Forcing three levels.** The old capability/feature/story hierarchy is gone. Depth emerges from actual complexity. A node with 4 clear assertions doesn't need children.

**Creating enablers for everything.** Enablers exist only when two or more siblings share a dependency. A single-use piece of infrastructure is not an enabler — it belongs inside the outcome that uses it.

**Losing assertions during redistribution.** Every parent assertion must end up in exactly one child or remain as a cross-cutting assertion in the parent. Diff the before/after to verify.

**Renumbering siblings.** When decomposing one node, do not renumber its siblings. The parent's index stays the same; only its internal structure changes.

</anti_patterns>

<success_criteria>

Decomposition is complete when:

- [ ] Decomposition need assessed (not forced)
- [ ] Concerns identified and presented to user
- [ ] Enablers extracted where shared infrastructure exists
- [ ] Each child has correct node type (enabler or outcome)
- [ ] Sparse integer indices assigned following ordering rules
- [ ] All assertions redistributed (none lost, cross-cutting in parent)
- [ ] Child specs written using templates from understanding
- [ ] Parent spec revised to reflect decomposition
- [ ] Validation checklist passes

</success_criteria>
