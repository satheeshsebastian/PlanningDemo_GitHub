# Enhancement Planning Workflow (Existing Features)

## Overview
Updates an existing BRD and modifies/creates related user stories when **enhancing an existing feature**.

**When to Use**: Enhancement detector finds MATCH with confidence > 70%

---

## Workflow Steps

### Stage 1: Enhancement Detection ✓
**Status**: Already completed in planning-workflow.md

- Result: MATCH FOUND
- Matched artifact: `features/brd/[existing-slug]-v[N].md`
- Confidence: > 70%
- User decision: Proceed with enhancement modification

---

### Stage 2: Enhancement BRD Modifier

**Skill**: `enhancement-modifier` (`.github/skills/enhancement-modifier/SKILL.md`)

**Input**:
- Existing BRD file path (from enhancement-detector)
- New enhancement requirements from user
- Current version number (e.g., v2.0)

**Process**:
1. **Load Existing BRD**
   - Read complete existing BRD document
   - Extract current version, sections, and structure
   - Identify sections affected by enhancement

2. **Ask Clarification** (Max 1 question, if needed)
   - Only if enhancement scope is ambiguous
   - Example: "Should SMS include in-app notifications, or SMS only?"
   - Skip if scope is clear from requirements

3. **Plan Updates**
   - Identify which sections will be modified
   - Determine version bump strategy

4. **Version Bump Decision**
   - **MINOR (v2.0 → v2.1)**: Backward-compatible extensions
     - Adding new features/stories
     - Extending existing capabilities
     - No breaking changes to user flows
     - Example: "Add SMS to existing coupon distribution"
   
   - **MAJOR (v2.0 → v3.0)**: Breaking changes
     - Structural changes to core flow
     - Significant requirement rewrites
     - Breaking changes to user flows
     - Example: "Change coupon pricing model from % to fixed amount"

