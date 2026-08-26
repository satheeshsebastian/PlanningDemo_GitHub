# Planning Workflow - Complete Guide

## Overview

The **Planning Workflow** is a comprehensive, professional process for transforming raw requirements into production-ready Business Requirement Documents (BRDs), user stories, functional test cases, and GitHub issues.

### What Gets Created

✅ **Business Requirement Document (BRD)** - Formal requirements specification  
✅ **User Stories** - Individual, testable feature requirements  
✅ **Functional Test Cases** - BDD-style test specifications  
✅ **GitHub Issues** - Team-ready work tracking  
✅ **Dependency Maps** - Story and test relationships  
✅ **Tracking Artifacts** - Issue ID mappings for future reference  
✅ **AI Signal & Action Audit Trail** - Every AI signal, action, decision and human override  
✅ **Result Analysis, Scorecard & RL Next Steps** - Verifier-based scoring and a learning loop  

---

## Quick Start

### For Your First Planning Session

1. **Provide Input**
   ```
   Share your requirement in one of these formats:
   - Meeting transcript (Google Meet, Zoom, Teams)
   - Email thread or feature request
   - Requirements document
   - Verbal description
   ```

2. **Answer Brainstorming Questions**
   ```
   The workflow will ask 4-6 questions to identify gaps and clarify scope.
   Answer directly in the chat.
   ```

3. **Review Generated Artifacts**
   ```
   - BRD: Formal requirements document
   - User Stories: Individual work items
   - Test Cases: Quality assurance specifications
   ```

4. **Upload to GitHub** (Optional)
   ```
   Convert stories to GitHub issues for team tracking.
   ```

---

## Workflow Stages (9 Steps + Continuous Auditing)

### Stage 1: Input & Enhancement Detection

**What happens**:
- You provide raw requirements
- System checks if it's an enhancement to existing feature
- You confirm: **New Feature** or **Enhancement**

**If Enhancement**: 
- Existing BRD and stories are identified
- You choose to update existing or create new
- Updates merge with existing artifacts

**If New Feature**:
- Proceed directly to BRD generation

**Duration**: 5-10 minutes

---

### Stage 2: Brainstorming & BRD Generation

**What happens**:
1. System analyzes your requirement (BMAD framework)
2. System generates **4-6 targeted questions** addressing gaps
3. **MANDATORY PAUSE** - You answer these questions
4. System synthesizes your answers
5. System generates formal BRD document

**Example Questions You Might See**:
- "Which user roles will access this feature?"
- "Should this integrate with existing system X?"
- "What's your expected user volume?"
- "Are there compliance requirements?"
- "How do we measure success?"

**What You Get**:
- Professional BRD with 9 sections
- Saved to `features/brd/[feature-name]-v1.0.md`

**Duration**: 10-15 minutes (including your answers)

---

### Stage 3: User Story Decomposition

**What happens**:
1. System analyzes the BRD
2. System identifies distinct user stories
3. System creates individual story files
4. System maps dependencies between stories
5. System generates story map overview

**What You Get**:
- Multiple user stories (typically 3-8 per feature)
- Each story in BDD format: "As a [role], I want [action], So that [benefit]"
- Acceptance criteria in Given/When/Then format
- Story dependencies clearly marked
- Saved to `features/user-stories/[story-name].md`

**Duration**: 5-10 minutes (automated)

---

### Stage 4: Functional Test Case Writing

**What happens**:
1. System analyzes acceptance criteria
2. System generates comprehensive test cases
3. System covers: Happy path, Errors, Edge cases
4. System creates test coverage matrices
5. System organizes by story

**What You Get**:
- Test cases in BDD Given/When/Then format
- Happy path tests (normal scenarios)
- Error scenario tests (what happens when things go wrong)
- Edge case tests (boundary conditions)
- Manual vs. Automated indicators
- Saved to `features/test-cases/[story-name]-test-cases.md`

**Duration**: 10-15 minutes (automated)

---

### Stage 5: GitHub Integration

**What happens** (if you choose):
1. System creates GitHub issue for each user story
2. System creates GitHub issue for test case suites
3. System applies labels (user-story, priority, epic, etc.)
4. System links dependencies between issues
5. System creates tracking artifact (issue-map.json)

**Prerequisites**:
- GitHub CLI installed (`gh auth login`)
- Repository accessible

**What You Get**:
- All stories as GitHub issues
- All test suites as linked GitHub issues
- Issues labeled and prioritized
- Dependencies visible in GitHub
- Tracking file mapping story slugs to issue numbers
- Team can start working immediately

**Duration**: 5 minutes

---

### Stage 6: Completion & Summary

