# Normal Planning Workflow (New Features)

## Overview
Creates a complete BRD and user stories for **brand new features** (not enhancements to existing capabilities).

**When to Use**: Enhancement detector finds NO match (confidence < 40%)

---

## Workflow Steps

### Stage 1: Enhancement Detection ✓
**Status**: Already completed in planning-workflow.md

- Result: NO EXISTING ARTIFACTS FOUND
- User decision: Proceed with NEW feature creation

---

### Stage 2: BRD Generation

**Skill**: `brd-generator` (`.github/skills/brd-generator/SKILL.md`)

**Input**:
- Raw requirement description from user
- Domain/context information
- Business goals and objectives

**Process**:
1. **BMAD Analysis** (Brainstorming, Motivation, Acceptance, Definition)
   - What are the user problems? (Brainstorming)
   - Why is this valuable? (Motivation)
   - How do we validate success? (Acceptance)
   - What exactly is in/out of scope? (Definition)

2. **Ask Clarifying Questions** (Max 2-3)
   - Conversational, one question at a time
   - Wait for user response after each question
   - Focus on BMAD gaps only
   - Target: 2-3 minute interaction

3. **Synthesize Feedback**
   - Incorporate user answers
   - Extract assumptions (ASSUMPTION-001, ASSUMPTION-002, etc.)
   - Validate scope clarity

4. **Generate BRD v1.0**
   - 9 sections: Overview, Scope, Requirements, Acceptance Criteria, Assumptions, Success Criteria, Technical Design, Dependencies, Appendices
   - Include version header: "v1.0 - NEW"
   - Create assumptions file: `[slug]-assumptions.md`

**Outputs**:
```
features/brd/
├── [slug]-v1.0.md              ← Main BRD document
└── [slug]-assumptions.md       ← Assumptions register
```

**Approval Gate 1**: ✅ Stakeholder sign-off required

---

### Stage 3: User Story Building

**Skill**: `user-story-builder` (`.github/skills/user-story-builder/SKILL.md`)

**Input**:
- Completed BRD v1.0 from Stage 2
- Assumptions file
- Target story point capacity

**Process**:
1. **Read & Analyze BRD**
   - Extract all functional requirements
   - Identify user journeys
   - Note dependencies and sequences

2. **Identify Stories**
   - Decompose BRD into independent user stories
   - One story per user journey or feature
   - Minimize dependencies

3. **Reference Assumptions**
   - Link each story to relevant assumptions
   - Flag high-risk assumptions

4. **Assign Story Details**
   - Story ID: SC-001, SC-002, SC-003, etc.
   - Slug: kebab-case descriptive name
   - MoSCoW: MUST/SHOULD/COULD/WON'T
   - Story points: With breakdown justification
   - Acceptance criteria: BDD Given/When/Then format

5. **Create Individual Story Files** ⭐ CRITICAL
   - **One .md file per story** (not batched)
   - Format: `features/user-stories/[slug].md`
   - Include all sections: Statement, AC, Technical Requirements, Dependencies, Points, MoSCoW, Assumptions, INVEST Check, Definition of Done
   - Verify each file created before proceeding to next story

6. **Generate Story Map**
   - Overview showing all stories
   - Story count and point totals
   - Dependency graph
   - Story matrix with priorities

7. **Generate Traceability JSON**
   - Maps story IDs to BRD requirements
   - Links stories to assumptions
   - Prepares for test case generation

8. **INVEST Validation**
   - Independent: Can be done without 3+ blockers?
   - Negotiable: Details can be discussed?
   - Valuable: Clear user/business value?
   - Estimable: Can be sized?
   - Small: < 13 story points?
   - Testable: Clear acceptance criteria?

**Outputs**:
```
features/user-stories/
├── [slug-1].md                 ← Individual story file
├── [slug-2].md
├── [slug-3].md
├── ... (one per story)
├── story-map-[slug].md         ← Dependency overview
└── story-traceability.json     ← Story-to-requirement mapping
```

**Approval Gate 2**: ✅ Product owner review required

---

### Stage 4: Functional Test Case Writing (Optional)

**Skill**: `functional-test-writer` (`.github/skills/functional-test-writer/SKILL.md`)

**Input**:
- User stories and acceptance criteria from Stage 3
- Story-traceability.json
- Test coverage requirements

**Process**:
1. **Analyze Acceptance Criteria**
   - Extract test scenarios from AC
   - Identify edge cases and error conditions

2. **Generate Test Cases**
   - Happy path tests (main flows)
   - Negative tests (error handling)
   - Edge case tests (boundary conditions)
   - Non-functional tests (performance, security)

3. **Create Test Documentation**
   - BDD format (Given/When/Then)
   - Traceability to user stories
   - Automation recommendations

**Outputs**:
```
features/test-cases/
└── [slug]-test-cases.md        ← Comprehensive test cases
```

---

