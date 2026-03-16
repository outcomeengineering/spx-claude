---
name: understanding
description: |
  Foundation skill — loaded automatically before any other spec-tree skill.
  When the user asks about methodology, node types, or how specs work, invoke this first.
allowed-tools: Read, Glob, Grep
---

<objective>

Load the Spec Tree methodology into the conversation so all subsequent skills operate from a shared foundation. This is a foundation skill — it loads once and emits a marker that other skills check before starting work.

</objective>

<quick_start>

Invoke `/understanding` before any spec-tree work. The skill reads 3 core reference files and emits a `<SPEC_TREE_FOUNDATION>` marker. If the marker is already present in the conversation, skip.

</quick_start>

<principles>

1. **FOUNDATION, NOT CONTEXT** — This skill loads methodology; it does not load project-specific artifacts. Use `/contextualizing` for target-specific context injection.
2. **LOAD ONCE** — Check for `<SPEC_TREE_FOUNDATION>` marker before loading. If present, skip.
3. **SPECS ARE PERMANENT** — The Spec Tree is a durable map. Nothing moves, nothing closes. Read `references/durable-map.md`.
4. **TWO NODE TYPES** — Enablers (infrastructure) and outcomes (hypothesis + assertions). No other node types exist. Read `references/node-types.md`.
5. **ASSERTIONS SPECIFY OUTPUT** — Assertions specify what the software does, locally verifiable by automated tests or agent review.
6. **DETERMINISTIC CONTEXT** — The tree structure defines what context an agent receives. No keyword search, no heuristics. This is handled by `/contextualizing`.
7. **ATEMPORAL VOICE** — Specs state product truth. Never narrate history. Flag temporal language as a quality issue.

</principles>

<workflow>

1. Check conversation for `<SPEC_TREE_FOUNDATION>` marker. If present, skip — already loaded.
2. Read core references (always loaded):
   - `references/durable-map.md` — specs as permanent truth, atemporal voice, node states
   - `references/node-types.md` — enabler vs outcome, directory structure
   - `references/assertion-types.md` — scenario, mapping, conformance, property, compliance
3. Note operational references (loaded on demand by other skills):
   - `references/decomposition-semantics.md` — when to nest, depth heuristics (used by `/decomposing`)
   - `references/ordering-rules.md` — sparse integer ordering, dependency encoding (used by `/authoring`, `/decomposing`)
   - `references/what-goes-where.md` — ADR/PDR/spec/test content taxonomy (used by `/aligning`)
4. Note template and example locations (read only when authoring):
   - `templates/product/product-name.product.md`
   - `templates/decisions/decision-name.adr.md`
   - `templates/decisions/decision-name.pdr.md`
   - `templates/nodes/enabler-name.md`
   - `templates/nodes/outcome-name.md`
   - `examples/` — concrete filled specs (read when you need to see what a completed spec looks like)
5. Emit the `<SPEC_TREE_FOUNDATION>` marker:

```text
<SPEC_TREE_FOUNDATION>
Loaded: durable-map, node-types, assertion-types
Operational references available: decomposition-semantics, ordering-rules, what-goes-where
Templates available: product, adr, pdr, enabler, outcome
Examples available in: examples/
</SPEC_TREE_FOUNDATION>
```

</workflow>

<success_criteria>

- [ ] Three core reference files read and understood
- [ ] Operational reference, template, and example locations known
- [ ] `<SPEC_TREE_FOUNDATION>` marker emitted
- [ ] Can explain: What is an enabler? What is an outcome? When to use each?
- [ ] Can explain: What does atemporal voice mean? Why does it matter?
- [ ] Can explain: What are the five assertion types and when to use each?

</success_criteria>
