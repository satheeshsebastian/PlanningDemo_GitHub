---
name: planning-workflow
description: >
  Master Planning Workflow - Unified entry point for all requirement planning activities.
  Orchestrates BRD generation, user story creation, test case writing, and GitHub integration
  with support for both green-field and enhancement scenarios.
---

# Planning Workflow - Master Orchestration

> **Single entry point** for transforming raw requirements into production-ready BRDs, user stories, functional test cases, and GitHub issues.

**Configuration**: This workflow uses LLM configuration from `.github/rules/planning-llm-config.md`
**Execution Reporting**: All runs generate execution reports per `.github/rules/execution-report-template.md`

---

## BEFORE YOU START

### 1. Check LLM Configuration
Review `.github/rules/planning-llm-config.md`:
- Defines which LLM each skill should use
- Primary + Secondary LLM options per skill
- Token budget and escalation rules
- Cost estimates per workflow run

### 2. Understand Execution Reporting
Every workflow run will be tracked with:
- Step-by-step status (input/output per stage)
- LLM used at each stage (with version)
- Token consumption (input/output)
- Agent decisions and key insights
- Quality metrics and approval gates
- See template: `.github/rules/execution-report-template.md`

---

## Workflow Overview

```
┌─────────────────────────────────────────────────────────────┐
│           PLANNING WORKFLOW - MASTER ORCHESTRATION          │
│                    (planning-workflow.md)                   │
└─────────────────────────────────────────────────────────────┘

                    INPUT & ENHANCEMENT DETECTION
                           Stage 1
                          /      \
                         /        \
            NO MATCH (< 40%)    MATCH (> 70%)
                   /                  \
                  /                    \
    ┌─────────────────────┐  ┌──────────────────────────┐
    │ NORMAL WORKFLOW     │  │ ENHANCEMENT WORKFLOW     │
    │ (New Feature)       │  │ (Existing Feature)       │
    │                     │  │                          │
    │ normal-planning.yml │  │ enhancement-planning.yml │
    │                     │  │                          │
    │ ├─ brd-generator    │  │ ├─ enhancement-modifier  │
    │ ├─ user-story-      │  │ ├─ enhancement-story-    │
    │ │  builder          │  │ │  updater               │
    │ └─ functional-tests │  │ └─ functional-tests      │
    │    (optional)       │  │    (optional)            │
    └─────────────────────┘  └──────────────────────────┘
             │                          │
             └──────────────┬───────────┘
                            │
                    GitHub Integration
                    (github-issue-uploader)
                           Stage 5
                            │
                     Completion & Summary
                           Stage 6
```

---

## Stage 1: Input & Enhancement Detection

**LLM Configuration**: See `planning-llm-config.md` - Enhancement Detector section

**Workflow Execution**:
- **NEW FEATURE**: Routes to `.github/workflows/normal-planning.yml`
- **ENHANCEMENT**: Routes to `.github/workflows/enhancement-planning.yml`

**Process**:
- Request raw requirement input if not provided
- Invoke **enhancement-detector** skill
  - LLM: Claude Sonnet 4.6 (primary)
  - Tokens: ~3-7K estimated
- **ACTIVE SEARCH**: Scans `features/brd/` and `features/user-stories/` for matches
- Display findings with confidence scores (0-100%)
- **Auto-recommend**: UPDATE vs CREATE based on match > 70%
- Ask user: **"Proceed with enhancement or create new feature?"**

**Execution Tracking**:
- Recorded in execution report (Stage 1 section)
- LLM selection + tokens logged
- Decision captured
- Workflow path selected (normal or enhancement)

---

## Stage 2: Brainstorming & BRD Generation

**Workflow Path**: 
- **NEW FEATURE** → `.github/workflows/normal-planning.yml` (Step 2)
- **ENHANCEMENT** → `.github/workflows/enhancement-planning.yml` (Step 2)

**LLM Configuration**: See `planning-llm-config.md` - BRD Generator section

### For NEW Features:
1. Invoke **brd-generator** skill
   - LLM: Claude Sonnet 4.6 (primary) or Claude Opus 4.8 (if escalated)
   - Tokens: ~4-7K estimated
2. Perform BMAD analysis
3. Generate **2-3 clarifying questions** (conversational, one at a time)
4. **MANDATORY PAUSE** - Wait for user answers after each question
5. Synthesize feedback
6. Extract assumptions (ASSUMPTION-001, etc.)
7. Generate formal BRD (v1.0)
8. **APPROVAL GATE 1**: Stakeholder sign-off required
9. Save to `features/brd/[slug]-v1.0.md` and assumptions file

