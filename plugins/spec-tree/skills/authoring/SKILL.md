---
name: authoring
description: >-
  ALWAYS invoke this skill when adding, defining, or creating specs, decisions, or nodes.
  NEVER author spec tree artifacts without this skill.
allowed-tools: Read, Glob, Grep, Write, Edit
---

<objective>

Author Spec Tree artifacts — product specs, decision records (ADR/PDR), enabler nodes, and outcome nodes — using templates from the `understanding` foundation skill. Guides placement, index assignment, and content quality.

</objective>

<quick_start>

**PREREQUISITE**: Check for `<SPEC_TREE_FOUNDATION>` marker. If absent, invoke `/understanding` first.

Templates and examples live in the `understanding` skill's directory:

- `understanding` skill → `templates/product/product-name.product.md`
- `understanding` skill → `templates/decisions/decision-name.adr.md`
- `understanding` skill → `templates/decisions/decision-name.pdr.md`
- `understanding` skill → `templates/nodes/enabler-name.md`
- `understanding` skill → `templates/nodes/outcome-name.md`
- `understanding` skill → `examples/` — filled specs for reference

Read the appropriate template before drafting.

</quick_start>

<workflow>

<step name="intake">

**Step 1: Determine what to create**

Ask or infer from context:

| Artifact         | When to create                        | Template                                    |
| ---------------- | ------------------------------------- | ------------------------------------------- |
| **Product spec** | Bootstrapping a new tree              | `templates/product/product-name.product.md` |
| **ADR**          | Architecture decision needs recording | `templates/decisions/decision-name.adr.md`  |
| **PDR**          | Product constraint needs recording    | `templates/decisions/decision-name.pdr.md`  |
| **Enabler node** | Shared infrastructure for 2+ siblings | `templates/nodes/enabler-name.md`           |
| **Outcome node** | User-facing behavior with hypothesis  | `templates/nodes/outcome-name.md`           |

If unclear which type, apply the decision table from `understanding` → `references/node-types.md`:

- Delivers user-facing value? → Outcome
- Exists only to serve other nodes? → Enabler
- Governs how things are built? → ADR
- Governs what the product does? → PDR

</step>

<step name="context">

**Step 2: Load context for placement**

Check for `<SPEC_TREE_CONTEXT>` marker. If absent or targeting a different path, invoke `/contextualizing` for the parent directory where the artifact will be placed.

This loads:

- Existing siblings (to avoid duplication and determine index)
- Ancestor ADRs/PDRs (to respect constraints)
- Parent spec (to understand scope)

**Bootstrap mode**: If creating the first artifact in an empty tree (product spec), no context is needed. Proceed directly.

</step>

<step name="placement">

**Step 3: Determine placement and index**

**For product specs:** Place at `spx/{product-name}.product.md`. No index.

**For ADRs/PDRs:** Place in the directory where the decision's scope applies. Assign an index using the ordering rules from `understanding` → `references/ordering-rules.md`:

- The index encodes dependency: lower constrains higher
- An ADR/PDR at index N constrains all siblings at N+1 and above
- Use the distribution formula for new items: `i_k = 10 + floor(k * 89 / (N + 1))`
- Use midpoint insertion between existing indices

**For enabler/outcome nodes:** Place as a child of the parent where the concern belongs.

- Determine the index relative to siblings
- Enablers that others depend on get lower indices
- Independent outcomes can share the same index
- Derive the slug from the concern name (lowercase, hyphenated)

Present the proposed placement to the user before creating files.

</step>

<step name="clarify">

**Step 4: Clarify content**

Before drafting, gather what's needed for the artifact type:

**Product spec:**

- Why does this product exist?
- What is the product hypothesis (output → outcome → impact)?
- What's included vs excluded?
- Any product-wide compliance rules?

**ADR:**

- What concern does this govern?
- What is the decision?
- What alternatives were considered?
- What trade-offs are accepted?
- What compliance rules follow from this decision?

**PDR:**

- What product behavior does this govern?
- What is the decision?
- What product invariants does this establish?

**Enabler:**

- What does this enabler provide?
- Which siblings depend on it?
- What assertions specify its output?

**Outcome:**

- What is the three-part hypothesis?
  - Output: what the software does (testable)
  - Outcome: measurable change in user behavior
  - Impact: business value
- What assertions specify the output?

Use `AskUserQuestion` for genuine gaps. Do not ask about information already provided in the conversation.

</step>

<step name="draft">

