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
│           PLANNING WORKFLOW - 6 STAGES                      │
└─────────────────────────────────────────────────────────────┘

Stage 1: INPUT & ENHANCEMENT CHECK
├─ Accept raw requirement input
├─ Check if enhancement to existing feature
└─ User confirms: New or Update?

                        ↓

Stage 2: BRAINSTORMING & BRD GENERATION
├─ Analyze requirement (BMAD framework)
├─ Generate 2-3 clarifying questions simple and direct Wait for user responses after each question
├─ MANDATORY PAUSE: Wait for user responses
├─ Synthesize feedback
└─ Generate formal BRD document

                        ↓

Stage 3: USER STORY DECOMPOSITION
├─ Analyze BRD
├─ Identify distinct user stories
├─ Create individual story files
├─ Generate story map with dependencies
└─ User reviews stories (OPTIONAL PAUSE)

                        ↓

Stage 4: FUNCTIONAL TEST CASE WRITING
├─ Analyze acceptance criteria from stories
├─ Generate comprehensive test cases
├─ Cover: Happy path, Errors, Edge cases
├─ Create test matrices
└─ Generate test case documents

                        ↓

Stage 5: GITHUB INTEGRATION
├─ Parse user stories
├─ Create GitHub issues from stories
├─ Create test case issues (optional)
├─ Link dependencies between issues
├─ Create tracking artifact (issue-map.json)
└─ Generate GitHub sync summary

                        ↓

Stage 6: COMPLETION & SUMMARY
├─ Generate workflow completion report
├─ List all artifacts created
├─ Provide next steps
└─ Confirm ready for development
```

---

## Stage 1: Input & Enhancement Detection

## Stage 1: Input & Enhancement Detection

**LLM Configuration**: See `planning-llm-config.md` - Enhancement Detector section

**Process**:
- Request raw requirement input if not provided
- Invoke **enhancement-detector** skill
  - LLM: Claude Sonnet 4.6 (primary)
  - Tokens: ~3-7K estimated
- Analyze for existing BRD, stories, test cases
- Display findings with confidence scores
- Ask user: **"New Feature or Enhancement?"**

**Execution Tracking**:
- Recorded in execution report (Stage 1 section)
- LLM selection + tokens logged
- Decision captured

---

## Stage 2: Brainstorming & BRD Generation

**LLM Configuration**: See `planning-llm-config.md` - BRD Generator section

**Process**:
1. Invoke **brd-generator** skill
   - LLM: Claude Sonnet 4.6 (primary) or Claude Opus 4.8 (if escalated)
   - See config for escalation triggers
   - Tokens: ~4-7K estimated
2. Perform BMAD analysis
3. Generate 4-6 clarifying questions
4. **MANDATORY PAUSE** - Wait for user answers
5. Synthesize feedback
6. SPEC-IT clarity validation (flag vague requirements)
7. Extract assumptions (ASSUMPTION-001, etc.)
8. Generate formal BRD
9. **APPROVAL GATE 1**: Stakeholder sign-off required
10. Save to `features/brd/[slug]-v1.0.md` and assumptions file

**Execution Tracking**:
- LLM used, version, and selection rationale
- Input tokens (requirement size)
- Output tokens (BRD generation)
- Quality score (SPEC-IT compliance)
- Assumptions extracted
- Approval status

---

## Stage 3: User Story Decomposition

**LLM Configuration**: See `planning-llm-config.md` - User Story Builder section

**Process**:
1. Invoke **user-story-builder** skill
   - LLM: Claude Sonnet 4.6 (primary) or Claude Opus 4.8 (if escalated)
   - Maintain consistency with Stage 2 LLM if possible
   - Tokens: ~6-11K estimated
2. Analyze completed BRD
3. Identify [N] distinct user stories
4. Add per story:
   - MoSCoW classification (MUST/SHOULD/COULD/WON'T)
   - Story point breakdown (justified)
   - Assumption references
   - Traceability IDs (SC-001, SC-002, etc.)
5. Create individual story files
6. Generate story map with dependencies
7. **INVEST Health Check**: Validate all stories
8. **APPROVAL GATE 2**: Product owner review required
9. Save to `features/user-stories/[slug-*.md]`
10. Generate traceability JSON

**Execution Tracking**:
- LLM consistency (same as Stage 2?)
- Input tokens (BRD + assumptions)
- Output tokens (stories + map)
- Stories generated count
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

## When to Use Enhancement vs. New Feature

### Use **Enhancement** when:
- Feature request builds on existing capability
- New requirements extend existing user stories
- Integration with existing system is primary
- Existing BRD covers 70%+ of scope

### Use **New Feature** when:
- Completely separate capability
- No relationship to existing features
- Different user audience
- Separate deployment/release

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
│   └── github-issue-uploader/SKILL.md
└── workflows/
    └── planning-workflow.md             ← This file

features/
├── brd/
│   └── [slug]-v1.0.md                   ← Generated BRDs
├── user-stories/
│   ├── [slug].md                        ← Individual stories
│   └── story-map-[slug].md              ← Dependency map
├── test-cases/
│   └── [slug]-test-cases.md             ← Test cases
└── github-sync/
    └── [slug]-issue-map.json            ← GitHub tracking
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