### For ENHANCEMENTS:
1. Invoke **enhancement-modifier** skill (NEW)
   - LLM: Claude Sonnet 4.6 (primary)
   - Tokens: ~3-5K estimated
2. Ask 1 clarification question if scope is ambiguous (optional)
3. Load existing BRD
4. Identify sections for modification
5. Determine version bump:
   - **MINOR** (v2.0 → v2.1): Backward-compatible extensions
   - **MAJOR** (v2.0 → v3.0): Breaking changes
6. Merge new requirements (add without replacing)
7. Update assumptions (add ASSUMPTION-007, etc.)
8. Generate `CHANGELOG.md` with all changes
9. Save to `features/brd/[slug]-v[N].md` (updated version)

**Execution Tracking**:
- Workflow path taken (normal vs enhancement)
- LLM used, version, and selection rationale
- Input tokens (requirement size)
- Output tokens (BRD generation)
- Version bump strategy (if enhancement)
- Approval status

---

## Stage 3: User Story Decomposition

**Workflow Path**:
- **NEW FEATURE** → `.github/workflows/normal-planning.yml` (Step 3)
- **ENHANCEMENT** → `.github/workflows/enhancement-planning.yml` (Step 3)

**LLM Configuration**: See `planning-llm-config.md` - User Story Builder section

### For NEW Features:
1. Invoke **user-story-builder** skill
   - LLM: Claude Sonnet 4.6 (primary) or Claude Opus 4.8 (if escalated)
   - Tokens: ~6-11K estimated
