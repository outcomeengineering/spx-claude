<overview>
Complete reference of all document types in the Outcome Engineering framework hierarchy and their requirements.
</overview>

<work_item_hierarchy>

```text
Product
└── Capability (E2E level)
    ├── {slug}.capability.md     REQUIRED
    ├── {topic}.prd.md           OPTIONAL (enrichment)
    ├── NN-{slug}.adr.md         OPTIONAL (architectural decisions)
    ├── NN-{slug}.pdr.md         OPTIONAL (product decisions)
    ├── tests/                   OPTIONAL (co-located tests)
    └── Feature (Integration level)
        ├── {slug}.feature.md    REQUIRED
        ├── NN-{slug}.adr.md     OPTIONAL (architectural decisions)
        ├── NN-{slug}.pdr.md     OPTIONAL (product decisions)
        ├── tests/               OPTIONAL (co-located tests)
        └── Story (Unit level)
            ├── {slug}.story.md  REQUIRED
            └── tests/           OPTIONAL (co-located tests)
```

</work_item_hierarchy>

<document_types>

<product_level>

**Location**: `spx/`

| Document      | Pattern                 | Required? | Purpose                              |
| ------------- | ----------------------- | --------- | ------------------------------------ |
| Project Guide | `CLAUDE.md`             | YES       | Project structure and navigation     |
| Product ADRs  | `NN-{slug}.adr.md`      | NO        | Product-wide architectural decisions |
| Product PDRs  | `NN-{slug}.pdr.md`      | NO        | Product-wide product decisions       |
| Product PRD   | `{product-name}.prd.md` | NO        | Optional product-wide requirements   |

</product_level>

<capability_level>

**Location**: `spx/NN-{slug}.capability/`

| Document        | Pattern                | Required?     | Purpose                             |
| --------------- | ---------------------- | ------------- | ----------------------------------- |
| Capability Spec | `{slug}.capability.md` | YES           | E2E scenario definition             |
| PRD             | `{topic}.prd.md`       | NO (optional) | Product requirements catalyst       |
| Capability ADRs | `NN-{slug}.adr.md`     | NO            | Capability-scoped arch decisions    |
| Capability PDRs | `NN-{slug}.pdr.md`     | NO            | Capability-scoped product decisions |
| Tests           | `tests/`               | NO            | Co-located tests                    |

**Note**: PRD is optional enrichment. If PRD exists but spec is missing, offer to create spec from PRD.

</capability_level>

<feature_level>

**Location**: `spx/NN-{slug}.capability/NN-{slug}.feature/`

| Document     | Pattern             | Required? | Purpose                          |
| ------------ | ------------------- | --------- | -------------------------------- |
| Feature Spec | `{slug}.feature.md` | YES       | Integration scenario definition  |
| Feature ADRs | `NN-{slug}.adr.md`  | NO        | Feature-scoped arch decisions    |
| Feature PDRs | `NN-{slug}.pdr.md`  | NO        | Feature-scoped product decisions |
| Tests        | `tests/`            | NO        | Co-located tests                 |

**Note**: Technical details belong in feature.md, not separate TRD documents.

</feature_level>

<story_level>

**Location**: `spx/.../NN-{slug}.story/`

| Document   | Pattern           | Required? | Purpose                          |
| ---------- | ----------------- | --------- | -------------------------------- |
| Story Spec | `{slug}.story.md` | YES       | Atomic implementation definition |
| Tests      | `tests/`          | NO        | Co-located tests                 |

**Note**: Stories do NOT have their own ADRs or PDRs. They inherit decisions from parent feature/capability.

</story_level>

</document_types>

<document_content_requirements>

<spec_files>

**Applies to**: `.capability.md`, `.feature.md`, `.story.md`

**Must contain** (contract — references must resolve):

- **Purpose**: What this container delivers and why it matters
- **Requirements**: Functional and quality requirements
- **Outcomes**: Numbered Gherkin scenarios with test file tables (links must resolve to actual files)

**May contain**:

- **Test Strategy**: Component/level/harness/rationale table (contract)
- **Architectural Constraints**: References to applicable ADRs and PDRs (contract)
- **Analysis** (stories only): Files, constants, config examined (context — implementation may diverge)

</spec_files>

<prd_content>

**PRD (Product Requirements Document)**:

- User value proposition
- Customer journey
- Measurable outcomes (X% improvement targets)
- Acceptance tests (BDD scenarios)

</prd_content>

<adr_content>

**Architectural Decision Records** must contain:

- **Purpose**: What architectural concern this decision governs (atemporal — state as permanent truth)
- **Decision**: What is being decided
- **Consequences**: Trade-offs and implications
- **Compliance**: How adherence will be verified (code review criteria)

</adr_content>

<pdr_content>

**Product Decision Records** must contain:

- **Purpose**: What product behavior this decision governs (atemporal — state as permanent truth)
- **Decision**: What product behavior is being decided
- **Product Invariants**: Observable behaviors users can rely on
- **Compliance**: How adherence will be verified (product behavior validation)

</pdr_content>

<adr_vs_pdr>

- **ADR**: Governs code architecture (HOW to build)
- **PDR**: Governs product behavior (WHAT users experience)

</adr_vs_pdr>

</document_content_requirements>

<prd_as_optional_enrichment>
PRD documents are **optional** at all levels. The spec file (`.capability.md`, `.feature.md`, `.story.md`) is the only required document for each work item.

**When PRD exists without spec file:**

If a PRD exists at a level but the corresponding spec file is missing, offer to create the spec from the requirements document. This handles cases where work was initiated from a requirements document but the spec wasn't yet created.

</prd_as_optional_enrichment>

<status_determination>
Status is derived from whether tests pass, not from directory location or spec content.
</status_determination>

<test_colocation>
Tests are co-located with their specs in `spx/.../tests/`.

| Level      | Location                           | Test Suffix             |
| ---------- | ---------------------------------- | ----------------------- |
| Capability | `spx/NN-{slug}.capability/tests/`  | `*.e2e.test.ts`         |
| Feature    | `spx/.../NN-{slug}.feature/tests/` | `*.integration.test.ts` |
| Story      | `spx/.../NN-{slug}.story/tests/`   | `*.unit.test.ts`        |

</test_colocation>

<bsp_numbering>
**Binary Space Partitioning (BSP)** encodes dependency order: lower BSP items are dependencies that higher-BSP items may rely on; same BSP means independent. Two-digit numbers (10-99) with `-` separator.

**Rules**:

- Lower BSP = dependency (others may rely on it)
- Same BSP = independent, can work in parallel
- Hyphen (`-`) separates BSP from slug: `NN-{slug}.{type}/`
- Use `@` for recursive insertion when no integer space: `20@54-audit.capability/`
- Never rename existing numbers—insert between them

**Examples**:

- `20-advanced-features.capability/` may depend on `10-core-cli.capability/`
- `40-test.story/` may depend on `30-build.story/`

</bsp_numbering>
