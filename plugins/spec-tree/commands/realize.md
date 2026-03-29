---
description: Run the spec-tree TDD flow on a subtree or discover work from spx/EXCLUDE
argument-hint: [node-path]
---

<objective>
Run the full spec-tree TDD flow (contextualize, architect, test, code with review gates) and commit.

Two modes:

1. **With argument** (`/realize some/node`): Run TDD on the given subtree, then stop.
2. **Without argument**: Determine work from conversation context. If nothing is clear, fall back to `spx/EXCLUDE` — nodes with specs and tests but no implementation.

</objective>

<context>
**Excluded Nodes (specs exist, implementation does not):**
!`cat spx/EXCLUDE 2>/dev/null || echo '(no spx/EXCLUDE file found)'`

**Git Status:**
!`git status --short || echo 'Not a git repo'`

**Recent Commits:**
!`git log --oneline -5 || echo 'Not a git repo'`

**Project Language Indicators:**
!`ls pyproject.toml package.json tsconfig.json 2>/dev/null || echo 'No indication of Python or TypeScript'`
</context>

<process>

## Step 1: Determine the work queue

**If `$ARGUMENTS` is provided:** The argument is the node path. The work queue is that single node.

**If no argument:** Check conversation context for a node the user wants implemented. If nothing is clear, read `spx/EXCLUDE`. Each non-comment, non-blank line is a node path relative to `spx/`.

If no work is found, report "Nothing to realize" and stop.

## Step 2: Order by dependency

When multiple nodes are queued, sort by numeric index prefix (lower first). Lower-indexed nodes constrain higher-indexed ones and must be realized first.

## Step 3: Realize each node

For each node path, in order:

**3a. Remove from EXCLUDE**

If the node is listed in `spx/EXCLUDE`, remove its line. Then run the project's sync command (defined in `spx/CLAUDE.md`) so the node's tests join the quality gate.

**3b. Load context**

```text
Skill tool → { "skill": "spec-tree:contextualizing", "args": "spx/{node-path}" }
```

**3c. Run the TDD flow**

```text
Skill tool → { "skill": "spec-tree:coding" }
```

This runs the full 8-phase flow: methodology, context, architect, review, test, review, implement, review.

**3d. Commit the realized node**

```text
Skill tool → { "skill": "spec-tree:committing-changes" }
```

**3e. Move to next node**

Do NOT stop or ask the user. Proceed immediately to the next node.

## Step 4: On failure

If a node's TDD flow fails (review gate rejects after max retries, tests won't pass, or implementation blocked):

- STOP the loop — do not skip to the next node
- Report which node failed and at which phase
- Leave remaining nodes in `spx/EXCLUDE`

## Step 5: Report

When all nodes are realized (or on failure), report final status.

</process>

<success_criteria>

- All targeted nodes realized (tests passing, implementation complete)
- Each node committed individually
- Realized nodes removed from `spx/EXCLUDE`
- No nodes skipped — dependency order respected

</success_criteria>
