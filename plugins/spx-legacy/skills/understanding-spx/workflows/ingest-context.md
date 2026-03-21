<required_reading>
Read these reference files NOW:

1. `references/document-types.md` - Required documents at each level
2. `references/abort-protocol.md` - Error handling and remediation

</required_reading>

<process>
Execute these phases IN ORDER. ABORT immediately if any required document is missing.

<phase_0_locate>

**Goal**: Find exact path to work item in `spx/`

**Input formats accepted**:

- Full path: `10-cli.capability/20-commands.feature/30-build.story`
- Story name only: `30-build.story`
- Natural language: "build command story"

**Actions**:

```bash
# Search for work item (try multiple patterns)
Glob: "spx/**/*-{slug}.story/"
Glob: "spx/**/*-{slug}.feature/"
Glob: "spx/**/*-{slug}.capability/"

# Extract work item details
# Determine level: CAPABILITY, FEATURE, or STORY
# Parse BSP number and slug from path
```

**Abort if**: Work item path not found

**Output**:

```text
Work Item Located
  Path: spx/10-cli.capability/20-commands.feature/30-build.story
  Level: STORY
  BSP: 30
  Slug: build
```

</phase_0_locate>

<phase_1_product>

**Goal**: Load product-wide constraints and guidance

**Required Documents**:

- `spx/CLAUDE.md` MUST EXIST
- `spx/NN-*.adr.md` May not exist for new projects

**Actions**:

```bash
# Read project guide
Read: spx/CLAUDE.md

# Find and read all product ADRs and PDRs
Glob: "spx/*-*.adr.md"
Glob: "spx/*-*.pdr.md"
# For each decision record found:
Read: [decision record path]
```

**⚠️ Read EVERY file returned by the globs above. Do not skip any based on perceived relevance. Decision records contain cross-cutting constraints that may not be obvious from the title. If the glob returns 15 files, you read 15 files — no exceptions.**

**Verification**: Count the files returned by the globs. Count the files you actually read. These numbers must match. If they don't, go back and read the ones you skipped.

**Abort if**: `spx/CLAUDE.md` missing

**Strict Mode Check**: None (product ADRs/PDRs are truly optional)

**Output**:

```text
Product Context Loaded
  - spx/CLAUDE.md
  - Product ADRs: 3 found, 3 read ✓
    - [Type Safety](21-type-safety.adr.md)
    - [Testing Strategy](37-testing-strategy.adr.md)
    - [CLI Framework](54-cli-framework.adr.md)
  - Product PDRs: 1 found, 1 read ✓
    - [Simulation Lifecycle](10-simulation-lifecycle.pdr.md)
```

</phase_1_product>

<phase_2_capability>

**Goal**: Load capability specification, requirements, and decisions

**Required Documents**:

- `{capability-path}/{slug}.capability.md` MUST EXIST
- `{capability-path}/*.prd.md` OPTIONAL (read if present)
- `{capability-path}/*-*.adr.md` May not exist
- `{capability-path}/*-*.pdr.md` May not exist

**Actions**:

```bash
# Read capability spec
Glob: "{capability-path}/*.capability.md"
# Verify exactly one file found
Read: {capability-path}/{slug}.capability.md

# Read PRD if present (optional enrichment)
Glob: "{capability-path}/*.prd.md"
# If found:
Read: [PRD path]
# If not found:
# Continue without PRD (it's optional)

# Read capability ADRs and PDRs (interleaved)
Glob: "{capability-path}/*-*.adr.md"
Glob: "{capability-path}/*-*.pdr.md"
# For each decision record found:
Read: [decision record path]
```

**⚠️ Read EVERY ADR and PDR returned by the globs. Do not filter by perceived relevance — constraints are cross-cutting and titles are not reliable indicators of applicability.**

**Verification**: Glob count must equal read count. If not, go back.

**Abort if**:

- Capability spec missing (but PRD exists -> offer to create spec from PRD)
- Multiple capability specs found (ambiguous)

**Offer to create spec if**:

- Capability spec missing BUT PRD exists at this level
- Prompt: "Found PRD but no capability.md - create spec from it?"

**Enumerate Features (ALWAYS)**:

```bash
# CRITICAL: Use ls or find for directories, NOT Glob
ls -d {capability-path}/*.feature/ 2>/dev/null || echo "No features found"
```

**Output**:

```text
Capability Context Loaded: 10-cli.capability
  - cli.capability.md
  - command-architecture.prd.md (optional, found)
  - Capability ADRs: 2 found, 2 read ✓
    - [Commander Pattern](21-commander-pattern.adr.md)
    - [Config Loading](37-config-loading.adr.md)
  - Capability PDRs: 0
  - Features: 2
    - 20-commands.feature/
    - 37-plugins.feature/
```

</phase_2_capability>

<phase_3_feature>

**Goal**: Load feature specification and decisions

**Skip if**: Working on capability-level (no parent feature)

**Required Documents**:

- `{feature-path}/{slug}.feature.md` MUST EXIST
- `{feature-path}/*-*.adr.md` May not exist
- `{feature-path}/*-*.pdr.md` May not exist

**Actions**:

```bash
# Read feature spec
Glob: "{feature-path}/*.feature.md"
# Verify exactly one file found
Read: {feature-path}/{slug}.feature.md

# Read feature ADRs and PDRs (interleaved)
Glob: "{feature-path}/*-*.adr.md"
Glob: "{feature-path}/*-*.pdr.md"
# For each decision record found:
Read: [decision record path]
```

**⚠️ Read EVERY ADR and PDR returned by the globs. Do not filter by perceived relevance.**

**Verification**: Glob count must equal read count. If not, go back.

