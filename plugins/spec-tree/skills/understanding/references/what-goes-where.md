<overview>

Every artifact in the Spec Tree has a specific purpose. Content placed in the wrong artifact creates confusion and duplication.

| Artifact type    | Purpose                    | Contains                         | Verified by         |
| ---------------- | -------------------------- | -------------------------------- | ------------------- |
| **ADR**          | GOVERNS how (architecture) | Decisions, rationale, invariants | Architecture review |
| **PDR**          | GOVERNS what (product)     | Constraints, product invariants  | Product review      |
| **Enabler spec** | DESCRIBES infrastructure   | What it provides, assertions     | Tests               |
| **Outcome spec** | DESCRIBES hypothesis       | Outcome belief, assertions       | Tests               |
| **Test**         | PROVES assertions          | Test code                        | Test runner         |

</overview>

<adr>

**Purpose:** GOVERNS how things are built. Constrains architecture, not product behavior.

**Contains:**

- Purpose — what concern this decision governs
- Context — business impact and technical constraints
- Decision — the chosen approach in one sentence
- Rationale — why this is right given constraints
- Trade-offs accepted — what was given up and why
- Invariants — algebraic properties that must hold
- Compliance — executable verification criteria (MUST / NEVER rules)

**Does NOT contain:** Outcomes, assertions, test references, or implementation code.

**Verified by:** Architecture review skills (e.g., `/reviewing-typescript-architecture`).

</adr>

<pdr>

**Purpose:** GOVERNS what the product does. Establishes product constraints that must hold across a subtree.

**Contains:**

- Purpose — what product behavior this decision governs
- Context — business impact and technical constraints
- Decision — the chosen approach in one sentence
- Rationale — why this is right for users
- Trade-offs accepted — what was given up and why
- Product invariants — observable behaviors users can always rely on
- Compliance — product behavior validation criteria (MUST / NEVER rules)

**Does NOT contain:** Outcomes, assertions, test references, or implementation code.

**Verified by:** Product/UX review.

</pdr>

<enabler_spec>

**Purpose:** DESCRIBES what infrastructure this node provides to its dependents.

**Contains:**

- What this enabler provides and why dependents need it
- Assertions specifying output — what must be true about this infrastructure

**Does NOT contain:** Outcome hypotheses, user behavior claims.

</enabler_spec>

<outcome_spec>

**Purpose:** DESCRIBES a hypothesis connecting a testable output to user behavior change and business impact.

**Contains:**

- Three-part hypothesis: WE BELIEVE THAT [output] WILL [outcome] CONTRIBUTING TO [impact]
- Assertions specifying the output — locally verifiable by tests or review

**Does NOT contain:** Architecture decisions (→ ADR), product constraints (→ PDR), implementation details.

</outcome_spec>

<test_files>

**Purpose:** PROVES that assertions hold.

**Contains:** Test code organized by level:

| Level       | Suffix                    | Question                             |
| ----------- | ------------------------- | ------------------------------------ |
| Unit        | `.unit.test.{ext}`        | Is our logic correct?                |
| Integration | `.integration.test.{ext}` | Does it work with real dependencies? |
| E2E         | `.e2e.test.{ext}`         | Does it work for users?              |

**Does NOT contain:** Spec content, decision rationale, or anything other than test code.

</test_files>

<flow>

```text
ADR/PDR ──governs──→ Spec ──asserts──→ Test
                      │                  │
                      │                  │
                 "what should       "does it
                  be true"          hold?"
```

</flow>

<common_misplacements>

| Content                  | Wrong location | Correct location |
| ------------------------ | -------------- | ---------------- |
| Architecture choice      | Spec           | ADR              |
| Product constraint       | Spec           | PDR              |
| Outcome hypothesis       | ADR            | Outcome spec     |
| Test reference           | ADR/PDR        | Spec assertions  |
| Implementation detail    | Spec           | Code (not spec)  |
| "How to build it"        | Spec           | ADR or code      |
| "What users can rely on" | Spec           | PDR              |
| Cross-cutting invariant  | Child spec     | Ancestor spec    |

</common_misplacements>
