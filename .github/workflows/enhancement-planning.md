# Enhancement Planning Workflow (Existing Features)

Updates existing BRD and modifies/creates related user stories.

**When to Use**: Enhancement detector confidence ≥ 70% (automatic detection)

**Design Pattern**: See `WORKFLOW-AUTO-DETECTION-DESIGN.md` for detection logic

## Execution Sequence

### Stage 0: Enhancement Detection (AUTOMATIC - No User Input)
**Responsibility**: Auto-detect feature type and route to appropriate workflow

**Logic**:
1. Extract feature slug from user input
2. Search for existing artifacts (BRD, stories, tests, GitHub issues)
3. Calculate confidence score
4. Route decision:
   - **≥ 70% confidence**: AUTO-ROUTE to this workflow (enhancement-planning)
   - **< 40% confidence**: AUTO-ROUTE to normal-planning
   - **40-70% confidence**: ASK USER for clarification

**Output**: Detection result logged to execution report

**Example**:
- Input: "Smart Coupon System - Add push notifications"
- Detection: Existing BRD + 7 stories + 114 tests found
- Confidence: 85% → AUTO-ROUTE to enhancement-planning ✓

---

## Enhancement Planning Workflow (Existing Features)

Updates existing BRD and modifies/creates related user stories.

**Status**: Routed from Stage 0 auto-detection (confidence ≥ 70%)

## Execution Sequence

### Stage 1: Enhancement Detection ✓
Complete. Result: MATCH FOUND (auto-detected in Stage 0)

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

### Stage 6: Execution Report Generation (MANDATORY & AUTOMATIC)
**Implementation**: Auto-generate after Stage 5 completes (no agent needed)

**When to Execute**: 
- Automatically triggered after github-issue-uploader completes (Stage 5)
- Run at end of EVERY planning workflow (both normal-planning and enhancement-planning)
- No user approval required

**Input**: Metadata from all previous stages
- Stage start/end times
- File paths and sizes of artifacts created/modified
- Token usage per stage (if tracked)
- User approvals/decisions made
- GitHub issues created/updated (#s)
- Test count and coverage metrics
- Enhancement detection results (confidence, rationale)
- BRD version bump (v1.0 → v1.1, etc.)
- Modified stories count, new stories count
- Deprecated stories count

**Output**: `features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md`

**Report Contents**:
1. **Workflow Overview**: Total stages, completion status, success rate
2. **Stage-by-Stage Details** (6 sections):
   - Enhancement Detection (confidence, existing artifacts found, decision rationale)
   - BRD Modification (version bump, sections changed, new assumptions)
   - Story Updates (modified count, new count, deprecated count, point changes)
   - Functional Tests (new tests added, updated tests, coverage impact)
   - GitHub Integration (issues created/updated, existing issues affected)
   - Report Generation (this stage)
3. **Aggregated Metrics**:
   - Total execution duration
   - Artifacts created/modified/deprecated counts
   - Quality metrics (coverage maintained, dependencies updated)
   - Impact analysis (backward compatibility, migration path)
4. **Enhancement Summary**: What changed, why, impact on existing features
5. **Next Steps & Recommendations**: Rollout plan, compatibility notes
6. **Appendix**: Workflow configuration, rules applied, decisions made

**Quality Checks**:
- ✅ Validate all modified files exist
- ✅ Verify version bumps on BRD (v[N] → v[N+1])
- ✅ Confirm story mapping updates
- ✅ Check test coverage maintained or improved
- ✅ Validate GitHub issue updates (comments added, linked)
- ✅ Confirm backward compatibility

**Approval Gate**: None (auto-generated for audit trail)

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
✓ features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md (auto-generated)
```

## Next: See individual skill files for detailed execution logic
