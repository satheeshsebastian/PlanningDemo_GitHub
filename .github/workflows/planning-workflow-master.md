# Master Planning Workflow - Intelligent Feature Planning

**Version**: 2.0  
**Date**: June 11, 2026  
**Status**: Active - Smart Auto-Routing Enabled

---

## 🎯 Overview

The Master Planning Workflow intelligently detects whether a user requirement is for:
1. **New Feature** → Routes to `normal-planning` workflow
2. **Enhancement** → Routes to `enhancement-planning` workflow
3. **Ambiguous** → Asks user for clarification

**Key Principle**: Never ask users to choose what the system can automatically detect.

---

## 🔄 Complete Workflow Flow

```
┌─────────────────────────────────────┐
│  User Provides Feature Requirements │
│  (BRD concept, user stories, etc.)  │
└────────────────┬────────────────────┘
                 ↓
    ┌────────────────────────────────┐
    │ STAGE 0: Auto-Detection (NEW)  │
    │  ✓ Run enhancement-detector    │
    │  ✓ Calculate confidence score  │
    │  ✓ Determine route             │
    └────────────┬───────────────────┘
                 ↓
        ┌────────────────────┐
        │ Confidence Check?  │
        └─┬──────┬──────┬────┘
          │      │      │
    <40%  │      │      │  ≥70%
          ↓      ↓      ↓
      [NEW]  [ASK]  [ENH]
        ↓      ↓      ↓
        └──────┴──────┘
                ↓
    ┌─────────────────────────────────┐
    │ NORMAL-PLANNING or              │
    │ ENHANCEMENT-PLANNING            │
    │ (Stages 1-6)                    │
    └────────────────┬────────────────┘
                     ↓
    ┌─────────────────────────────────┐
    │ LEARNING LOOP (auto, stages 7-9)│
    │  7. result-analyzer             │
    │  8. scoring-agent (rewards)     │
    │  9. rl-next-steps-recommender   │
    └────────────────┬────────────────┘
                     ↓
         policy update → biases next run

  ⟲ ai-signal-auditor captures every signal & action across ALL stages
```

---

## 📋 Execution Steps

### Stage 0: Automatic Enhancement Detection

**Input**: Raw feature requirements from user

**Process**:
```
1. Extract feature slug (e.g., "smart-coupon-system")
2. Search for existing artifacts:
   a) BRD files: features/brd/*[slug]*.md
   b) Stories: features/user-stories/*[slug]*.md
   c) Tests: features/test-cases/*[slug]*.md
   d) GitHub issues: Search repo for issues tagged with slug
   e) Keyword analysis: Match terminology in existing artifacts
3. Calculate confidence score (0-100%)
4. Make routing decision based on confidence
5. Log detection result for audit trail
```

**Confidence Scoring**:
```
≥ 70% → Enhancement (High confidence)
40-70% → Ambiguous (Ask user)
< 40% → New Feature (Low confidence - no match)
```

**Routing Decision Logic**:
```javascript
if (confidence >= 70) {
  route = "enhancement-planning";
  user_interaction = "NONE";
  auto_route = true;
} else if (confidence < 40) {
  route = "normal-planning";
  user_interaction = "NONE";
  auto_route = true;
} else {
  // 40-70% → Ambiguous
  user_choice = ask_user(
    "Is this updating an existing feature? (Yes/No)"
  );
  route = user_choice ? "enhancement-planning" : "normal-planning";
  user_interaction = "YES";
  auto_route = false;
}
```

**Output Example**:
```json
{
  "stage": "Stage 0: Auto-Detection",
  "feature_slug": "smart-coupon-system",
  "detected_artifacts": {
    "brd_files": ["smart-coupon-system-v1.0.md"],
    "story_files": 7,
    "test_files": 114,
    "github_issues": 0
  },
  "confidence_score": 85,
  "confidence_breakdown": {
    "brd_match": 50,
    "story_match": 30,
    "test_match": 20,
    "keyword_match": 5
  },
  "route_selected": "enhancement-planning",
  "user_interaction_required": false,
  "decision_rationale": "Existing BRD found with high confidence (85%)",
  "timestamp": "2026-06-11T10:00:00Z"
}
```

