---
name: planning-workflow
description: Master Planning Workflow - Routes requirements to normal or enhancement path
---

# Planning Workflow - Master Orchestration

Transforms requirements into BRDs, user stories, tests, and GitHub issues.

## Workflow Routing

```
USER INPUT
    ↓
Stage 1: enhancement-detector
    ├─ Search: features/brd/ & features/user-stories/
    ├─ Confidence < 40%  → normal-planning.md
    └─ Confidence > 70%  → enhancement-planning.md
    ↓
Stages 2-6: plan → stories → tests → GitHub → execution report
    ↓
Stages 7-9: result-analyzer → scoring-agent → rl-next-steps-recommender
    ↓
Policy update (features/analysis/rl-policy-state.json) biases the next run

⟲ ai-signal-auditor captures every AI signal & action across ALL stages
```

## Path Selection

| Scenario | Workflow |
|----------|----------|
| **New Feature** | `normal-planning.md` |
| **Enhancement** | `enhancement-planning.md` |

## Skills Reference

See individual skill files for detailed logic:

- `enhancement-detector` → `.github/skills/enhancement-detector/SKILL.md`
- `brd-generator` → `.github/skills/brd-generator/SKILL.md`
- `user-story-builder` → `.github/skills/user-story-builder/SKILL.md`
- `enhancement-modifier` → `.github/skills/enhancement-modifier/SKILL.md`
- `enhancement-story-updater` → `.github/skills/enhancement-story-updater/SKILL.md`
- `functional-test-writer` → `.github/skills/functional-test-writer/SKILL.md`
- `github-issue-uploader` → `.github/skills/github-issue-uploader/SKILL.md`
- `ai-signal-auditor` → `.github/skills/ai-signal-auditor/SKILL.md` (all stages)
- `result-analyzer` → `.github/skills/result-analyzer/SKILL.md` (Stage 7)
- `scoring-agent` → `.github/skills/scoring-agent/SKILL.md` (Stage 8)
- `rl-next-steps-recommender` → `.github/skills/rl-next-steps-recommender/SKILL.md` (Stage 9)

## Rules Reference

- `.github/rules/planning-standards.md` — artifact quality standards
- `.github/rules/planning-llm-config.md` — LLM selection & token budgets
- `.github/rules/ai-audit-standards.md` — AI signal/action audit schema (mandatory)
- `.github/rules/agentic-rl-standards.md` — agentic RL rewards, policy & guardrails

## File Structure

```
.github/
├── workflows/
│   ├── planning-workflow.md          ← Routing (this file)
│   ├── normal-planning.md            ← New feature path
│   └── enhancement-planning.md       ← Enhancement path
├── rules/
│   ├── planning-standards.md
│   ├── planning-llm-config.md
│   ├── ai-audit-standards.md
│   └── agentic-rl-standards.md
└── skills/
    ├── brd-generator/SKILL.md
    ├── user-story-builder/SKILL.md
    ├── functional-test-writer/SKILL.md
    ├── enhancement-detector/SKILL.md
    ├── enhancement-modifier/SKILL.md
    ├── enhancement-story-updater/SKILL.md
    ├── github-issue-uploader/SKILL.md
    ├── ai-signal-auditor/SKILL.md
    ├── result-analyzer/SKILL.md
    ├── scoring-agent/SKILL.md
    └── rl-next-steps-recommender/SKILL.md

features/
├── brd/[slug]-v*.md
├── user-stories/[slug-*.md]
├── test-cases/[slug]-test-cases.md
├── github-sync/[slug]-issue-map.json
├── audit/ai-signal-log-{RUN_ID}.jsonl
├── audit/ai-action-audit-{RUN_ID}.md
├── reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md
└── analysis/
    ├── RESULT-ANALYSIS-{RUN_ID}.md
    ├── SCORECARD-{RUN_ID}.md
    ├── NEXT-STEPS-{RUN_ID}.md
    └── rl-policy-state.json
```

## Documentation

**For new features**: See `normal-planning.md`
**For enhancements**: See `enhancement-planning.md`