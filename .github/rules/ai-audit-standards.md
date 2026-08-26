# AI Signal & Action Audit Standards

**Version**: 1.0
**Status**: Active - MANDATORY for every planning workflow run
**Applies To**: All stages (0-9) of `new-feature-planning` and `enhancement-planning`

---

## 🎯 Purpose

Every planning workflow run is executed by AI agents (skills). To make those runs
auditable, reproducible and improvable, **every AI signal and every AI action must be
captured** in a durable audit trail.

The audit trail answers five questions for every step:

1. **What did the AI see?** (signal — input, evidence, retrieved artifacts)
2. **What did the AI do?** (action — tool call, file write, GitHub call, question asked)
3. **Why did it do it?** (rationale — confidence, rule applied, alternatives rejected)
4. **What happened?** (outcome — artifacts, errors, human approval/override)
5. **How good was it?** (reward — score assigned in Stage 8, used by Stage 9)

---

## 📐 Standards Alignment

The schema and rules below follow established observability, provenance and AI-governance
practice rather than any single vendor's approach:

| Practice | Adopted here |
|----------|--------------|
| **OpenTelemetry GenAI semantic conventions** (spans/events for model calls, tools, tokens) | `llm` block on every event: model, settings, input/output tokens; one event per tool action with duration and status |
| **W3C PROV-O provenance** (entity / activity / agent) | Every artifact (entity) links to the action (activity) and the skill (agent) that produced it |
| **Structured, append-only event logging** (JSONL, immutable, correlation IDs) | `run_id` + sequential `event_id`; corrections are new events, never edits |
| **NIST AI RMF (Measure/Manage), ISO/IEC 42001, EU AI Act art. 12** | Complete traceability of AI-authored artifacts, recorded human oversight, retention with the run artifacts |
| **Agent-trajectory evaluation practice** (evaluate the path, not just the answer) | Signals, rejected alternatives and human gates are logged so the whole trajectory can be replayed |
| **Privacy-by-design / data minimisation** | Log references (paths, issue numbers), never secrets or personal data; redact free text |

---

## 📁 Audit Artifacts (per run)
```
features/audit/
├── ai-signal-log-{RUN_ID}.jsonl      # Append-only event stream (one JSON per line)
├── ai-action-audit-{RUN_ID}.md       # Human-readable audit summary
└── audit-index.json                  # Index of all runs (append one entry per run)
```

`RUN_ID` format: `RUN-{YYYY-MM-DD}-{feature-slug}-{NN}` (e.g. `RUN-2026-06-11-smart-coupon-system-01`).

**Rules**:
- The signal log is **append-only** — never rewrite or delete previous events.
- Write the event **at the moment it happens**, not at the end of the run.
- If a stage fails, still write the event with `"outcome.status": "failed"`.
- Audit files are committed to version control with the rest of the run artifacts.

---

## 🧾 Event Schema (`ai-signal-log-{RUN_ID}.jsonl`)

One JSON object per line:

```json
{
  "event_id": "EVT-0007",
  "run_id": "RUN-2026-06-11-smart-coupon-system-01",
  "timestamp": "2026-06-11T10:14:22Z",
  "stage": 3,
  "stage_name": "User Story Building",
  "skill": "user-story-builder",
  "event_type": "action",
  "signal": {
    "type": "artifact_read",
    "source": "features/brd/smart-coupon-system-v1.0.md",
    "summary": "47 functional requirements, 5 assumptions",
    "confidence": 0.88,
    "evidence": ["FR-012", "FR-013", "ASSUMPTION-002"]
  },
  "action": {
    "type": "file_write",
    "target": "features/user-stories/member-receives-smart-coupons-by-email.md",
    "parameters": { "story_id": "SC-003", "points": 8, "moscow": "MUST" },
    "rationale": "FR-012/FR-013 form one independently deliverable slice (INVEST)",
    "alternatives_considered": ["Merge with SC-004 (rejected: not independent)"],
    "rules_applied": ["INVEST validation", "MoSCoW classification"],
    "autonomy": "auto"
  },
  "llm": {
    "model": "Claude Sonnet 4.6",
    "temperature": 0.7,
    "input_tokens": 4205,
    "output_tokens": 5120
  },
  "human": {
    "interaction_required": true,
    "prompt": "Approve 7 generated stories?",
    "response": "approved",
    "override": false
  },
  "outcome": {
    "status": "success",
    "duration_seconds": 52,
    "artifacts": ["features/user-stories/member-receives-smart-coupons-by-email.md"],
    "errors": []
  },
  "reward": null
}
```