---

### Stages 1-6: Execute Chosen Workflow Path

#### Path A: Normal Planning (New Features - Confidence < 40%)

```
Stage 1: BRD Generation
  Input: Feature requirements + user responses
  Output: BRD v1.0 + Assumptions
  Approval: ✅ Required

Stage 2: User Story Building
  Input: BRD + Assumptions
  Output: 7-11 stories + Story Map + Traceability
  Approval: ✅ Required

Stage 3: Functional Testing
  Input: User stories + AC
  Output: 100+ BDD test cases
  Approval: ❌ Not required

Stage 4: GitHub Integration
  Input: Stories + Tests + Traceability
  Output: GitHub issues + Issue map
  Approval: ❌ Not required

Stage 5: Execution Report (AUTO)
  Input: Metadata from all stages
  Output: Comprehensive workflow report
  Approval: ❌ Not required (audit trail)

Stage 7: Result Analysis (AUTO)
  Skill: result-analyzer
  Input: AI signal log + execution report + artifacts
  Output: RESULT-ANALYSIS + findings JSON
  Approval: ❌ Not required

Stage 8: Scoring (AUTO)
  Skill: scoring-agent
  Input: Result analysis + AI signal log + policy state
  Output: SCORECARD + per-action RL rewards (verifier-based)
  Approval: ❌ Not required

Stage 9: RL Next Steps (AUTO)
  Skill: rl-next-steps-recommender
  Input: Scorecard + analysis + policy state
  Output: NEXT-STEPS + updated rl-policy-state.json
  Approval: ⚠️ Only for policy changes touching human gates
```

**Total Duration**: ~6-8 hours (includes approvals)

#### Path B: Enhancement Planning (Existing Features - Confidence ≥ 70%)

```
Stage 1: Enhancement BRD Modifier
  Input: Existing BRD + enhancement requirements
  Output: BRD v[N+1] + Updated Assumptions
  Approval: ❌ Not required (stories need approval)

Stage 2: Enhancement Story Updater
  Input: Updated BRD + existing stories
  Output: Modified + new stories + Story Map
  Approval: ✅ Required (for new stories)

Stage 3: Functional Testing (Updated)
  Input: Modified/new stories + AC
  Output: New + updated test cases
  Approval: ❌ Not required

Stage 4: GitHub Integration (Updated)
  Input: Updated stories + tests
  Output: Updated GitHub issues + Issue map
  Approval: ❌ Not required

Stage 5: Execution Report (AUTO)
  Input: Metadata from all stages
  Output: Comprehensive workflow report
  Approval: ❌ Not required (audit trail)

Stage 7: Result Analysis (AUTO)
  Skill: result-analyzer
  Input: AI signal log + execution report + created/modified artifacts
  Output: RESULT-ANALYSIS + findings JSON (incl. regression & compatibility findings)
  Approval: ❌ Not required

Stage 8: Scoring (AUTO)
  Skill: scoring-agent
  Input: Result analysis + AI signal log + policy state
  Output: SCORECARD + per-action RL rewards (verifier-based)
  Approval: ❌ Not required

Stage 9: RL Next Steps (AUTO)
  Skill: rl-next-steps-recommender
  Input: Scorecard + analysis + policy state
  Output: NEXT-STEPS + updated rl-policy-state.json
  Approval: ⚠️ Only for policy changes touching human gates
```

**Total Duration**: ~4-6 hours (fewer approvals)

---

## 🎯 Detection Examples