**What happens**:
1. System generates completion report
2. System lists all artifacts created
3. System provides next steps for team
4. System offers helpful commands

**What You Get**:
- Summary of all created artifacts
- Statistics (# stories, # test cases, etc.)
- Links to GitHub issues
- Step-by-step instructions for team
- Helpful commands for future updates

**Duration**: 2 minutes (report only)

**Quality Gate**: The report fails if the AI signal audit log is incomplete.

---

### Stage 7: Result Analysis (automatic)

**Skill**: `result-analyzer`

**What happens**:
1. The AI signal/action audit trail is replayed against the artifacts actually produced
2. Coverage gaps, traceability breaks, rule violations and unaudited actions are detected
3. Each finding is given a severity and a root cause, with evidence (`event_id` + file path)
4. Results are compared to previous runs of the same feature (trend)

**What You Get**: `features/analysis/RESULT-ANALYSIS-{RUN_ID}.md` + JSON findings

---

### Stage 8: Scoring (automatic)

**Skill**: `scoring-agent`

**What happens**:
1. Deterministic verifiers score coverage, conformance, audit completeness, efficiency and
   human alignment (no LLM grades its own work)
2. A run score (0-100) and grade are produced, plus a per-stage breakdown
3. Every AI action receives a reinforcement-learning reward (−1.0 … +1.0)
4. The run is normalised against the last 5 comparable runs (group-relative advantage)

**What You Get**: `features/analysis/SCORECARD-{RUN_ID}.md` + JSON scorecard

---

### Stage 9: RL Next Steps (automatic)

**Skill**: `rl-next-steps-recommender`

**What happens**:
1. The run is turned into an RL episode: state → action → reward → return
2. The workflow policy (`rl-policy-state.json`) is updated with small, bounded steps
3. Proposed changes are estimated against past runs before activation; changes touching a human
   approval gate stay as proposals
4. Next best actions are ranked (immediate fixes, next-run policy, delivery backlog) plus one
   labelled exploration experiment

**What You Get**: `features/analysis/NEXT-STEPS-{RUN_ID}.md` + updated policy state

---

## AI Signal & Action Auditing (all stages)

**Skill**: `ai-signal-auditor` — **Standard**: `.github/rules/ai-audit-standards.md`

Every AI signal (what the agent saw) and every AI action (what the agent did — including
rationale, rules applied, rejected alternatives, and your approvals/overrides) is written to an
append-only log as it happens:

```
features/audit/ai-signal-log-{RUN_ID}.jsonl
features/audit/ai-action-audit-{RUN_ID}.md
features/audit/audit-index.json
```

**Rule**: no silent AI action. An artifact with no matching audit event becomes a Stage 7
finding, and the audit trail is the input to scoring and learning.

---

## Artifact Organization

All planning artifacts are organized in this structure:

```
features/
├── brd/
│   ├── employee-certification-v1.0.md
│   └── [other features]
│
├── user-stories/
│   ├── employee-enroll-course.md
│   ├── employee-track-progress.md
│   └── story-map-employee-certification.md
│
├── test-cases/
│   ├── employee-enroll-course-test-cases.md
│   └── employee-track-progress-test-cases.md
│
├── github-sync/
│   └── employee-certification-issue-map.json
│
├── audit/
│   ├── ai-signal-log-{RUN_ID}.jsonl
│   ├── ai-action-audit-{RUN_ID}.md
│   └── audit-index.json
│
├── reports/
│   └── PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md
│
└── analysis/
    ├── RESULT-ANALYSIS-{RUN_ID}.md
    ├── SCORECARD-{RUN_ID}.md
    ├── NEXT-STEPS-{RUN_ID}.md
    └── rl-policy-state.json
```

---

## Key Features

### 1. Interactive Brainstorming (4-6 Questions)

The system generates targeted questions to:
- Identify scope boundaries
- Clarify user roles and permissions
- Understand integration requirements
- Confirm performance expectations
- Identify compliance needs
- Define success metrics

This ensures **nothing is missed** and requirements are **crystal clear**.

### 2. Enhancement Detection

New requirement?
- System searches existing BRDs, user stories, test cases
- System shows matching artifacts (if any)
- You decide: **Update existing** or **Create new**
- System preserves relationships and version history

### 3. BDD Test Format

Test cases use industry-standard **Given/When/Then** format:

```
Given I am logged in as an employee
When I click "Enroll in Course"
Then I see course enrollment form
  And I can select a course
  And I can submit enrollment
```

This format is:
- ✅ Readable by all team members (not just QA)
- ✅ Automatable by testing tools
- ✅ Directly traceable to acceptance criteria

### 4. GitHub Integration

Seamless connection to GitHub:
- Stories become issues
- Test suites become linked issues
- Labels organize by priority, epic, type
- Dependencies show blocking relationships
- Team can start work immediately

### 5. Dependency Mapping

Stories are linked to show:
- **Blocks**: What stories depend on this one
- **Blocked By**: What must be completed first
- **Related**: Cross-functional relationships

---

## Example Workflow: Certification Management

### Input (User Provides)
```
Meeting Transcript Excerpt:
"We need a way to track employee certifications. 
Admins should create certification courses, and 
employees should be able to enroll and see their progress."
```

### Output
**BRD**: `features/brd/employee-certification-v1.0.md`
**User Stories** (5-8 stories): `features/user-stories/employee-*.md`
**Test Cases**: `features/test-cases/*-test-cases.md`
**GitHub Issues**: #123-#127 (linked & labeled)
**Tracking**: `features/github-sync/employee-certification-issue-map.json`

---

## FAQ

**Q: How long does a planning session take?**  
A: 30-60 minutes including your input and questions. Most time is the brainstorming phase (10-15 min of your time).

**Q: Can I update artifacts after creation?**  
A: Yes! Edit the markdown files locally, then sync to GitHub.

**Q: What if requirements change mid-way?**  
A: Stop, update the BRD, and re-run the workflow.

**Q: Do I need to use GitHub?**  
A: No, GitHub is optional. You can use artifacts locally.

**Q: Can I export artifacts to other formats?**  
A: All artifacts are markdown. You can convert to PDF, Word using tools like Pandoc.

**Q: What if enhancement detection finds false matches?**  
A: Review the findings. If not related, choose "CREATE NEW".

---

## Standards & Best Practices

All artifacts follow these standards:
- **BRD**: `.github/rules/planning-standards.md` § 1
- **User Stories**: `.github/rules/planning-standards.md` § 2  
- **Test Cases**: `.github/rules/planning-standards.md` § 3

---

## Support

### Need Help?
- Review `.github/rules/planning-standards.md` for detailed templates
- Check workflow guide at `.github/workflows/planning-workflow-master.md`
- Review skill documentation in `.github/skills/*/SKILL.md`

---

## Next Steps After Planning

### Phase 1: Approval
- [ ] Share artifacts with stakeholders
- [ ] Get sign-off on requirements
- [ ] Confirm priorities and scope

### Phase 2: Development
- [ ] Team reviews stories
- [ ] Team estimates effort
- [ ] Move issues to "Ready" in GitHub project

### Phase 3: Implementation
- [ ] Reference GitHub issue numbers in commits
- [ ] QA executes test cases

### Phase 4: Deployment
- [ ] Final stakeholder review
- [ ] Deploy to production
- [ ] Update issue status to "Done"

---

## Key Files

```
.github/
├── workflows/
│   ├── planning-workflow-master.md    ← Main orchestration & auto-routing guide
│   ├── new-feature-planning.md        ← New feature path
│   └── enhancement-planning.md        ← Enhancement path
│
├── skills/
│   ├── brd-generator/SKILL.md         ← BRD generation
│   ├── user-story-builder/SKILL.md   ← Story decomposition
│   ├── functional-test-writer/SKILL.md ← Test case writing
│   ├── enhancement-detector/SKILL.md  ← Existing artifact detection
│   ├── github-issue-uploader/SKILL.md ← GitHub integration
│   ├── ai-signal-auditor/SKILL.md     ← AI signal & action audit trail (all stages)
│   ├── result-analyzer/SKILL.md       ← Stage 7: outcome & anomaly analysis
│   ├── scoring-agent/SKILL.md         ← Stage 8: verifier-based scoring & rewards
│   └── rl-next-steps-recommender/SKILL.md ← Stage 9: RL policy update & next steps
│
└── rules/
    ├── planning-standards.md          ← Templates & standards
    ├── planning-llm-config.md         ← LLM selection & token budgets
    ├── ai-audit-standards.md          ← AI signal/action audit schema (mandatory)
    └── agentic-rl-standards.md        ← Agentic RL rewards, policy & guardrails
```

---

## Success Metrics

Your planning workflow is successful when:

✅ BRD captures all requirements  
✅ User stories are independent and testable  
✅ Test cases cover acceptance criteria  
✅ GitHub issues are ready for team  
✅ Stakeholders approve all artifacts  
✅ Team can begin development immediately  
✅ No requirement rework needed during development  
✅ Every AI signal and action is captured in the audit trail (audit completeness = 100%)  
✅ The run is scored by deterministic verifiers, and next steps come from the RL loop  

---

**Ready to get started?** Provide your requirement, and the planning workflow will guide you through each stage!
