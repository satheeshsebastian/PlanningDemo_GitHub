# Enhancement Planning Workflow (Existing Features)

Updates existing BRD and modifies/creates related user stories.

**When to Use**: Enhancement detector confidence > 70%

## Execution Sequence

### Stage 1: Enhancement Detection ✓
Complete. Result: MATCH FOUND

### Stage 2: Enhancement BRD Modifier
**Skill**: `enhancement-modifier` (`.github/skills/enhancement-modifier/SKILL.md`)

**Input**: Existing BRD + enhancement requirements + user responses
**Output**: 
- `features/brd/[slug]-v[N+1].md` (version bumped)
- `features/brd/[slug]-assumptions.md` (updated)
- `features/CHANGELOG.md` (new)
**No Approval Gate** (stories need approval)

### Stage 3: Enhancement Story Updater
**Skill**: `enhancement-story-updater` (`.github/skills/enhancement-story-updater/SKILL.md`)

**Input**: Updated BRD + existing stories + changelog
**Output**:
- Modified story files: `features/user-stories/[slug-*.md]` (updated)
- New story files: `features/user-stories/[new-slug-*.md]` (created)
- `features/user-stories/story-map-[slug].md` (updated)
- `features/user-stories/story-traceability.json` (updated)
**Approval Gate**: Product owner review of modified/new stories

### Stage 4: Functional Tests (Optional)
**Skill**: `functional-test-writer` (`.github/skills/functional-test-writer/SKILL.md`)

**Input**: Modified/new stories + acceptance criteria
**Execution**: Parallel sub-agents (1 per modified/new story + 1 common)
**Output**: Updated test case files
- `features/test-cases/smart-coupon-system-common-tests.md` (updated)
- `features/test-cases/smart-coupon-system-SC-[modified]-tests.md` (updated)
- `features/test-cases/smart-coupon-system-SC-[new]-tests.md` (created)
- `features/test-cases/smart-coupon-system-test-index.md` (updated)

### Stage 5: GitHub Integration (Optional)
**Skill**: `github-issue-uploader` (`.github/skills/github-issue-uploader/SKILL.md`)

**Input**: Updated stories + tests + traceability
**Output**: 
- Updated existing GitHub issues
- Created new GitHub issues
- `features/github-sync/[slug]-issue-map.json` (updated)

### Stage 6: Completion Report
Summary of all modifications and new artifacts.

## Artifacts Checklist

```
✓ features/brd/[slug]-v[N+1].md (version bumped)
✓ features/brd/[slug]-assumptions.md (updated)
✓ features/CHANGELOG.md (new file)
✓ features/user-stories/[modified-slug].md (updated)
✓ features/user-stories/[new-slug-*.md] (created)
✓ features/user-stories/story-map-[slug].md (updated)
✓ features/user-stories/story-traceability.json (updated)
✓ features/test-cases/[slug]-test-cases.md (if updated)
✓ features/github-sync/[slug]-issue-map.json (if updated)
```

## Next: See individual skill files for detailed execution logic