2. Analyze completed BRD
3. Identify [N] distinct user stories
4. Add per story:
   - MoSCoW classification (MUST/SHOULD/COULD/WON'T)
   - Story point breakdown (justified)
   - Assumption references
   - Traceability IDs (SC-001, SC-002, etc.)
5. **CREATE INDIVIDUAL STORY FILES** (CRITICAL)
   - One `.md` file per story: `features/user-stories/[slug].md`
   - No batching—each file created separately
6. Generate story map with dependencies
7. Generate story-traceability.json
8. **INVEST Health Check**: Validate all stories
9. **APPROVAL GATE 2**: Product owner review required
10. Save all artifacts

### For ENHANCEMENTS:
1. Invoke **enhancement-story-updater** skill (NEW)
   - LLM: Claude Sonnet 4.6 (primary)
   - Tokens: ~5-8K estimated
2. Load existing stories and story-map
3. **MODIFY EXISTING STORIES**:
   - Expand acceptance criteria (add new AC without removing old)
   - Update story points if scope significantly expanded
   - Update dependencies if new blockers
   - Update assumptions if new ones added
   - Mark all changes with "[MODIFIED in v2.1]"
4. **CREATE NEW STORIES** for new capabilities:
   - Continue story ID sequence (SC-014, SC-015, etc.)
   - Link to modified stories as dependencies
   - Mark as "[NEW in v2.1]"
5. Update story-map.md with new totals and relationships
6. Update story-traceability.json with modified entries
7. Save all updated story files
8. Generate summary of changes

**Execution Tracking**:
- Workflow path taken (normal vs enhancement)
- LLM consistency (same as Stage 2?)
- Input tokens (BRD + assumptions or existing stories)
- Output tokens (stories + map)
- Stories generated count (new) or modified count (enhancement)
- MOSCOW distribution
- Approval status

---

## Stage 4: Functional Test Case Writing

**LLM Configuration**: See `planning-llm-config.md` - Functional Test Writer section

**Process**:
1. Invoke **functional-test-writer** skill
   - LLM: Claude Sonnet 4.6 (primary) or Claude Opus 4.8 (if escalated)
   - Tokens: ~7-12K estimated
2. Analyze acceptance criteria from stories
3. Generate comprehensive test cases:
   - Happy path tests
   - Negative tests (NULL, invalid format, security)
   - Edge case tests
   - Integration tests
   - Non-functional tests (performance, security, load)
4. Create test coverage matrices
5. Add traceability (T-001 → SC-001 → REQ-001)
6. Save to `features/test-cases/[slug]-test-cases.md`

**Execution Tracking**:
- LLM used and version
- Input tokens (stories + AC)
- Output tokens (test cases)
- Test count (functional, negative, NFR)
- Coverage percentage
- Automation recommendations per test

---

## Stage 5: GitHub Integration

**LLM Configuration**: See `planning-llm-config.md` - GitHub Issue Uploader section

**Process** (if user chooses):
1. Invoke **github-issue-uploader** skill
   - LLM: Claude Sonnet 4.6 (primary)
   - Tokens: ~3.5-6K estimated
2. Verify GitHub CLI authenticated
3. **Pre-Upload Validation**: Check story completeness
4. Extract traceability (BRD ID, Story ID, Test ID)
5. Create GitHub issues with full context:
   - Story issues (10+)
   - Test suite grouping issues
   - Complete traceability in issue body
   - MoSCoW labels
   - Story ID labels
6. Link dependencies between issues
7. Create issue-map.json with complete audit trail
8. Save mapping to `features/github-sync/[slug]-issue-map.json`

**Execution Tracking**:
- LLM used and version
- Input tokens (stories + tests + traceability)
- Output tokens (issue bodies + JSON)
- Issues created count
- Traceability mappings (Req → Story → Test → Issue)

---

## Stage 6: Completion & Summary

**Process**:
1. Generate **Execution Report**
   - Complete stage-by-stage details
   - LLM selections and tokens per stage
   - Quality metrics
   - Artifacts generated
   - Key decisions and insights
   
2. Display summary:
   - Artifacts created (count, types, size)
   - Total tokens used vs. budget
   - LLM distribution
   - Quality scores
   - Next steps
   
3. Confirm ready for development

**Execution Report Location**: `features/[slug]/execution-report-[DATE].json`
3. Create GitHub issues from user stories
4. Create test case issues (optional)
5. Apply labels and link dependencies
6. Generate tracking artifact
7. Save to `features/github-sync/[slug]-issue-map.json`

---

## Stage 6: Completion & Summary

**Output**:
- Workflow completion report
- List all artifacts created
- Next steps for team
- Helpful commands for team

---

## Workflow Architecture & Routing

### Master Workflow (`planning-workflow.md` - This Document)
- **Purpose**: Defines the complete planning process (6 stages)
- **Entry Point**: User provides requirements
- **Decision Point**: Stage 1 - Enhancement Detection
- **Routing**: Directs to appropriate sub-workflow based on feature detection

### Normal Planning Workflow (`.github/workflows/normal-planning.yml`)
**When to Use**: Feature is brand new (enhancement-detector finds NO match)

**Flow**:
```
Stage 1: enhancement-detector
         └─ Result: NO MATCH
                    ↓
Stage 2: brd-generator
         └─ Generates v1.0 BRD
                    ↓
Stage 3: user-story-builder
         └─ Creates individual story files
                    ↓
Stage 4: functional-test-writer (optional)
         └─ Generates test cases
                    ↓
Stage 5: github-issue-uploader (optional)
         └─ Creates GitHub issues
                    ↓
Stage 6: Completion report
```

**Artifacts Generated**:
- `features/brd/[slug]-v1.0.md` (new BRD)
- `features/brd/[slug]-assumptions.md` (assumptions)
- `features/user-stories/[slug-1].md` ... `[slug-N].md` (individual stories)
- `features/user-stories/story-map-[slug].md` (dependency map)
- `features/user-stories/story-traceability.json` (traceability)
- `features/test-cases/[slug]-test-cases.md` (if tests enabled)
- `features/github-sync/[slug]-issue-map.json` (if GitHub enabled)

### Enhancement Planning Workflow (`.github/workflows/enhancement-planning.yml`)
**When to Use**: Feature exists and you want to enhance it (enhancement-detector finds MATCH > 70%)

**Flow**:
```
Stage 1: enhancement-detector
         └─ Result: MATCH FOUND
                    │
                    └─ Returns: existing BRD path, confidence %
                                ↓
Stage 2: enhancement-modifier
         └─ Updates BRD v2.0 → v2.1
         └─ Adds new requirements without replacing
         └─ Creates CHANGELOG.md
                    ↓
Stage 3: enhancement-story-updater
         └─ Modifies existing stories (expand AC)
         └─ Creates new stories for new features
         └─ Updates story-map and traceability
                    ↓
Stage 4: functional-test-writer (optional)
         └─ Generates tests for modified/new stories
                    ↓
Stage 5: github-issue-uploader (optional)
         └─ Updates existing issues + creates new ones
                    ↓
Stage 6: Completion report
```

**Artifacts Generated/Updated**:
- `features/brd/[slug]-v2.1.md` (updated BRD, version bumped)
- `features/brd/[slug]-assumptions.md` (assumptions updated)
- `features/user-stories/[slug-existing].md` (modified)
- `features/user-stories/[slug-new].md` (newly created)
- `features/user-stories/story-map-[slug].md` (updated totals)
- `features/user-stories/story-traceability.json` (updated mappings)
- `features/CHANGELOG.md` (new file tracking changes)
- `features/test-cases/[slug]-test-cases.md` (if tests enabled)
- `features/github-sync/[slug]-issue-map.json` (if GitHub enabled)

### Decision Logic (Stage 1)

**Enhancement Detector performs**:
1. Extract keywords from user input
2. Search `features/brd/` for matching BRD files
3. Search `features/user-stories/` for matching story files
4. Calculate relevance score for each match (0-100%)
5. Provide recommendation:
   - **Score > 70%**: RECOMMEND UPDATE (enhancement path)
   - **Score 40-70%**: RECOMMEND CREATE NEW (with note about partial overlap)
   - **Score < 40%**: RECOMMEND CREATE NEW (no overlap)

**User Decision**:
- Accepts recommendation → Routes to appropriate workflow
- Overrides recommendation → Routes to chosen workflow

---

## When to Use Enhancement vs. New Feature

### Use **Enhancement** when:
- Feature request builds on existing capability
- New requirements extend existing user stories
- Integration with existing system is primary
- Existing BRD covers 70%+ of scope
- Want to maintain version history (v1.0 → v2.1)
- Backward compatibility is important

### Use **New Feature** when:
- Completely separate capability
- No relationship to existing features
- Different user audience
- Separate deployment/release
- Existing BRD covers < 40% of new scope
- Independent feature set

---

## Success Criteria

Your planning workflow succeeds when:

✅ **Requirements are Clear**
- BRD captures all requirements
- Stakeholders approve without revisions

✅ **Stories are Testable**
- Each story has measurable acceptance criteria
- Test cases directly trace to criteria

✅ **Team is Ready**
- Developers know exactly what to build
- QA has comprehensive test cases
- GitHub issues are assigned and prioritized

✅ **No Rework Needed**
- Development proceeds without scope creep
- Feature delivered as specified

---

## Integration with Existing Architecture

### Preserved:
- 3-Tier API Architecture
- Tech Stack (Node.js, React, Vite, Tailwind CSS)
- Existing Standards
- Architecture Best Practices

### Added:
- Planning Workflow (orchestration)
- 5 Skills (specialized planning tools)
- Planning Standards (templates)
- Enhancement Detection
- GitHub Integration

---

## File Structure

```
.github/
├── docs/
│   └── PLANNING-WORKFLOW-README.md      ← User guide
├── rules/
│   └── planning-standards.md            ← All templates & standards
├── skills/
│   ├── brd-generator/SKILL.md
│   ├── user-story-builder/SKILL.md
│   ├── functional-test-writer/SKILL.md
│   ├── enhancement-detector/SKILL.md
│   ├── enhancement-modifier/SKILL.md    ← NEW (for enhancements)
│   ├── enhancement-story-updater/SKILL.md ← NEW (for enhancements)
│   └── github-issue-uploader/SKILL.md
└── workflows/
    ├── planning-workflow.md             ← Master orchestration (this file)
    ├── normal-planning.yml              ← NEW (new feature path)
    └── enhancement-planning.yml         ← NEW (enhancement path)

features/
├── brd/
│   ├── [slug]-v1.0.md                   ← Generated BRDs (new features)
│   └── [slug]-v2.1.md                   ← Updated BRDs (enhancements)
├── user-stories/
│   ├── [slug-1].md                      ← Individual stories
│   ├── [slug-2].md
│   ├── story-map-[slug].md              ← Dependency map
│   └── story-traceability.json          ← Story-to-requirement mapping
├── test-cases/
│   └── [slug]-test-cases.md             ← Test cases
├── github-sync/
│   └── [slug]-issue-map.json            ← GitHub tracking
└── CHANGELOG.md                         ← Version tracking (enhancements)
```

---

## Quick Start

1. **Provide Input**
   - Meeting transcript, email, document, or description

2. **Answer Brainstorming Questions**
   - System asks 4-6 clarifying questions
   - Answer in chat

3. **Review Artifacts**
   - BRD generated
   - User stories created
   - Test cases written
   - GitHub issues created (optional)

4. **Share with Team**
   - All artifacts are markdown files
   - GitHub issues linked and labeled
   - Ready for development

---

## Next Steps

1. Review `.github/docs/PLANNING-WORKFLOW-README.md` (user guide)
2. Review `.github/rules/planning-standards.md` (all templates)
3. Provide your first requirement
4. Answer brainstorming questions
5. Review generated artifacts
6. Share with team

