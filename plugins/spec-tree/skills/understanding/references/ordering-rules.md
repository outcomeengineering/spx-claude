<overview>
Numeric prefixes on directories and decision files encode **dependency order** within each directory.
</overview>

<core_rule>

A lower-index item constrains every sibling with a higher index — and that sibling's descendants.

```text
15-decision.adr.md        # Constrains everything at 16+
21-infra.enabler/          # Depends on 15; constrains 22+
32-feature.outcome/        # Depends on 15 and 21; constrains 33+
```

**Same index = independent.** Items sharing the same index are independent of each other. They all depend on the previous lower index, but not on each other.

```text
32-auth.outcome/           # Independent of billing
32-billing.outcome/        # Independent of auth
43-integration.outcome/    # Depends on BOTH 32s
```

</core_rule>

<distribution>

| Property       | Value                                        |
| -------------- | -------------------------------------------- |
| Range          | [10, 99] (two digits)                        |
| First item     | Start at 21 (room for ~10 insertions before) |
| Formula        | `i_k = 10 + floor(k * 89 / (N + 1))`         |
| For N=7        | 21, 32, 43, 54, 65, 76, 87                   |
| Gap size (N=7) | 11 slots between consecutive items           |

The formula distributes N expected items evenly across the two-digit range, leaving gaps for future insertions.

**Product root with 4 items (N=4):**

```text
i_k = 10 + floor(k * 89 / 5)
→ 27, 45, 63, 81
```

**Feature with 3 children (N=3):**

```text
i_k = 10 + floor(k * 89 / 4)
→ 32, 54, 76
```

</distribution>

<insertion>

When inserting between two existing indices, use the midpoint:

```text
Insert between 21 and 32: floor((21 + 32) / 2) = 26
Insert between 32 and 43: floor((32 + 43) / 2) = 37
```

When appending after the last index:

```text
Append after 87: floor((87 + 99) / 2) = 93
```

</insertion>

<fractional_indexing>

When integer gaps are fully exhausted (rare), use fractional indexing as an escape hatch:

```text
21-test-harness.enabler/
21.5-integration-harness.enabler/    # Inserted between 21 and 22
22-e2e-harness.enabler/
```

Fractional indexing should be avoided when possible. If you find yourself using it frequently, consider restructuring the directory.

</fractional_indexing>

<unified_number_space>

Within each directory, ALL items share the same number space — nodes, ADRs, PDRs. The numeric prefix comes first (for sorting), the type comes last (for identification):

```text
15-auth-strategy.adr.md            # Decision at 15
15-pricing-model.pdr.md            # Constraint at 15 (independent of ADR)
21-test-harness.enabler/           # Enabler at 21 (constrained by 15)
32-user-auth.outcome/              # Outcome at 32 (constrained by 15 and 21)
```

</unified_number_space>

<scope>

Numeric prefixes are only unique among siblings within the same directory. Different directories can reuse the same numbers:

```text
21-infra.enabler/21-setup.enabler/           # One "21"
21-infra.enabler/32-config.outcome/          # Different directory
32-feature.outcome/21-sub-setup.enabler/     # Another "21" — different parent
```

**Always use full paths** when referencing nodes. "outcome-21" is ambiguous; `32-feature.outcome/21-sub-setup.enabler` is not.

</scope>