### Stage 5: GitHub Integration (Optional)

**Skill**: `github-issue-uploader` (`.github/skills/github-issue-uploader/SKILL.md`)

**Input**:
- User stories from Stage 3
- Test cases from Stage 4 (if generated)
- Traceability mappings

**Process**:
1. **Create Story Issues**
   - One GitHub issue per user story
   - Include full story details in issue body
   - Add labels: Story ID, MoSCoW priority
   - Link to parent epic (if applicable)

2. **Create Test Issues** (Optional)
   - Group test cases by story
   - Link to corresponding story issue

3. **Link Dependencies**
   - Create issue links showing story dependencies
   - Show blocker relationships

4. **Generate Tracking Artifact**
   - `features/github-sync/[slug]-issue-map.json`
   - Maps: Issue # → Story ID → BRD Requirement

**Outputs**:
```
features/github-sync/
└── [slug]-issue-map.json       ← GitHub issue tracking
```

---

### Stage 6: Completion & Summary

**Process**:
1. **Generate Execution Report**
   - All stages completed successfully
   - Artifacts created (count, types, file paths)
   - Assumptions extracted and documented
   - Stories generated (count, total points)
   - Test cases created (count, coverage %)
   - GitHub issues created (count, issue #s)

2. **Display Summary**
   ```
   ✅ NEW FEATURE PLANNING COMPLETE
   
   📄 BRD Generated:
      - features/brd/[slug]-v1.0.md
      - features/brd/[slug]-assumptions.md
   
   📋 User Stories Created: 13 stories, 73 points
      - 7 MUST stories, 4 SHOULD stories, 2 COULD stories
      - 13 individual story files created
      - features/user-stories/story-map-[slug].md
      - features/user-stories/story-traceability.json
   
   🧪 Test Cases Created: 45 test cases (if enabled)
      - features/test-cases/[slug]-test-cases.md
   
   🔗 GitHub Issues Created: 13 issues (if enabled)
      - features/github-sync/[slug]-issue-map.json
   
   Next Steps:
   - Review artifacts with team
   - Approve stories with product owner
   - Begin development sprints
   - Track progress in GitHub issues
   ```

3. **Confirm Ready for Development**

---

## Integration with planning-workflow.md

This workflow represents **Stages 2-6** of the master planning workflow:

```
planning-workflow.md (Master)
  └─ Stage 1: Enhancement Detection
      ├─ NO MATCH (< 40%) → normal-planning.md (THIS FILE)
      │   ├─ Stage 2: brd-generator
      │   ├─ Stage 3: user-story-builder
      │   ├─ Stage 4: functional-test-writer (optional)
      │   ├─ Stage 5: github-issue-uploader (optional)
      │   └─ Stage 6: Completion
      │
      └─ MATCH (> 70%) → enhancement-planning.md
          └─ (Different workflow for enhancing existing features)
```

---

## Key Principles

✅ **Create Individual Story Files**: Each story MUST be a separate `.md` file
✅ **Maintain Traceability**: Every artifact links to BRD requirements
✅ **Validate INVEST**: All stories pass independence, value, and size checks
✅ **Document Assumptions**: All assumptions extracted and linked to stories
✅ **Approval Gates**: Sign-offs required at Stage 2 and Stage 3
✅ **Conversational Questions**: Max 2-3 questions, one at a time
✅ **Version Control**: All artifacts saved with clear version numbers (v1.0)

---

## Time Estimate

- **Stage 2 (BRD)**: 2-3 minutes (with user responses)
- **Stage 3 (Stories)**: 10-15 minutes (skill autonomous)
- **Stage 4 (Tests)**: 5-10 minutes (optional)
- **Stage 5 (GitHub)**: 2-5 minutes (optional)
- **Total**: 20-35 minutes (depending on options enabled)

---

## File Checklist

After completing this workflow, verify these files exist:

```
✓ features/brd/[slug]-v1.0.md
✓ features/brd/[slug]-assumptions.md
✓ features/user-stories/[slug-1].md (and all others)
✓ features/user-stories/story-map-[slug].md
✓ features/user-stories/story-traceability.json
✓ features/test-cases/[slug]-test-cases.md (if Stage 4 enabled)
✓ features/github-sync/[slug]-issue-map.json (if Stage 5 enabled)
```

---

## Related Documentation

- **Master Workflow**: `.github/workflows/planning-workflow.md`
- **Enhancement Workflow**: `.github/workflows/enhancement-planning.md`
- **BRD Generator**: `.github/skills/brd-generator/SKILL.md`
- **User Story Builder**: `.github/skills/user-story-builder/SKILL.md`
- **Functional Test Writer**: `.github/skills/functional-test-writer/SKILL.md`
- **GitHub Issue Uploader**: `.github/skills/github-issue-uploader/SKILL.md`
- **Planning Standards**: `.github/rules/planning-standards.md`
