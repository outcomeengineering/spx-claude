<overview>

When looking at a node's scope, ask: **"Is this one coherent thing, or does it contain multiple independent concerns?"**

If it contains multiple concerns, decompose into child nodes. Each child is either an enabler (shared infrastructure) or an outcome (hypothesis + assertions).

</overview>

<enabler_vs_outcome>

| Question                                                             | Answer  |
| -------------------------------------------------------------------- | ------- |
| Does it deliver user-facing value (directly or indirectly)?          | Outcome |
| Does it exist only to serve other nodes?                             | Enabler |
| Would you remove it if all its dependents were retired?              | Enabler |
| Can you express a three-part hypothesis (output → outcome → impact)? | Outcome |

When unclear, default to **outcome**. Outcomes are the primary building blocks — each expresses a hypothesis connecting a testable output to a measurable change in user behavior and its expected business impact. Enablers emerge when you notice shared infrastructure across siblings.

</enabler_vs_outcome>

<when_to_decompose>

Decompose a node when:

- It has more than ~7 assertions across all types (scenarios, mappings, conformance, properties, compliance) — the spec is doing too much
- Its deterministic context payload exceeds an agent's reliable working set
- It contains independent concerns that could be validated separately
- Two assertions in different parts of the spec have no relationship to each other

Do NOT decompose when:

- A single coherent hypothesis covers all assertions
- The assertions are tightly coupled and meaningless in isolation
- Decomposition would create nodes with only 1-2 trivial assertions

</when_to_decompose>

<depth>

The tree has no fixed number of levels. A simple product might have:

```text
spx/
├── product.product.md
└── 21-core.outcome/
```

A complex product might nest 4-5 levels deep. Depth emerges from the product's actual complexity, not from a prescribed hierarchy.

</depth>

<children_heuristic>

At any directory level, aim for at most ~7 child nodes. This is not a hard limit — it's a signal:

- More than 7 children → consider grouping related children under a parent outcome or enabler
- Fewer than 3 children → consider whether the parent node is necessary

The sparse integer ordering formula `i_k = 10 + floor(k * 89 / (N+1))` for N=7 produces the canonical sequence 21, 32, 43, 54, 65, 76, 87.

</children_heuristic>

<shared_enabler_extraction>

Extract an enabler when:

- Two or more sibling nodes share a dependency
- The shared piece is infrastructure, not user-facing value
- Removing the shared piece would break multiple siblings

Place the enabler at a **lower index** than the nodes that depend on it:

```text
NN-shared-infra.enabler/       # Lower index
NN+k-depends-on-it.outcome/    # Higher index, depends on enabler
NN+k-also-depends.outcome/     # Also depends on enabler
```

</shared_enabler_extraction>

<cross_cutting_assertions>

When a behavior spans multiple child nodes, the assertion belongs in the **lowest common ancestor**:

- The ancestor's spec captures cross-cutting behavior
- Child nodes handle their local concerns
- If an ancestor accumulates too many cross-cutting assertions, extract a shared enabler at a lower index

</cross_cutting_assertions>

<growth_patterns>

**Starting small:** A new tree starts with a product file and a handful of nodes. Structure emerges as the product grows.

```text
spx/
├── product.product.md
├── 21-core-infra.enabler/
└── 43-first-feature.outcome/
```

**Horizontal growth:** Adding a new independent concern at the same level.

```text
spx/
├── product.product.md
├── 21-core-infra.enabler/
├── 43-first-feature.outcome/
└── 54-second-feature.outcome/     # New sibling
```

**Vertical growth:** Decomposing an existing node into children.

```text
43-first-feature.outcome/
├── first-feature.md
├── 21-shared-setup.enabler/       # Extracted enabler
├── 32-sub-behavior-a.outcome/     # Decomposed from parent
└── 43-sub-behavior-b.outcome/     # Decomposed from parent
```

**Restructuring:** When a child grows into its own complex subtree, the tree absorbs this naturally. No renumbering of siblings. The parent's index stays the same; only its internal structure changes.

</growth_patterns>
