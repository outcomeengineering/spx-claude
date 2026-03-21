---
name: testing
description: |
  Manage Spec Tree testing: create tests from assertions, run tests, check stale status.
  Use when creating tests, running tests, or synchronizing spec-test consistency after changes.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash
---

<!-- PLACEHOLDER: Full implementation in Phase 3 -->

<objective>

Create tests from assertion links in specs, invoke language-specific testing skills, run tests, and detect stale nodes lacking evidence.

Operates in two modes:

- **Direct invocation:** User asks to create or run tests.
- **Postflight:** Action skills trigger after making changes to synchronize spec-test consistency.

</objective>