**Abort if**:

- Feature spec missing
- Multiple feature specs found (ambiguous)

**Note**: Technical details belong in feature.md. No separate TRD documents.

**Enumerate Stories (ALWAYS, even if not descending to story level)**:

```bash
# CRITICAL: Use ls to find directories, NOT Glob
# Glob matches files, not directories - story directories won't appear!
ls -d {feature-path}/*.story/ 2>/dev/null || echo "No stories found"

# Alternative: find command
find {feature-path} -maxdepth 1 -type d -name "*.story"
```

**If no stories found**: Feature is NOT ready for implementation. Recommend decomposition using `/decomposing-feature-to-stories`.

**Output**:

```text
Feature Context Loaded: 20-commands.feature
  - commands.feature.md
  - Feature ADRs: 1 found, 1 read ✓
    - [Subcommand Structure](21-subcommand-structure.adr.md)
  - Feature PDRs: 0
  - Stories: 3
    - 21-parse-flags.story/
    - 37-execute-command.story/
    - 54-output-format.story/
```

</phase_3_feature>

<phase_4_story>

**Goal**: Load story specification

**Skip if**: Working on feature-level or capability-level (no story)

**Required Documents**:

- `{story-path}/{slug}.story.md` MUST EXIST
- `{story-path}/tests/` directory Should exist for in-progress work

**Actions**:

```bash
# Read story spec
Glob: "{story-path}/*.story.md"
# Verify exactly one file found
Read: {story-path}/{slug}.story.md

# Check for tests/ directory to understand coverage
```

**Abort if**:

- Story spec missing
- Multiple story specs found (ambiguous)

**Output**:

```text
Story Context Loaded: 30-build.story
  - build.story.md
  - tests/ directory: exists
```

</phase_4_story>

<phase_5_summary>

**Goal**: Confirm complete context loaded and provide actionable summary

**Generate the following summary** (adapt counts and paths to actual project):

````markdown
# CONTEXT INGESTION COMPLETE

## Work Item

- **Level**: Story
- **Path**: spx/10-cli.capability/20-commands.feature/30-build.story
- **BSP**: 30 (story), 20 (feature), 10 (capability)

## Documents Loaded

### Product Level

- **Guide**: spx/CLAUDE.md
- **ADRs**: 3 found, 3 read ✓
  - [Type Safety](21-type-safety.adr.md)
  - [Testing Strategy](37-testing-strategy.adr.md)
  - [CLI Framework](54-cli-framework.adr.md)
- **PDRs**: 1 found, 1 read ✓
  - [Simulation Lifecycle](10-simulation-lifecycle.pdr.md)

### Capability Level: cli

- **Spec**: 10-cli.capability/cli.capability.md
- **PRD**: 10-cli.capability/command-architecture.prd.md
- **ADRs**: 2 found, 2 read ✓
  - [Commander Pattern](21-commander-pattern.adr.md)
  - [Config Loading](37-config-loading.adr.md)
- **PDRs**: 0 documents

### Feature Level: commands

- **Spec**: 20-commands.feature/commands.feature.md
- **ADRs**: 1 found, 1 read ✓
  - [Subcommand Structure](21-subcommand-structure.adr.md)
- **PDRs**: 0 documents

### Story Level: build

- **Spec**: 30-build.story/build.story.md
- **Tests**: tests/ directory exists

## Constraints Summary

**Total ADRs Applicable**: 6

- Product-wide: 3
- Capability-scoped: 2
- Feature-scoped: 1
- Story-specific: 0 (stories inherit parent ADRs)

**Total PDRs Applicable**: 1

- Product-wide: 1
- Capability-scoped: 0
- Feature-scoped: 0
- Story-specific: 0 (stories inherit parent PDRs)

**Test Location**: `spx/.../30-build.story/tests/`

## Hierarchy Chain

```text
Product (claude)
└── Capability 10: cli
    └── Feature 20: commands
        └── Story 30: build ← YOU ARE HERE
```

## Ready for Implementation

All required documents verified and read.
Complete hierarchical context loaded.
All architectural constraints (ADRs) and product decisions (PDRs) understood.

You may now proceed with implementation.
````

</phase_5_summary>

</process>

<failure_modes>
Failures from actual usage:

**Failure 1: Agent skipped most ADRs/PDRs based on title relevance**

- What happened: Agent globbed 17 product-level PDRs and ADRs but only read 2 whose titles seemed relevant to the immediate question, skipping 15 documents
- Why it failed: Agent optimized for efficiency over completeness — the pseudo-code comment `# For each decision record found:` inside a code fence didn't carry imperative weight
- How to avoid: The verification gates ("glob count must equal read count") enforce exhaustive reading. The answer to the user's question was in one of the 15 skipped documents.

**Failure 2: Agent treated "For each" loop as illustrative, not mandatory**

- What happened: The instruction to read each file was a comment inside a bash code block. Agent interpreted it as an example pattern rather than a strict directive.
- Why it failed: Comments inside code fences are read as documentation, not commands. Agents distinguish between "here's how you could do it" and "you MUST do this."
- How to avoid: Critical instructions must appear as bold text OUTSIDE code fences, not as comments inside them.

</failure_modes>

<success_criteria>
Workflow is complete when:

- [ ] All phases executed in order (0 through 5)
- [ ] Every required document located and read
- [ ] All ADRs and PDRs at all levels read and listed
- [ ] Glob count matches read count at every level (verify: output shows "N found, N read ✓")
- [ ] Context summary generated with complete document list
- [ ] Clear indication that implementation may proceed
- [ ] No ABORT conditions triggered (or appropriate error shown)

</success_criteria>
