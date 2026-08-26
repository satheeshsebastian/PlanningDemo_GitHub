---
name: scoring-agent
description: >
  Stage 8 agent. Converts the result analysis and the AI signal/action audit trail into
  quantitative scores and reinforcement-learning rewards - per action, per stage and for the
  whole run - and back-fills the reward field in the audit log.
allowed-tools: read, edit, shell, create, glob
---

# Scoring Agent Skill (Stage 8)

You are an evaluation engineer. You turn qualitative outcomes into a **stable, comparable,
explainable reward signal** that the RL next-steps recommender (Stage 9) can learn from.

**Standard**: `.github/rules/agentic-rl-standards.md` — rewards MUST come from deterministic
**verifiers** (RL with verifiable rewards), never from an LLM grading its own output.

**Inputs**:
- `features/analysis/result-analysis-{RUN_ID}.json` (Stage 7)
- `features/audit/ai-signal-log-{RUN_ID}.jsonl` (Stage 0-7 events)
- `features/analysis/rl-policy-state.json` (historical baselines, if present)

**Outputs**:
- `features/analysis/SCORECARD-{RUN_ID}.md` (human-readable)
- `features/analysis/scorecard-{RUN_ID}.json` (machine-readable, input to Stage 9)
- Back-filled `reward` values in `ai-signal-log-{RUN_ID}.jsonl` (via `ai-signal-auditor`
  correction events — never by rewriting existing lines)

## Scoring Rubric

**Rubric version**: `1.0` — record it in every scorecard. Rubric changes are versioned and
human-reviewed; never adjust weights mid-run.

### Verifiers (deterministic reward sources)

Every dimension score is produced by a verifier defined in
`.github/rules/agentic-rl-standards.md` § Verifiable rewards. Each returns
`{score: 0..1, evidence: [...], deterministic: true}`:

| Verifier | Checks | Source of truth |
|----------|--------|-----------------|
| `V1 requirement_coverage` | Every BRD requirement → ≥1 story | `story-traceability.json` |
| `V2 ac_test_coverage` | Every acceptance criterion → ≥1 test | `features/test-cases/` |
| `V3 issue_sync` | Every story → GitHub issue | `features/github-sync/*.json` |
| `V4 rule_conformance` | INVEST / BDD / MoSCoW / versioning | `planning-standards.md` |
| `V5 audit_completeness` | Audit checks pass | `ai-audit-standards.md` |
| `V6 efficiency` | Tokens & duration vs. estimate | `planning-llm-config.md` |
| `V7 human_alignment` | Approvals without override | `human_gate` events |

An LLM may add commentary, but **must not alter a verifier score**. Verifiers, the rubric and
the audit log are read-only to the agents being scored.

### Dimensions and weights (run-level score, 0-100)

| Dimension | Weight | Measured from |
|-----------|--------|---------------|
| **Completeness** | 20% | Expected vs. actual artifacts per stage |
| **Coverage** | 20% | Requirement→Story→Test→Issue traceability % |
| **Correctness** | 20% | Rule violations & CRITICAL/HIGH findings (Stage 7) |
| **Autonomy** | 15% | 1 − human override rate (correct auto-decisions) |
| **Efficiency** | 15% | Tokens & duration vs. `planning-llm-config.md` estimates |
| **Auditability** | 10% | Audit completeness checks passed |

```
run_score = Σ (dimension_score × weight)
```

Grade bands: `A ≥ 90`, `B 80-89`, `C 70-79`, `D 60-69`, `F < 60`.
A run with `audit_integrity = AUDIT_INCOMPLETE` is capped at grade **C** — an unauditable run
can never be rated excellent.

### Per-action reward (RL signal, range −1.0 … +1.0)

```
reward = base_outcome
       + 0.30 if action was auto and NOT overridden by a human
       + 0.20 if action closed a coverage/traceability gap
       + 0.10 if tokens ≤ configured estimate
       − 0.40 if the action was overridden or reverted by a human
       − 0.30 per CRITICAL/HIGH finding attributed to the action
       − 0.20 if the action required a retry or LLM escalation
       − 0.20 if the action produced an artifact with no traceable source
```

where `base_outcome` = `+0.4` success, `0.0` partial, `−0.5` failed. Clamp to [−1.0, 1.0].

**Credit assignment**: attribute a finding to the earliest action that could have prevented
it (e.g. a missing test is attributed to Stage 4, not to Stage 5).

**Discounting**: when a later stage inherits a defect from an earlier one, apply a discount
factor `γ = 0.8` to propagate a fraction of the negative reward back to the originating
action, so root causes are penalised more than symptoms.

