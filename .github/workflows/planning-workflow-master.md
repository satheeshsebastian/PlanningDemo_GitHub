# Master Planning Workflow - Intelligent Feature Planning

**Version**: 2.0  
**Date**: June 11, 2026  
**Status**: Active - Smart Auto-Routing Enabled

---

## 🎯 Overview

The Master Planning Workflow intelligently detects whether a user requirement is for:
1. **New Feature** → Routes to `new-feature-planning` workflow
2. **Enhancement** → Routes to `enhancement-planning` workflow
3. **Ambiguous** → Asks user for clarification

**Key Principle**: Never ask users to choose what the system can automatically detect.

---

## 🔄 Complete Workflow Flow

**User requirement → Stage 0: Auto-Detection → Route to appropriate path → Execute Stages 1-6 → Stages 7-9: Analyse, Score, Learn**

Every stage emits AI signal/action audit events continuously (see *AI Signal & Action Auditing* below).

---

## 📋 Stage 0: Automatic Enhancement Detection

**Responsibility**: Route requirements to appropriate workflow path

**Input**: Raw feature requirements from user

**Skill**: `.github/skills/enhancement-detector/SKILL.md`

**Logic Summary**:
- Extracts feature slug from requirements
- Searches for existing artifacts (BRD, stories, tests, GitHub issues)
- Calculates confidence score (0-100%)
- Routes based on thresholds:
  - **≥ 70% confidence** → `enhancement-planning` workflow
  - **< 40% confidence** → `new-feature-planning` workflow  
  - **40-70% confidence** → Asks user for clarification

**Output**: Detection result logged to execution report with confidence score and rationale

---

## 📋 Stages 1-6: Execute Chosen Workflow Path

### Path A: New Feature Planning (Confidence < 40%)
Auto-routed when no existing artifacts found for this feature

| Stage | Skill | Input | Output | Approval |
|-------|-------|-------|--------|----------|
| 1 | brd-generator | Feature requirements + user brainstorming | BRD v1.0, Assumptions | ✅ Required |
| 2 | user-story-builder | BRD v1.0, Assumptions | 7-11 stories, Story Map, Traceability | ✅ Required |
| 3 | functional-test-writer | User stories + AC | 100+ BDD test cases | ❌ None |
| 4 | github-issue-uploader | Stories, Tests, Traceability | GitHub issues + Issue map | ❌ None |
| 5 | (auto-generated) | Metadata from all stages | Execution Report | ❌ None |

**Total Duration**: ~6-8 hours (includes approvals)

### Path B: Enhancement Planning (Confidence ≥ 70%)
Auto-routed when existing artifacts found for this feature

| Stage | Skill | Input | Output | Approval |
|-------|-------|-------|--------|----------|
| 1 | enhancement-modifier | Existing BRD + enhancement requirements | BRD v[N+1], Updated Assumptions | ❌ None |
| 2 | enhancement-story-updater | Updated BRD + existing stories + changelog | Modified/new stories, Updated Story Map | ✅ For new stories |
| 3 | functional-test-writer | Modified/new stories + AC | New/updated test cases | ❌ None |
| 4 | github-issue-uploader | Updated stories, tests, traceability | Updated/new GitHub issues + Issue map | ❌ None |
| 5 | (auto-generated) | Metadata from all stages | Execution Report | ❌ None |

**Total Duration**: ~4-6 hours (fewer approvals)

---

## 📋 Stages 7-9: Analyse, Score, Learn (MANDATORY & AUTOMATIC)

Run after Stage 6 on **both** paths. No approval gates; all outputs are advisory.

| Stage | Skill | Input | Output |
|-------|-------|-------|--------|
| 7 | result-analyzer | AI signal log + all artifacts + execution report | `features/analysis/RESULT-ANALYSIS-{RUN_ID}.md` |
| 8 | scoring-agent | Result analysis + AI signal log + policy state | `features/analysis/SCORECARD-{RUN_ID}.md` + `.json`, rewards back-filled into the signal log |
| 9 | rl-next-steps-recommender | Scorecard + result analysis + policy state | `features/analysis/NEXT-STEPS-{RUN_ID}.md`, updated `features/analysis/rl-policy-state.json` |

**Standard**: `.github/rules/agentic-rl-standards.md`

**Guardrails**:
- Scoring uses **verifiable checks** (V1-V7), not self-assessment; the scoring agent never scores its own authored content without a verifier.
- Policy updates are **bounded** (trust region: threshold changes capped at ±5, minimum 3 visits) and human overrides are weighted 2×.
- Any policy change that would weaken a human approval gate is emitted as `pending_approval` and **never** auto-applied.
- Every policy change is versioned in `rl-policy-state.json` with a rollback entry.

---

## 🔍 AI Signal & Action Auditing (ALL STAGES)

**Skill**: `ai-signal-auditor` (`.github/skills/ai-signal-auditor/SKILL.md`)  
**Standard**: `.github/rules/ai-audit-standards.md`

Every stage MUST emit append-only audit events as they happen:

| Event | Captures |
|-------|----------|
| `signal` | What the AI observed — inputs, searches, confidence scores, anomalies |
| `action` | What the AI did — route decision, file write, GitHub call, question asked, plus rationale, rules applied, rejected alternatives and autonomy level |
| `human_gate` | Every question, approval, rejection and override (verbatim) |
| `error` | Every failure and retry |

**Audit artifacts**:
```
features/audit/ai-signal-log-{RUN_ID}.jsonl   (append-only event stream)
features/audit/ai-action-audit-{RUN_ID}.md    (human-readable summary)
features/audit/audit-index.json               (index of all runs)
```

**Rule**: No silent AI action. An artifact without a matching audit event is a Stage 7 finding, and an unauditable run is capped at grade C by Stage 8.

---

## 📚 Related Documents

- `new-feature-planning.md` - New feature workflow
- `enhancement-planning.md` - Enhancement workflow
- `.github/rules/ai-audit-standards.md` - AI signal & action audit standard
- `.github/rules/agentic-rl-standards.md` - Agentic reinforcement-learning standards
- `.github/rules/planning-llm-config.md` - Per-skill LLM configuration
- Enhancement Detector Skill: `.github/skills/enhancement-detector/SKILL.md`

---

**Status**: ✅ LIVE - Auto-Detection Enabled  
**Last Updated**: June 11, 2026  
**Implemented By**: Planning Workflow v2.0