**Step 5: Draft the artifact**

Read the appropriate template from the `understanding` skill. Fill it using the gathered content.

**Voice rules** (from `understanding` → `references/durable-map.md`):

- **Atemporal**: State product truth. Never narrate history ("we discovered", "currently", "after investigating").
- **Permanent**: Write as if this will be true forever. If it wouldn't, it's temporal.
- **Test**: Read any sentence aloud. If it would sound wrong after the work is done, rewrite it.

**Assertion rules** (from `understanding` → `references/assertion-types.md`):

- Every outcome must have at least one assertion
- Each assertion must link to a test file: `([test](tests/{slug}.{level}.test.{ext}))`
- Match assertion type to test strategy: Scenario → example-based, Property → property-based, etc.
- Test files don't need to exist yet — the link is a contract for what will be created

**Enabler assertions**: Same rules apply. Enablers have assertions too — they specify what the infrastructure must do.

</step>

<step name="validate">

**Step 6: Validate the draft**

Before writing files, check:

- [ ] Correct artifact type for the content
- [ ] Placed in the right directory at the right index
- [ ] Slug matches directory name convention (`{NN}-{slug}.{enabler|outcome}/` for nodes)
- [ ] Spec file named `{slug}.md` (no type suffix, no numeric prefix)
- [ ] Atemporal voice throughout — no temporal markers
- [ ] For outcomes: three-part hypothesis present (output → outcome → impact)
- [ ] For enablers: enables statement describes what it provides
- [ ] All assertions have test links (even if test files don't exist yet)
- [ ] Assertion types match test strategy
- [ ] ADR/PDR compliance rules use MUST/NEVER format with `([review])` tags
- [ ] No content misplacement (per `understanding` → `references/what-goes-where.md`)

</step>

<step name="create">

**Step 7: Create files**

**For nodes (enabler/outcome):**

```text
spx/{parent-path}/{NN}-{slug}.{enabler|outcome}/
├── {slug}.md        # Spec file
└── tests/           # Empty directory for future tests
```

1. Create the directory
2. Write the spec file
3. Create the `tests/` directory

**For decision records:**

```text
spx/{scope-path}/{NN}-{slug}.{adr|pdr}.md
```

Write the file directly.

**For product specs:**

```text
spx/{product-name}.product.md
```

Write the file. If `spx/CLAUDE.md` doesn't exist, note that one should be created as a project guide.

</step>

<step name="deliver">

**Step 8: Summarize and recommend next steps**

Report what was created:

- Artifact type and path
- Index and placement rationale
- Open decisions (if any were identified during drafting)

Recommend next steps based on artifact type:

| Created                      | Recommended next                                     |
| ---------------------------- | ---------------------------------------------------- |
| Product spec                 | Author top-level nodes with `/authoring`             |
| ADR/PDR                      | Verify compliance in affected nodes with `/aligning` |
| Enabler                      | Author dependent outcome nodes                       |
| Outcome with many assertions | Decompose with `/decomposing`                        |
| Outcome with few assertions  | Write tests with `/testing`                          |

</step>

</workflow>

<anti_patterns>

**Writing implementation details in specs.** Specs describe *what*, not *how*. "How" belongs in ADRs (architecture) or code. If you're writing about function signatures, data structures, or algorithms, stop — that's an ADR or code.

**Copying temporal language from user input.** Users naturally say "we need to fix X" or "currently the system does Y." Translate to atemporal: "The system does Z" or "X handles Y correctly."

**Creating outcomes without hypotheses.** Every outcome must express: output → outcome → impact. If you can't write the hypothesis, the scope may be wrong — it might be an enabler or need further clarification.

**Placing assertions in ADRs/PDRs.** Decision records govern; they don't assert. Assertions belong in specs. ADRs/PDRs have compliance rules (MUST/NEVER) verified by review, not by tests.

**Numbering from 1.** Indices start at 10+ and use the sparse distribution formula. Never use single-digit indices.

</anti_patterns>

<success_criteria>

Authoring is complete when:

- [ ] Artifact type determined (product, ADR, PDR, enabler, outcome)
- [ ] Context loaded for placement (or bootstrap mode for empty tree)
- [ ] Index and placement determined using ordering rules
- [ ] Content gathered from user (genuine gaps only)
- [ ] Template read and filled with atemporal voice
- [ ] Validation checklist passes
- [ ] Files created in correct location
- [ ] Next steps recommended

</success_criteria>