`reward` is left `null` at capture time and is back-filled by the **scoring-agent** (Stage 8).

### Field rules

| Field | Required | Notes |
|-------|----------|-------|
| `event_id` | ✅ | Sequential per run: `EVT-0001`, `EVT-0002`, … |
| `run_id` | ✅ | Same for all events in a run |
| `timestamp` | ✅ | ISO-8601 UTC |
| `stage` / `stage_name` / `skill` | ✅ | Which agent produced the event |
| `event_type` | ✅ | `signal` \| `action` \| `decision` \| `human_gate` \| `error` \| `score` |
| `signal` | ✅ for `signal`/`decision` | What the AI observed |
| `action` | ✅ for `action` | What the AI did |
| `llm` | ✅ when an LLM call occurred | Model, settings, tokens |
| `human` | ✅ when a gate/question occurred | Approval, override, free-text |
| `outcome` | ✅ | Status + artifacts + errors |
| `reward` | ⬜ | Filled by scoring-agent |

---

## 🔢 Signal Types (controlled vocabulary)

| Signal type | Captured when |
|-------------|---------------|
| `user_requirement` | Raw user input received |
| `artifact_read` | Existing BRD/story/test/report loaded |
| `artifact_search` | glob/grep search performed |
| `github_read` | Issues/PRs/labels queried |
| `confidence_score` | Detector or validator produced a score |
| `rule_check` | A planning standard was evaluated |
| `anomaly` | Missing artifact, contradiction, coverage gap |
| `human_feedback` | Approval, rejection, correction, override |

## ⚙️ Action Types (controlled vocabulary)

| Action type | Captured when |
|-------------|---------------|
| `route_decision` | Stage 0 chose normal vs enhancement path |
| `question_asked` | AI asked the user something |
| `file_write` / `file_update` / `file_deprecate` | Artifact created/changed |
| `github_issue_create` / `github_issue_update` | GitHub mutation |
| `version_bump` | BRD version changed |
| `escalation` | Switched to secondary LLM |
| `retry` | Re-ran a failed step |
| `report_generate` | Execution report / analysis / scorecard written |

### Autonomy levels

- `auto` — taken without human confirmation
- `confirmed` — human approved before execution
- `overridden` — human changed the AI's proposal (**always capture the original proposal**)

---

## 🔒 Capture Rules (MANDATORY)

1. **No silent AI action.** Every file write, GitHub mutation, routing decision, and user
   question emits at least one event.
2. **Capture the signal before the action** so cause and effect are traceable.
3. **Capture rejected alternatives** — they are the negative examples the RL loop learns from.
4. **Capture every human gate** verbatim (question, answer, override, timestamp). Human
   overrides are the highest-value learning signal.
5. **Capture errors and retries** — failed runs must be as auditable as successful ones.
6. **Never log secrets or credentials.** Log the *reference* (issue number, file path), not
   tokens, keys, or personal data.
7. **Redact free-text** that contains PII before writing to the log.
8. **Audit log completeness is a Stage 6 quality check** — the execution report must fail its
   quality gate if the signal log is missing or has stage gaps.

---

## ✅ Audit Completeness Checks

Run at the end of Stage 6 (execution report) and again in Stage 7 (result analyzer):

```
✓ ai-signal-log-{RUN_ID}.jsonl exists and is valid JSONL
✓ Every executed stage (0-9) has ≥ 1 event
✓ Every artifact in features/ created by the run appears in some event's outcome.artifacts
✓ Every human gate in the workflow has a matching human_gate event
✓ Every LLM call has token counts recorded
✓ No event is missing event_id / timestamp / outcome.status
✓ No secrets present in the log
✓ audit-index.json contains an entry for this run
```

---

## 🔁 Downstream Consumers

| Consumer | Uses the audit trail for |
|----------|--------------------------|
| Stage 6 `execution report` | Timeline, token usage, decisions |
| Stage 7 `result-analyzer` | Outcome vs. expectation analysis, anomaly detection |
| Stage 8 `scoring-agent` | Reward computation per action (`reward` back-fill) |
| Stage 9 `rl-next-steps-recommender` | Policy update + next-best-action recommendations |
| Compliance / audit | Full decision traceability of AI-authored artifacts |

---

**Owner**: Planning Workflow Team
**Last Updated**: 2026-06-11
**Related**: `.github/rules/agentic-rl-standards.md` (how rewards are derived from this log)