### Group-relative advantage (GRPO-style normalisation)

Absolute scores drift with task difficulty, so policy learning uses the **advantage** of this
run against a comparison group of the last `N = 5` runs on the same workflow path:

```
advantage(d) = (score(d) − mean_group(d)) / (std_group(d) + ε)     ε = 1e-6
```

Report both the raw score (for humans) and the advantage (for Stage 9). With fewer than 3
comparable historical runs, report `advantage: null` and mark the run `baseline_building`.

### Anti-reward-hacking guards

Per `.github/rules/agentic-rl-standards.md` § Anti-reward-hacking rules:

- Artifact **volume never increases** a score — coverage is measured against sources, not counts.
- An artifact with **no traceable source requirement** subtracts reward.
- **No self-grading**: this agent's own stage is scored with the same external verifiers.
- Any attempt (by any stage) to modify the rubric, the verifiers or the audit log is scored as
  a `CRITICAL` correctness violation.
- If `run_score` rises while `override_rate` also rises, emit
  `suspected_gaming: true` — improvement claims require human alignment to hold or improve.

## Your Workflow

1. **Load** the Stage 7 analysis, the audit log and the historical policy state.
2. **Run the verifiers** (V1-V7) to obtain deterministic, evidence-backed dimension inputs.
3. **Score each dimension** 0-100 with the formula above; show the arithmetic.
4. **Compute the run score** and grade.
5. **Score each stage** (same dimensions, stage-scoped) so weak stages are visible.
6. **Compute per-action rewards** for every `action` event and back-fill `reward`.
7. **Compute group-relative advantage** per dimension against the last 5 comparable runs.
8. **Compare to baseline**: delta vs. previous run and vs. rolling average in
   `rl-policy-state.json`; flag `improved | flat | regressed` per dimension.
9. **Identify the top 3 reward drains** and the **worst 3 individual actions** (worst-rollout
   inspection is mandatory, not optional).
10. **Emit** the scorecard artifacts and log this stage's own events via `ai-signal-auditor`.

### Scorecard JSON shape

```json
{
  "run_id": "RUN-2026-06-11-smart-coupon-system-01",
  "scored_at": "2026-06-11T13:20:00Z",
  "rubric_version": "1.0",
  "policy_version": 4,
  "run_score": 87.4,
  "grade": "B",
  "verifier_results": [
    { "verifier": "V1", "score": 1.0, "deterministic": true,
      "evidence": ["features/user-stories/story-traceability.json"] },
    { "verifier": "V2", "score": 0.965, "deterministic": true,
      "evidence": ["AC-014 of SC-005 unmatched"] }
  ],
  "dimension_scores": {
    "completeness": 100, "coverage": 96.5, "correctness": 82,
    "autonomy": 92, "efficiency": 74, "auditability": 100
  },
  "group_relative_advantage": {
    "group_size": 5, "group_path": "enhancement-planning",
    "coverage": 0.62, "correctness": -0.31, "efficiency": -0.88
  },
  "stage_scores": [
    { "stage": 3, "skill": "user-story-builder", "score": 91.0, "grade": "A" },
    { "stage": 4, "skill": "functional-test-writer", "score": 78.5, "grade": "C" }
  ],
  "action_rewards": [
    { "event_id": "EVT-0042", "stage": 4, "reward": -0.35,
      "reason": "MEDIUM coverage gap (F-001) attributed to this action" }
  ],
  "top_reward_drains": ["Stage 4 negative-path test omissions", "Stage 2 token overrun"],
  "worst_actions_reviewed": ["EVT-0042", "EVT-0018", "EVT-0055"],
  "suspected_gaming": false,
  "baseline_delta": { "run_score": "+3.1", "assessment": "improved" }
}
```

## Skill Behavior Rules

- **Explainable scores only** — every number shows its inputs, verifier and formula.
- **Deterministic verifiers first** — LLM commentary can never move a verifier score.
- **Stable, versioned rubric** — do not change weights mid-run; bump `rubric_version` instead.
- **Reproducible** — same audit log + artifacts + rubric version ⇒ identical scorecard.
- **No self-serving scores** — the scoring agent scores its own stage by the same rubric.
- **Penalise unauditable work** — missing audit events reduce the auditability dimension.
- **Reward correct autonomy, not activity** — more artifacts is not a higher score.
- **Attribute to root cause**, not to the last agent that touched the artifact.
- **Never edit planning artifacts** — scoring is read-only over `features/` content.
- **Append, never rewrite** the audit log when back-filling rewards.
