# Normal Planning Workflow (New Features)

Creates BRD and user stories for brand new features.

**When to Use**: Enhancement detector confidence < 40%

## Execution Sequence

### Stage 1: Enhancement Detection ✓
Complete. Result: NO MATCH

### Stage 2: BRD Generation
**Skill**: `brd-generator` (`.github/skills/brd-generator/SKILL.md`)

**Input**: Raw requirement + user responses
**Output**: `features/brd/[slug]-v1.0.md`, `features/brd/[slug]-assumptions.md`
**Approval Gate**: Stakeholder sign-off required

### Stage 3: User Story Building
**Skill**: `user-story-builder` (`.github/skills/user-story-builder/SKILL.md`)

**Input**: BRD v1.0 + assumptions file
**Output**: 
- Individual story files: `features/user-stories/[slug-*.md]`
- `features/user-stories/story-map-[slug].md`
- `features/user-stories/story-traceability.json`
**Approval Gate**: Product owner review required

### Stage 4: Functional Tests (Optional)
**Skill**: `functional-test-writer` (`.github/skills/functional-test-writer/SKILL.md`)

**Input**: User stories + acceptance criteria
**Execution**: Parallel sub-agents (1 per story + 1 common)
**Output**: 
- `features/test-cases/smart-coupon-system-common-tests.md` (integration)
- `features/test-cases/smart-coupon-system-SC-001-tests.md` (story 1)
- `features/test-cases/smart-coupon-system-SC-002-tests.md` (story 2)
- ... (one per story, 11 total)
- `features/test-cases/smart-coupon-system-test-index.md` (index)

### Stage 5: GitHub Integration (Optional)
**Skill**: `github-issue-uploader` (`.github/skills/github-issue-uploader/SKILL.md`)

**Input**: User stories + tests + traceability
**Output**: `features/github-sync/[slug]-issue-map.json` + GitHub issues

### Stage 6: Completion Report
Summary of all artifacts created.

## Artifacts Checklist

```
✓ features/brd/[slug]-v1.0.md
✓ features/brd/[slug]-assumptions.md
✓ features/user-stories/[slug-*.md] (individual files)
✓ features/user-stories/story-map-[slug].md
✓ features/user-stories/story-traceability.json
✓ features/test-cases/[slug]-test-cases.md (if enabled)
✓ features/github-sync/[slug]-issue-map.json (if enabled)
```

## Next: See individual skill files for detailed execution logic
