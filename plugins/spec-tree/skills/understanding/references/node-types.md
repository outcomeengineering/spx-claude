<overview>
The Spec Tree contains two node types. Every directory in the tree (other than the root and `tests/`) is one of these.
</overview>

<enabler>

**Directory suffix:** `.enabler`
**Spec header:** `## Enables`
**Purpose:** Infrastructure that would be removed if all its dependents were retired.

Enablers exist to serve other nodes. They provide shared infrastructure, utilities, or foundational capabilities that higher-index siblings and their descendants depend on.

**Spec format:**

```markdown
## Enables

[What this enabler provides to its dependents and why they need it]

### Assertions

- Assertion text ([test](tests/file.unit.test.ts))
- Another assertion ([test](tests/file.unit.test.ts))
```

**Examples:**

- Test harness that all other nodes use
- Parser that multiple outcome nodes depend on
- State machine that several features build on
- Shared configuration or bootstrap logic

**When to create an enabler:**

- Two or more sibling nodes share a need → factor it into an enabler at a lower index
- Infrastructure that has no direct user-facing value but enables user-facing value
- Removing it would break its dependents

</enabler>

<outcome>

**Directory suffix:** `.outcome`
**Spec header:** `## Outcome`
**Purpose:** Hypothesis about what change a behavior will produce, verified by testable assertions.

Outcomes are the nodes that deliver value. Each outcome expresses one hypothesis and the assertions that define what must be true for the hypothesis to become verifiable.

**Spec format:**

```markdown
## Outcome

We believe that [hypothesis about what change this behavior will produce].

### Assertions

- Assertion text ([test](tests/file.unit.test.ts))
- Another assertion ([test](tests/file.unit.test.ts))
```

**Examples:**

- "We believe that aggregating child node states into a parent status will let developers identify stale subtrees without inspecting each node individually."
- "We believe that formatting output as a tree will let developers scan product status at a glance."

**When to create an outcome:**

- The behavior has direct or indirect user-facing value
- You can express a hypothesis about what change it produces
- You can define assertions that are testable

</outcome>

<common_structure>

**Directory structure:**

```text
NN-slug.{enabler|outcome}/
├── slug.md              # Spec file (no type suffix, no numeric prefix)
├── spx-lock.yaml        # Lock file (written by spx lock when tests pass)
├── tests/               # Co-located test files
│   ├── slug.unit.test.ts
│   └── slug.integration.test.ts
└── NN-child.{enabler|outcome}/   # Nested child nodes (optional)
```

**Spec file naming:**

- The spec file is always `{slug}.md` — no type suffix, no numeric prefix
- The slug matches the directory name without the numeric prefix and type suffix
- Example: `43-status-rollup.outcome/` contains `status-rollup.md`

**Test files:**

- Co-located in `tests/` within the node directory
- Named with test level suffix: `.unit.test.{ext}`, `.integration.test.{ext}`, `.e2e.test.{ext}`
- Every assertion in the spec must link to at least one test file

</common_structure>

<lock_file>

`spx-lock.yaml` binds spec content to test evidence via Git blob hashes:

```yaml
schema: spx-lock/v1
blob: a3b7c12
tests:
  - path: tests/slug.unit.test.ts
    blob: 9d4e5f2
```

- `blob` is the Git blob hash of the spec file
- Each test entry has the path and blob hash of the test file
- Lock is written only when all tests pass
- Same state always produces the same lock file (deterministic)

**Node states (derived from lock):**

| State          | Condition                                 | Required action         |
| -------------- | ----------------------------------------- | ----------------------- |
| **Needs work** | No lock file exists                       | Write tests, then lock  |
| **Stale**      | Spec or test blob changed since last lock | Re-lock (`spx lock`)    |
| **Valid**      | All blobs match                           | None — evidence current |

</lock_file>

<product_file>

The root of every tree contains `{product-name}.product.md`. This is not a node — it's the tree's anchor. It captures why the product exists and what change in user behavior it aims to achieve.

```text
spx/
├── product-name.product.md       # Product spec (the root)
├── 15-decision.adr.md            # Product-level decision
├── 15-constraint.pdr.md          # Product-level constraint
├── 21-first.enabler/             # First enabler node
└── 32-first.outcome/             # First outcome node
```

</product_file>

<decision_records>

ADRs and PDRs are flat files, not directories. They sit alongside nodes at any directory level:

- `NN-slug.adr.md` — Architecture Decision Record (governs HOW)
- `NN-slug.pdr.md` — Product Decision Record (governs WHAT)

Their numeric prefix encodes dependency scope: a decision at index 15 constrains all siblings at index 16 and above, including their descendants.

</decision_records>