5. **Merge New Requirements**
   - Keep ALL existing content UNCHANGED
   - ADD new requirements (don't replace)
   - Use clear separators:
     ```
     ### Original Scope
     - Existing requirement 1
     - Existing requirement 2
     
     ### NEW in v2.1
     - New requirement A
     - New requirement B
     ```
   - Update section headers: "## Section Name (Updated in v2.1)"

6. **Update Assumptions**
   - Read existing assumptions file
   - Add new ASSUMPTION-XXX entries (continue numbering)
   - Link new assumptions to affected sections
   - Mark obsolete assumptions as DEPRECATED
   - Update assumptions file version

7. **Create CHANGELOG**
   - File: `features/CHANGELOG.md`
   - Document version bump (v2.0 → v2.1)
   - List all changes: Added, Changed, Fixed, Removed
   - Note affected components
   - Track impact on existing stories
   ```
   ## [v2.1] - 2026-06-10
   
   ### Added (Enhancement)
   - SMS distribution channel for coupons
   - Real-time notification tracking
   
   ### Changed
   - Updated assumption on gateway integration
   
   ### Dependencies Affected
   - Story: [SC-001] may need expansion
   - Story: [SC-005] creates new blocker
   ```

**Outputs**:
```
features/brd/
├── [slug]-v2.1.md              ← Updated BRD (version bumped)
└── [slug]-assumptions.md       ← Updated assumptions

features/
└── CHANGELOG.md                ← Version history
```

**No Approval Gate**: BRD update is direct (story updates need approval)

---

### Stage 3: Enhancement Story Updater

**Skill**: `enhancement-story-updater` (`.github/skills/enhancement-story-updater/SKILL.md`)

**Input**:
- Updated BRD v2.1 from Stage 2
- Existing user stories directory
- Changelog from Stage 2 (describing what changed)

**Process**:
1. **Load Existing Stories**
   - Read story-map file (all existing stories)
   - Read story-traceability.json
   - Read sample story files to understand current format

2. **Map Enhancement Impact to Stories**
   - For each BRD change, identify which stories are affected
   - **MODIFY**: Stories with changed acceptance criteria
   - **NEW**: Stories for completely new capabilities
   - **LINK**: Stories with new dependencies

3. **Modify Existing Stories**
   For each story that needs updating:
   
   a. **Read the existing story file**
   
   b. **Expand Acceptance Criteria** (ADD, don't replace)
      ```
      ### Acceptance Criteria (Updated in v2.1)
      
      **Original AC** (from v1.0):
      - Given user is logged in...
      - When user views coupon...
      - Then coupon details appear...
      
      **NEW in v2.1**:
      - Given user opted into SMS...
      - When new coupon issued...
      - Then SMS sent to user phone...
      ```
   
   c. **Update Story Points** (if scope significantly changed)
      - If +1-2 AC: Keep points same
      - If +3+ AC or major scope: Reestimate and document
      - Show: "Updated v2.1: 5 pts → 8 pts (SMS integration)"
   
   d. **Update Dependencies**
      - Add any new blockers
      - Note new blocked-by relationships
   
   e. **Update Assumptions**
      - Add references to new assumptions
      - Update "depends on" section
   
   f. **Update MoSCoW** (if priority changed)
      - Justify any priority changes
   
   g. **Add Version Marker**
      - "Last Updated: v2.1 - 2026-06-10"
      - Mark all new content with "[NEW in v2.1]"

4. **Create New Stories**
   For each brand-new capability:
   
   a. **Create new story file**: `features/user-stories/[new-slug].md`
   
   b. **Assign Story ID**
      - Continue from last ID (e.g., SC-014, SC-015 if last was SC-013)
   
   c. **Include all required sections**:
      - Story ID & statement
      - Acceptance criteria (BDD format)
      - Technical requirements
      - Dependencies (link to modified stories)
      - Story points with breakdown
      - MoSCoW classification
      - Assumption references
      - INVEST validation
      - Definition of Done
   
   d. **Mark as NEW**: "[NEW in v2.1]" throughout

5. **Update Story Map**
   - Add new stories to matrix
   - Update existing story entries if AC changed
   - Update dependency graph
   - Recalculate total points
   - Add "Updated in v2.1" note
   - Show story count changes (e.g., "13 → 15 stories")

6. **Update Story Traceability**
   - Add entries for new stories
   - Update BRD mappings for modified stories
   - Link new stories to new BRD sections
   - Update assumption dependencies
   
   Example:
   ```json
   {
     "brd_version": "2.1",
     "updated": "2026-06-10T17:00:00Z",
     "stories": [
       {
         "story_id": "SC-014",
         "slug": "user-can-receive-coupons-via-sms",
         "moscow": "SHOULD",
         "points": 5,
         "new_in": "v2.1",
         "depends_on_assumptions": ["ASSUMPTION-007"],
         "traceability": {
           "req_id": ["REQ-NEW-001"]
         }
       },
       {
         "story_id": "SC-005",
         "slug": "user-can-receive-coupons-via-email",
         "moscow": "MUST",
         "points": 5,
         "modified_in": "v2.1",
         "ac_expanded": true,
         "depends_on_assumptions": ["ASSUMPTION-001"]
       }
     ]
   }
   ```

7. **Validate Changes**
   - ✅ All new stories have unique IDs
   - ✅ All stories link to BRD sections
   - ✅ No circular dependencies
   - ✅ INVEST check passed
   - ✅ MoSCoW justified

**Outputs**:
```
features/user-stories/
├── [existing-slug].md           ← MODIFIED (updated AC)
├── [new-slug-1].md              ← NEW (created)
├── [new-slug-2].md              ← NEW (created)
├── story-map-[slug].md          ← UPDATED (new totals)
└── story-traceability.json      ← UPDATED (mappings)
```

**Approval Gate 2**: ✅ Product owner review of modified/new stories

---

### Stage 4: Functional Test Case Writing (Optional)

**Skill**: `functional-test-writer` (`.github/skills/functional-test-writer/SKILL.md`)

**Input**:
- Modified and new user stories from Stage 3
- Updated story-traceability.json
- Test coverage requirements

**Process**:
1. **Analyze Modified Stories**
   - Focus on new acceptance criteria
   - Generate tests for new/changed AC
   - Keep existing test cases (regression)

2. **Analyze New Stories**
   - Generate complete test case coverage
   - Happy path, negative, and edge cases

3. **Generate Test Cases**
   - BDD format (Given/When/Then)
   - Trace to modified/new AC
   - Include regression notes for modified stories

4. **Update Test Documentation**
   - Append new test cases to existing file (don't replace)
   - Mark new tests with "[NEW in v2.1]"
   - Mark modified tests with "[UPDATED in v2.1]"
   - Include story traceability

**Outputs**:
```
features/test-cases/
└── [slug]-test-cases.md        ← UPDATED with new tests
```

---

### Stage 5: GitHub Integration (Optional)

**Skill**: `github-issue-uploader` (`.github/skills/github-issue-uploader/SKILL.md`)

**Input**:
- Modified stories from Stage 3
- New stories from Stage 3
- Test cases from Stage 4 (if updated)
- Previous issue-map.json (to find existing issues)

**Process**:
1. **Update Existing Story Issues**
   - Find existing GitHub issues for modified stories
   - Update issue description with expanded AC
   - Add comment: "Updated in v2.1: [changes]"
   - Link to CHANGELOG.md

2. **Create New Story Issues**
   - Create GitHub issues for new stories
   - Use same format as original stories
   - Link to modified stories as blockers/related

3. **Update Test Issues** (if tests updated)
   - Add new test cases to existing test issue
   - Link to story issues

4. **Update Dependency Links**
   - Refresh issue dependencies
   - Add new blocker relationships

5. **Update Tracking Artifact**
   - Append to issue-map.json
   - Show issue #s for modified and new stories
   - Add v2.1 update timestamp

**Outputs**:
```
features/github-sync/
└── [slug]-issue-map.json       ← UPDATED with new issues & mappings
```

---

### Stage 6: Completion & Summary

**Process**:
1. **Generate Execution Report**
   - BRD updated: v2.0 → v2.1
   - Stories modified: [count], [points impact]
   - Stories created: [count], [points added]
   - Assumptions updated: [new added]
   - Test cases updated: [count]
   - GitHub issues: [count updated], [count new]

2. **Display Summary**
   ```
   ✅ ENHANCEMENT PLANNING COMPLETE (v2.1)
   
   📄 BRD Updated:
      - Version: v2.0 → v2.1 (MINOR bump)
      - features/brd/[slug]-v2.1.md
      - features/brd/[slug]-assumptions.md
      - features/CHANGELOG.md
   
   📋 User Stories:
      - Modified: 2 stories (AC expanded)
      - Created: 2 new stories
      - Total Stories: 13 → 15
      - Total Points: 73 → 78 (+5)
   
   🧪 Test Cases Updated: +8 new tests
      - features/test-cases/[slug]-test-cases.md
   
   🔗 GitHub Issues:
      - Updated: 2 issues
      - Created: 2 new issues
      - features/github-sync/[slug]-issue-map.json
   
   📝 What Changed:
      - See features/CHANGELOG.md for detailed changes
      - See story files for [NEW in v2.1] markers
   
   Next Steps:
   - Review modified stories with product owner
   - Plan sprint capacity for new stories
   - Update development timeline
   - Begin work on new capabilities
   ```

3. **Confirm Ready for Development**

---

## Integration with planning-workflow.md

This workflow represents **Stages 2-6** of the master planning workflow (enhancement path):

```
planning-workflow.md (Master)
  └─ Stage 1: Enhancement Detection
      ├─ NO MATCH (< 40%) → normal-planning.md
      │   └─ (Create brand new feature)
      │
      └─ MATCH (> 70%) → enhancement-planning.md (THIS FILE)
          ├─ Stage 2: enhancement-modifier
          ├─ Stage 3: enhancement-story-updater
          ├─ Stage 4: functional-test-writer (optional)
          ├─ Stage 5: github-issue-uploader (optional)
          └─ Stage 6: Completion
```

---

## Key Principles

✅ **Preserve History**: Keep all original content, add new sections
✅ **Clear Versioning**: MINOR for extensions, MAJOR for breaking changes
✅ **Backward Compatibility**: Ensure existing stories still make sense
✅ **Maintain Traceability**: Link all new requirements to BRD and stories
✅ **Document Changes**: CHANGELOG.md tracks all modifications
✅ **Approval Gates**: Sign-off required for modified/new stories
✅ **Update Artifacts**: ALL artifacts (map, traceability, GitHub) updated together

---

## Time Estimate

- **Stage 2 (BRD Modification)**: 5-10 minutes (skill autonomous)
- **Stage 3 (Story Updates)**: 10-15 minutes (skill autonomous)
- **Stage 4 (Tests)**: 5-10 minutes (optional)
- **Stage 5 (GitHub)**: 3-5 minutes (optional)
- **Total**: 23-40 minutes (depending on enhancement scope and options enabled)

---

## File Checklist

After completing this workflow, verify these files:

```
✓ features/brd/[slug]-v2.1.md (version bumped)
✓ features/brd/[slug]-assumptions.md (updated)
✓ features/CHANGELOG.md (new, version history)
✓ features/user-stories/[modified-story].md (updated)
✓ features/user-stories/[new-story-1].md (created)
✓ features/user-stories/[new-story-2].md (created)
✓ features/user-stories/story-map-[slug].md (updated totals)
✓ features/user-stories/story-traceability.json (updated)
✓ features/test-cases/[slug]-test-cases.md (if Stage 4 enabled)
✓ features/github-sync/[slug]-issue-map.json (if Stage 5 enabled)
```

---

## Related Documentation

- **Master Workflow**: `.github/workflows/planning-workflow.md`
- **Normal Workflow**: `.github/workflows/normal-planning.md`
- **Enhancement Detector**: `.github/skills/enhancement-detector/SKILL.md`
- **Enhancement Modifier**: `.github/skills/enhancement-modifier/SKILL.md`
- **Enhancement Story Updater**: `.github/skills/enhancement-story-updater/SKILL.md`
- **Functional Test Writer**: `.github/skills/functional-test-writer/SKILL.md`
- **GitHub Issue Uploader**: `.github/skills/github-issue-uploader/SKILL.md`
- **Planning Standards**: `.github/rules/planning-standards.md`
