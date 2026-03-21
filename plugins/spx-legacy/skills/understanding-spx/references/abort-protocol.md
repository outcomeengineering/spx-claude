<overview>
Error handling and remediation guidance when required documents are missing.
</overview>

<when_to_abort>
ABORT immediately when ANY of these conditions are met:

1. **Work item not found**: Cannot locate work item in `spx/`
2. **Product guide missing**: `spx/CLAUDE.md` does not exist
3. **Spec file missing**: Work item spec file (`.capability.md`, `.feature.md`, `.story.md`) missing AND no PRD to create it from
4. **Multiple specs found**: Ambiguous - multiple spec files at same level

**Note**: PRD is optional. Missing PRD does NOT trigger abort.

</when_to_abort>

<abort_message_format>

```markdown
CONTEXT INGESTION FAILED

**Phase**: [Phase name and number]
**Missing Document**: [Document type]
**Expected Path**: [Exact path where file should exist]
**Reason**: [Why this document is required]

**Remediation**:
[Numbered steps to create the missing document]

**Cannot proceed with implementation until this document exists.**
```

</abort_message_format>

<abort_scenarios>

<scenario name="work_item_not_found">

**Trigger**: Phase 0 - Cannot locate work item

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 0 - Locate Work Item
**Missing Document**: Work item directory
**Expected Pattern**: spx/**/30-build.story/
**Reason**: Cannot proceed without identifying the work item location

**Remediation**:

1. Verify the work item exists: `ls -R spx/`
2. Check work item naming follows pattern: NN-{slug}.{type}/
3. If work item doesn't exist, create it first
4. Re-run `/understanding-specs` with correct work item identifier

**Cannot proceed with implementation until work item exists.**
```

</scenario>

<scenario name="product_guide_missing">

**Trigger**: Phase 1 - `spx/CLAUDE.md` not found

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 1 - Product-Wide Context
**Missing Document**: Product guide
**Expected Path**: spx/CLAUDE.md
**Reason**: Product guide is required for all projects (defines structure and navigation)

**Remediation**:

1. Invoke `/managing-specs` skill to create spx/ structure
2. Or manually create `spx/CLAUDE.md` with project structure documentation
3. Re-run `/understanding-specs` to verify

**Cannot proceed with implementation until product guide exists.**
```

</scenario>

<scenario name="capability_spec_missing">

**Trigger**: Phase 2 - Capability spec file not found

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 2 - Capability Context
**Missing Document**: Capability specification
**Expected Path**: spx/10-cli.capability/cli.capability.md
**Reason**: Capability spec defines E2E scenario and is required

**Remediation**:

1. Create capability spec file at expected path
2. Use template from `/managing-specs` skill: `templates/outcomes/capability-name.capability.md`
3. Fill in functional requirements and acceptance criteria
4. Re-run `/understanding-specs` to verify

**Cannot proceed with implementation until capability spec exists.**
```

</scenario>

<scenario name="capability_spec_missing_with_prd">

**Trigger**: Phase 2 - Capability spec not found BUT PRD exists

**This is an OFFER scenario, not an ABORT scenario.**

```markdown
SPEC FILE MISSING - OFFER TO CREATE

**Phase**: Phase 2 - Capability Context
**Missing Document**: Capability specification
**Expected Path**: spx/10-cli.capability/cli.capability.md
**Available Source**: spx/10-cli.capability/command-architecture.prd.md

**Found PRD but no capability.md - create spec from it?**

If user accepts:

1. Read the PRD to understand requirements
2. Use `/managing-specs` template: `templates/outcomes/capability-name.capability.md`
3. Create capability spec derived from PRD requirements
4. Continue with context ingestion

If user declines:

1. ABORT - cannot proceed without spec file
```

</scenario>

<scenario name="feature_spec_missing">

**Trigger**: Phase 3 - Feature spec file not found

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 3 - Feature Context
**Missing Document**: Feature specification
**Expected Path**: spx/.../20-commands.feature/commands.feature.md
**Reason**: Feature spec defines integration scenario and is required

**Remediation**:

1. Create feature spec file at expected path
2. Use template from `/managing-specs` skill: `templates/outcomes/feature-name.feature.md`
3. Fill in functional requirements and acceptance criteria
4. Re-run `/understanding-specs` to verify

**Cannot proceed with implementation until feature spec exists.**
```

</scenario>

<scenario name="story_spec_missing">

**Trigger**: Phase 4 - Story spec file not found

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 4 - Story Context
**Missing Document**: Story specification
**Expected Path**: spx/.../30-build.story/build.story.md
**Reason**: Story spec defines atomic implementation unit and is required

**Remediation**:

1. Create story spec file at expected path
2. Use template from `/managing-specs` skill: `templates/outcomes/story-name.story.md`
3. Fill in functional requirements and acceptance criteria
4. Re-run `/understanding-specs` to verify

**Cannot proceed with implementation until story spec exists.**
```

</scenario>

<scenario name="multiple_specs_found">

**Trigger**: Any phase - Multiple spec files at same level

```markdown
CONTEXT INGESTION FAILED

**Phase**: Phase 2 - Capability Context
**Problem**: Multiple spec files found
**Found Files**:

- spx/10-cli.capability/cli.capability.md
- spx/10-cli.capability/old-cli.capability.md
  **Reason**: Ambiguous - cannot determine which spec file is current

**Remediation**:

1. Remove or rename old/duplicate spec files
2. Keep only ONE spec file per work item: {slug}.{type}.md
3. Archive old versions outside spx/ if needed
4. Re-run `/understanding-specs` to verify

**Cannot proceed with implementation until ambiguity is resolved.**
```

</scenario>

</abort_scenarios>

<warning_scenarios>
These scenarios generate warnings but don't abort:

<scenario name="working_on_completed_item">

```markdown
WARNING: Working on completed work item

**Story**: 30-build.story
**Status**: All tests passing
**Risk**: This story is complete. Changes may require re-running tests.

**Recommendations**:

1. If this is new work, create a new story instead
2. If fixing a bug, create a new bug-fix story
3. If truly modifying this story, understand you'll need to re-commit outcomes

Proceeding with context ingestion...
```

</scenario>

<scenario name="no_product_decisions">

```text
Product Context Loaded

- spx/CLAUDE.md
- Product ADRs: 0 (none found - acceptable for new projects)
- Product PDRs: 0 (none found - acceptable for new projects)
```

</scenario>

<scenario name="no_capability_decisions">

```text
Capability Context Loaded: 10-cli.capability

- cli.capability.md
- command-architecture.prd.md
- Capability ADRs: 0 (none found - acceptable)
- Capability PDRs: 0 (none found - acceptable)
```

</scenario>

</warning_scenarios>

<success_path>
When all documents exist:

```markdown
# CONTEXT INGESTION COMPLETE

All required documents verified and read
Complete hierarchical context loaded
All architectural constraints understood

You may now proceed with implementation.
```

</success_path>

<prd_handling>
**PRD is an optional enrichment document.**

- Missing PRD does NOT cause abort
- If PRD exists, read it for additional context
- If spec file is missing but PRD exists at that level, offer to create spec from it

**Offer-to-create workflow**:

1. Detect missing spec file with available PRD
2. Prompt user: "Found PRD but no [spec].md - create spec from it?"
3. If accepted: Create spec using template and requirements document
4. If declined: Abort with spec missing error

</prd_handling>