### Example 1: Clear Enhancement (Auto-Route)
```
INPUT:
User: "Smart Coupon System - Add push notification delivery channel"

DETECTION:
├─ Extract slug: "smart-coupon-system"
├─ BRD check: ✓ Found (smart-coupon-system-v1.0.md)
├─ Stories check: ✓ Found (7 existing stories)
├─ Tests check: ✓ Found (114 existing tests)
├─ GitHub check: ✗ Not found
├─ Keyword match: ✓ "push", "notification" in stories
│
├─ Score: 50+30+20+0+5 = 105 → Capped at 100%
├─ Confidence: 100% (very high)
├─ Ambiguity: None
│
DECISION:
├─ Route: enhancement-planning ✓
├─ User interaction: NONE ✓
├─ Rationale: "Exact match - existing BRD + stories + tests"
└─ Auto-route: TRUE ✓
```

### Example 2: Brand New Feature (Auto-Route)
```
INPUT:
User: "Referral rewards program with points tracking"

DETECTION:
├─ Extract slug: "referral-rewards"
├─ BRD check: ✗ Not found
├─ Stories check: ✗ Not found
├─ Tests check: ✗ Not found
├─ GitHub check: ✗ Not found
├─ Keyword match: ✗ No matches
│
├─ Score: 0+0+0+0+0 = 0%
├─ Confidence: 0% (no match)
├─ Ambiguity: None
│
DECISION:
├─ Route: normal-planning ✓
├─ User interaction: NONE ✓
├─ Rationale: "No existing artifacts found"
└─ Auto-route: TRUE ✓
```

### Example 3: Ambiguous Case (Ask User)
```
INPUT:
User: "Update loyalty program with new benefits tier"

DETECTION:
├─ Extract slug: "loyalty-program"
├─ BRD check: ✗ Not found
├─ Stories check: ✓ Found (3 stories for "loyalty-member")
├─ Tests check: ✗ Not found
├─ GitHub check: ✗ Not found
├─ Keyword match: ~ Partial ("loyalty", "benefits")
│
├─ Score: 0+30+0+0+2 = 32%
├─ Confidence: 32% (low-medium, ambiguous)
├─ Ambiguity: YES - Could be enhancement OR new feature
│
DECISION:
├─ Route: ASK USER ⚠️
├─ Question: "Is this updating the existing loyalty program?"
├─ User response: "Yes, adding new tier to Smart Coupon System"
├─ Re-route: enhancement-planning ✓
├─ User interaction: YES ✓
└─ Rationale: "Ambiguous - user clarified"
```

---

## 📊 Execution Report Includes

Every workflow run generates an execution report that includes:

**Stage 0 Detection Results**:
```
1. Detection Algorithm: "semantic-match-v1.0"
2. Feature Slug: "smart-coupon-system"
3. Artifacts Found: 7 stories, 114 tests, 1 BRD
4. Confidence Score: 85%
5. Route Selected: enhancement-planning
6. User Interaction: NONE
7. Decision Rationale: "Exact match - existing BRD + stories + tests"
8. Timestamp: 2026-06-11T10:00:00Z
```

**Remaining Stages**: (Depends on route taken)

---

## ✅ Quality Assurance

### Detection Accuracy Monitoring

Track over time:
```
1. Auto-route accuracy: X% of auto-routed decisions are correct
2. Ambiguous cases: Y% of 40-70% confidence cases
3. False positives: Z% of features incorrectly auto-routed
4. User overrides: A% of users override auto-detection
```

### Test Cases for Detection

1. **New Feature Detection**
   - Input: Completely new feature
   - Expected: confidence < 40%, route normal-planning
   - Status: ✓

2. **Enhancement Detection**
   - Input: Update to existing smart-coupon feature
   - Expected: confidence > 70%, route enhancement-planning
   - Status: ✓

3. **Ambiguous Detection**
   - Input: Partially matching feature
   - Expected: confidence 40-70%, ask user
   - Status: ✓

---

## 🔒 Audit Trail

**Skill**: `ai-signal-auditor` — **Standard**: `.github/rules/ai-audit-standards.md`

