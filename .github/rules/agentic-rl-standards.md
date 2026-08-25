# Agentic Reinforcement Learning Standards

**Version**: 1.0
**Status**: Active - governs Stage 7 (result-analyzer), Stage 8 (scoring-agent) and
Stage 9 (rl-next-steps-recommender)

---

## 🎯 Scope

This planning workflow does **not** fine-tune model weights. It runs an **offline, in-context
agentic RL loop**: episodes are workflow runs, rewards come from verifiers over the produced
artifacts, and "policy" is an explicit, human-reviewable configuration
(`features/analysis/rl-policy-state.json`) that biases future agent decisions.

The standards below are drawn from multiple sources — no single vendor — and each is mapped to
a concrete rule in this repository.

---

## 📚 Practice Sources → Rules Applied Here

| Source / practice | Adopted rule in this workflow |
|-------------------|-------------------------------|
| **Sutton & Barto, RL: An Introduction** (MDP formulation, TD learning, credit assignment, exploration/exploitation) | Explicit `(state, action, reward, next_state)` episode trace; discounted returns with `γ`; incremental value update with learning rate `α`; ε-greedy exploration |
| **NVIDIA agentic RL practice** (environment rollouts, RL with *verifiable* rewards, GRPO-style group-relative advantage, verifier isolation, failure inspection) | Deterministic verifiers over artifacts; **group-relative advantage** normalises a run against a baseline group of comparable runs instead of an absolute score; verifiers live outside agent-writable artifacts; mandatory inspection of the worst rollouts |
| **RLHF / RLAIF practice** (OpenAI, Anthropic; human preference as ground truth, KL/trust-region style constraint to stay near a known-good policy) | Human overrides weighted double; bounded policy steps (±5 confidence points, `α ≤ 0.2`) act as a trust region; no approval gate may be removed without human sign-off |
| **Reward-hacking / specification-gaming literature** (DeepMind, Anthropic, Amodei et al. *Concrete Problems in AI Safety*) | Anti-gaming rules: volume never increases score; every artifact must trace to a source requirement; agents may not modify the rubric, the verifiers or the audit log |
| **Off-policy evaluation** (inverse propensity scoring / doubly-robust estimation) | Proposed policy changes are estimated against *logged* historical episodes before activation; a change needs `visits ≥ 3` and a positive estimated uplift to become `active` |
| **Multi-armed bandit practice** (regret-aware exploration, one change at a time) | Exactly one labelled exploration experiment per run, with a pre-declared hypothesis and success metric |
| **MLOps / experiment tracking** (model & experiment cards, seeds, reproducibility) | Versioned rubric + versioned policy, full change history, deterministic re-scoring of the same inputs |
| **Google/industry agent-evaluation practice** (trajectory evaluation, not just final answer) | Stage 7 evaluates the whole trajectory (signals, tool actions, gates), not only final artifacts |
| **OpenTelemetry GenAI semantic conventions** (spans/events for model calls, tokens, tools) | Audit event schema records model, settings, token counts, tool action and outcome per step |
| **W3C PROV-O provenance model** (entity / activity / agent) | Every artifact links to the action that produced it and the agent that performed it |
| **NIST AI RMF, ISO/IEC 42001, EU AI Act art. 12 logging** | Append-only, immutable, complete audit trail; human oversight recorded; unauditable runs cannot be graded above C |

---

## 🔬 Core Requirements

### 1. Verifiable rewards (RLVR)
Rewards MUST be computed from **deterministic, inspectable verifiers**, not from an LLM's
opinion of its own work:

```
V1 requirement_coverage   : every BRD requirement maps to ≥1 story        (traceability file)
V2 ac_test_coverage       : every acceptance criterion maps to ≥1 test    (test files)
V3 issue_sync             : every story maps to a GitHub issue            (issue map)
V4 rule_conformance       : INVEST / BDD / MoSCoW / versioning checks     (planning-standards)
V5 audit_completeness     : audit checks pass                             (ai-audit-standards)
V6 efficiency             : tokens & duration vs. configured estimates    (planning-llm-config)
V7 human_alignment        : approvals without override                    (human_gate events)
```

Each verifier returns `{score: 0..1, evidence: [...], deterministic: true}`.
An LLM judge MAY add commentary but MUST NOT change a verifier score.

### 2. Verifier & rubric isolation
- Verifiers, the scoring rubric and the audit log are **not** agent-writable content.
- No agent may edit `.github/rules/*` as part of executing a run.
- Rubric changes are versioned proposals reviewed by humans, never silent edits.

### 3. Group-relative advantage (GRPO-style)
Score a run relative to a comparison group rather than in isolation:

```
group = last N=5 runs of the same workflow path (new vs. enhancement)
advantage(dimension) = (score − mean_group) / (std_group + ε)
```

Use the advantage — not the raw score — to drive policy updates, so drift in task difficulty
does not masquerade as improvement or regression.

### 4. Credit assignment & discounting
Attribute reward to the **earliest action that could have prevented the outcome**; propagate a
discounted share (`γ = 0.8`) back along the trajectory. Symptoms are penalised less than causes.

### 5. Bounded, reviewable policy updates (trust region)
```
α (learning rate)        = 0.2        # incremental, never a full overwrite
γ (discount)             = 0.8
ε (exploration)          = 0.1        # exactly one experiment per run
max Δ confidence threshold = ±5 points per run
min visits before `active` = 3
human override weight      = 2×
```
Any change touching a human approval gate → `pending_approval`, never auto-applied.

### 6. Anti-reward-hacking rules
- **Volume is not value** — more stories/tests/issues never raises a score by itself.
- **Untraceable artifacts are penalised**, not rewarded.
- **No self-grading** — an agent's score for its own stage uses the same external verifiers.
- **No rubric or log tampering** — attempts are `CRITICAL` findings.
- **Cross-check with human signals** — a rising score with a rising override rate is flagged
  as suspected gaming, not as improvement.
- **Inspect the worst rollouts** — the lowest-reward actions of every run must be reviewed in
  Stage 7/9 output, not just the aggregates.

### 7. Offline evaluation before activation
Before a proposed policy change is activated, replay it against logged historical episodes
(counterfactual/off-policy estimate) and record the estimated uplift, the number of supporting
episodes and the confidence. Negative or unsupported estimates stay `candidate`.

### 8. Regression detection & rollback
If an activated change is followed by two consecutive runs with negative group-relative
advantage on its target dimension, Stage 9 MUST recommend rollback and mark it `reverted`.

### 9. Reproducibility
Same audit log + same artifacts + same rubric version ⇒ same scores and same recommendations.
Record `rubric_version` and `policy_version` in every scorecard and next-steps artifact.

### 10. Human oversight
Humans remain the ground truth: they can approve, reject, override or roll back any policy
change. Every such intervention is captured as a `human_gate` audit event and fed back as a
double-weighted learning signal.

---

**Owner**: Planning Workflow Team
**Last Updated**: 2026-06-11
**Related**: `.github/rules/ai-audit-standards.md`, `.github/rules/planning-standards.md`,
`.github/rules/planning-llm-config.md`
