---
name: testing
description: |
  Manage Spec Tree lock file lifecycle: create tests from assertions, run tests, write lock files.
  Use when locking nodes, checking stale status, or synchronizing spec-test consistency after changes.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

<!-- PLACEHOLDER: Full implementation in Phase 3 -->

<objective>

Manage the lock file lifecycle for Spec Tree nodes. Creates tests from assertion links in specs, invokes language-specific testing skills, runs tests, and writes `spx-lock.yaml` when all pass. Detects stale nodes and flags outcomes lacking evidence.

Operates in two modes:

- **Direct invocation:** User asks to lock nodes or create tests.
- **Postflight:** Action skills trigger after making changes to synchronize spec-test consistency.

</objective>