Every AI signal and every AI action across **all** stages (0-9) is captured as it happens in an
append-only event stream:

```
features/audit/ai-signal-log-{RUN_ID}.jsonl   # signals, actions, human gates, errors, rewards
features/audit/ai-action-audit-{RUN_ID}.md    # human-readable audit summary
features/audit/audit-index.json               # index of every run
```

Each event records:
- **Signal** — what was detected, evidence, confidence breakdown
- **Action** — what was done, rationale, rules applied, rejected alternatives, autonomy level
- **Human gate** — question asked, answer, override (with the AI's original proposal)
- **LLM** — model, settings, input/output tokens
- **Outcome** — status, artifacts, errors, duration
- **Reward** — back-filled in Stage 8 from deterministic verifiers

**Rule**: No silent AI action. An artifact with no matching audit event is a Stage 7 finding,
and an incomplete audit fails the Stage 6 quality gate.

This enables:
- Post-analysis of detection accuracy (Stage 7)
- Verifiable, explainable scoring of every run (Stage 8)
- Reinforcement learning to improve the routing/planning policy (Stage 9)
- Compliance & audit requirements (traceability of AI-authored artifacts)
- Decision traceability from requirement → story → test → issue → policy change

---

## 🔁 Reinforcement Learning Loop (Stages 7-9)

**Standard**: `.github/rules/agentic-rl-standards.md`

```
Episode  = one workflow run (RUN_ID)
State    = feature slug, existing artifacts, confidence band, stage, complexity, budget
Action   = every AI action captured in the audit log
Reward   = deterministic verifiers (coverage, conformance, audit, efficiency, alignment)
Policy   = features/analysis/rl-policy-state.json (explicit, versioned, human-reviewable)
```

Key guardrails (no model weights are changed):
- **Verifiable rewards only** — no LLM grading its own work; verifiers are agent-read-only
- **Group-relative advantage** — a run is judged against the last 5 comparable runs
- **Bounded updates** — `α = 0.2`, `γ = 0.8`, max ±5 confidence points per run, `visits ≥ 3`
- **Off-policy evaluation** — proposed changes replayed on logged episodes before activation
- **Human overrides weighted 2×** and approval-gate changes never auto-apply
- **Anti-reward-hacking** — volume never scores; untraceable artifacts are penalised
- **Rollback** — a change followed by two negative-advantage runs is reverted

---

## 🚀 Benefits of Auto-Detection

| Benefit | Before | After |
|---------|--------|-------|
| User interaction | Ask which workflow | Auto-detect |
| Execution time | +2 min (user choice) | Auto (no delay) |
| Error rate | Higher (wrong choice) | Lower (intelligent) |
| Audit trail | Manual | Automatic |
| Consistency | Depends on user | Deterministic |
| Scalability | Manual → bottleneck | Auto → scalable |

---

## 📚 Related Documents

- `WORKFLOW-AUTO-DETECTION-DESIGN.md` - Detailed design & algorithm
- `normal-planning.md` - New feature workflow
- `enhancement-planning.md` - Enhancement workflow
- `.github/rules/ai-audit-standards.md` - AI signal & action audit schema and rules
- `.github/rules/agentic-rl-standards.md` - Agentic RL standards (rewards, policy, guardrails)
- Enhancement Detector Skill: `.github/skills/enhancement-detector/SKILL.md`
- AI Signal Auditor Skill: `.github/skills/ai-signal-auditor/SKILL.md`
- Result Analyzer Skill: `.github/skills/result-analyzer/SKILL.md`
- Scoring Agent Skill: `.github/skills/scoring-agent/SKILL.md`
- RL Next-Steps Recommender Skill: `.github/skills/rl-next-steps-recommender/SKILL.md`

---

**Status**: ✅ LIVE - Auto-Detection Enabled  
**Last Updated**: June 11, 2026  
**Implemented By**: Planning Workflow v2.0
