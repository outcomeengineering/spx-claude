---
name: contextualizing
description: |
  Deterministic context injection from Spec Tree structure.
  Use before working on any node to load its full context from root to target.
  Foundation skill that walks the tree and injects lower-index siblings at each level.
allowed-tools: Read, Glob, Grep
---

<!-- PLACEHOLDER: Full implementation in Phase 2 -->

<objective>

Walk the Spec Tree from product root to target node, deterministically collecting context: ancestor specs along the path, lower-index siblings' specs at each directory level. Emit `<SPEC_TREE_CONTEXT target="...">` marker with the collected context manifest.

</objective>
