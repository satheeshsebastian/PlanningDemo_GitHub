# Planning Workflow - Smart Auto-Detection & Routing

Intelligently detects whether a requirement is for a new feature or enhancement, then routes to the appropriate workflow automatically.

**Design Pattern**: See `WORKFLOW-AUTO-DETECTION-DESIGN.md` for detailed logic

## Execution Sequence

### Stage 0: Enhancement Detection (AUTOMATIC - No User Input)
**Responsibility**: Auto-detect feature type and route to appropriate workflow

**Logic**:
1. Extract feature slug from user input
2. Search for existing artifacts:
   - BRD files in `features/brd/[slug]*.md` (+50% confidence)
   - User stories in `features/user-stories/[slug]*.md` (+30% confidence)
   - Test cases in `features/test-cases/[slug]*.md` (+20% confidence)
   - GitHub issues for feature (+10% confidence)
   - Keyword matching in artifacts (+5% confidence)

3. Calculate total confidence score
4. Route decision:
   - **≥ 70% confidence**: AUTO-ROUTE to `enhancement-planning` workflow
   - **< 40% confidence**: AUTO-ROUTE to `normal-planning` workflow (THIS WORKFLOW)
   - **40-70% confidence**: ASK USER ("Is this updating an existing feature?")

**Output**: 
- Detection result logged to execution report
- Workflow route determined
- No user interaction required (except ambiguous cases)

**Examples**:
- Input: "Smart Coupon System - Add push notifications"
  - Detection: Existing BRD + 7 stories + 114 tests found
  - Confidence: 85% → AUTO-ROUTE to enhancement-planning ✓

- Input: "New Referral Program with rewards"
  - Detection: No existing artifacts found
  - Confidence: 5% → AUTO-ROUTE to normal-planning ✓

- Input: "Loyalty benefits updates"
  - Detection: Partial match (30% match on stories)
  - Confidence: 40% → ASK USER ⚠️

---

## Normal Planning Workflow (New Features)

Creates BRD and user stories for brand new features.

**When to Use**: Enhancement detector confidence < 40% (automatic detection)

## Execution Sequence

### Stage 1: BRD Generation ✓
Routed from Stage 0 auto-detection (confidence < 40%)

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

### Stage 6: Execution Report Generation (MANDATORY & AUTOMATIC)
**Implementation**: Auto-generate after Stage 5 completes (no agent needed)

**When to Execute**: 
- Automatically triggered after github-issue-uploader completes (Stage 5)
- Run at end of EVERY planning workflow (both normal-planning and enhancement-planning)
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
