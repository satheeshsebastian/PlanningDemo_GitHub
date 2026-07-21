# New Feature Planning Workflow

Creates Business Requirement Document (BRD) and user stories for brand new features.

**When to Use**: Auto-routed by Stage 0 when enhancement detector confidence < 40% (no existing artifacts found)

**Design Pattern**: See `planning-workflow-master.md` for auto-detection logic

---

## Execution Sequence

### Stage 1: BRD Generation ✓

**Skill**: `brd-generator` (`.github/skills/brd-generator/SKILL.md`)

**Why Here**: Routed from master workflow Stage 0 when confidence < 40%

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

### Stage 6: Execution Report Generation (MANDATORY & AUTOMATIC)
**Implementation**: Auto-generate after Stage 5 completes (no agent needed)

**When to Execute**: 
- Automatically triggered after github-issue-uploader completes (Stage 5)
- Run at end of EVERY planning workflow (both new-feature-planning and enhancement-planning)
- No user approval required

**Input**: Metadata from all previous stages
- Stage start/end times
- File paths and sizes of artifacts created
- Token usage per stage (if tracked)
- User approvals/decisions made
- GitHub issues created (#s)
- Test count and coverage metrics

**Output**: `features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md`

**Report Contents**:
1. **Workflow Overview**: Total stages, completion status, success rate
2. **Stage-by-Stage Details** (6 sections):
   - Enhancement Detection (confidence, decision, route taken)
   - BRD Generation (brainstorming sessions, requirements extracted)
   - User Stories (stories created, points, INVEST validation)
   - Functional Tests (test count, coverage %, automation rate)
   - GitHub Integration (issues created, labels, traceability)
   - Report Generation (this stage)
3. **Aggregated Metrics**:
   - Total execution duration
   - Artifacts count & sizes
   - Quality metrics (100% requirement coverage, 100% INVEST, etc.)
4. **Success Criteria Achievement**: Checklist of all completion gates
5. **Next Steps & Recommendations**: 3-5 week outlook with action items
6. **Appendix**: Workflow configuration, rules applied, decisions made

**Quality Checks**:
- ✅ Validate all artifacts exist (ls -la on all created files)
- ✅ Count total files created (should match expected)
- ✅ Verify story points sum (MUST+SHOULD+COULD = total)
- ✅ Check test coverage (100% of AC covered)
- ✅ Confirm GitHub sync (all issues created)
- ✅ Validate traceability (BRD→Stories→Tests→GitHub)

**Approval Gate**: None (auto-generated for audit trail)

## Artifacts Checklist

```
✓ features/brd/[slug]-v1.0.md
✓ features/brd/[slug]-assumptions.md
✓ features/user-stories/[slug-*.md] (individual files)
✓ features/user-stories/story-map-[slug].md
✓ features/user-stories/story-traceability.json
✓ features/test-cases/[slug]-test-cases.md (if enabled)
✓ features/github-sync/[slug]-issue-map.json (if enabled)
✓ features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md (auto-generated)
```

## Next: See individual skill files for detailed execution logic
