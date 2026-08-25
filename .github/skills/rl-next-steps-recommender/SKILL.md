---
name: rl-next-steps-recommender
description: >
  Stage 9 agent. Applies a reinforcement-learning loop (state, action, reward, policy update)
  over the scored audit trail to update the workflow policy and recommend the next best
  actions for the team and for future workflow runs.
allowed-tools: read, edit, shell, create, glob
---

# RL Next-Steps Recommender Skill (Stage 9)

You close the loop: **signals → actions → rewards → policy → next steps**.

**Standard**: `.github/rules/agentic-rl-standards.md` — offline, in-context agentic RL with
verifiable rewards, group-relative advantage, bounded (trust-region) policy updates, off-policy
evaluation before activation, and human oversight as ground truth. No model weights are changed;
the "policy" is an explicit, human-reviewable configuration file.

**Inputs**:
- `features/analysis/scorecard-{RUN_ID}.json` (Stage 8 rewards)
- `features/analysis/result-analysis-{RUN_ID}.json` (Stage 7 findings)
- `features/audit/ai-signal-log-{RUN_ID}.jsonl` (state/action context)
- `features/analysis/rl-policy-state.json` (accumulated policy — created on first run)

**Outputs**:
- `features/analysis/NEXT-STEPS-{RUN_ID}.md` (recommendations for humans)
- `features/analysis/rl-policy-state.json` (updated policy for future runs)

## The RL Formulation

| RL concept | Planning-workflow meaning |
|------------|---------------------------|
| **Environment** | The planning workflow over the `features/` artifact repository |
| **Episode** | One workflow run (`RUN_ID`), Stage 0 → Stage 9 |
| **State `s`** | Context at a decision point: feature slug, existing artifacts, confidence score, stage, complexity, token budget, prior override history |
| **Action `a`** | The AI action taken (route decision, question asked, artifact written, escalation, version bump…) |
| **Reward `r`** | Per-action reward from the scoring-agent, produced by deterministic verifiers (−1.0 … +1.0) |
| **Return `G`** | Discounted sum of rewards to the end of the episode (`γ = 0.8`) |
| **Advantage `A`** | Group-relative advantage vs. the last 5 comparable runs (GRPO-style) |
| **Policy `π(a|s)`** | Preference weights stored in `rl-policy-state.json` that bias future decisions |
| **Trust region** | Bounded updates (`α = 0.2`, ±5 confidence points, `visits ≥ 3`) that keep the policy near the last known-good one |
| **Exploration** | Deliberately trying an alternative action on low-confidence states (`ε = 0.1`) |
| **Off-policy evaluation** | Counterfactual replay of a proposed change over logged past episodes before it is activated |

## Your Workflow

### Step 1: Build the episode trace
From the audit log + scorecard, produce the `(state, action, reward, next_state)` tuples for
every decision point of the run. Bucket states so they generalise (e.g. confidence bands
`<40 / 40-70 / ≥70`, complexity `simple / complex`, path `new / enhancement`).

### Step 2: Compute returns and advantages
For each tuple compute the discounted return `G_t = r_t + γ·r_{t+1} + γ²·r_{t+2} + …`
so early decisions inherit credit/blame for their downstream consequences.

Then normalise against the comparison group from the scorecard
(`group_relative_advantage`, last 5 runs of the same workflow path) so that changes in task
difficulty are not mistaken for policy improvement or regression. Policy updates use the
**advantage-adjusted return**, not the raw return.

### Step 3: Update the policy (incremental, conservative, trust-region)
For each `(state_bucket, action)` pair update the value estimate:

```
Q(s,a) ← Q(s,a) + α · [ G_adv − Q(s,a) ]     with learning rate α = 0.2
visits(s,a) ← visits(s,a) + 1
```

**Guardrails** (per `.github/rules/agentic-rl-standards.md`):
- Never let a single episode flip a rule — require `visits ≥ 3` before a policy change is
  marked `active`; below that it is `candidate`.
- Human overrides count as **corrective feedback with double weight** — they are the strongest
  signal available and act as the ground truth for alignment.
- Any policy change that would reduce a human approval gate must be marked
  `requires_human_approval: true` and can never auto-activate.
- Cap the confidence-threshold movement at ±5 points per run (trust region).
- Never modify the rubric, the verifiers, `.github/rules/*` or the audit log.

### Step 3b: Off-policy evaluation before activation
For each candidate change, replay it counterfactually against the **logged historical
episodes** in `features/audit/` and estimate the uplift it would have produced (importance-
weighted / doubly-robust style estimate over comparable state buckets). Record:

```
{ "estimated_uplift": +2.4, "supporting_episodes": 6, "confidence": "medium" }
```

A change activates only when `visits ≥ 3` **and** the estimated uplift is positive; otherwise
it stays `candidate`. Unsupported or negative estimates are reported, not applied.

### Step 4: Derive the recommended policy adjustments
Translate high/low `Q` values into concrete, reviewable proposals, e.g.:
- adjust Stage 0 routing thresholds (within the ±5 cap)
- add/strengthen a rule in a skill checklist (e.g. mandatory negative-path tests)
- change the primary/secondary LLM for a stage that repeatedly escalates
- add a clarifying question that historically prevents overrides
- remove a question that never changes the outcome (reduces friction)

### Step 5: Choose the next best actions (exploit + explore)
Rank next steps by expected value = `Q(s,a) × impact × confidence`:

1. **Immediate (this run)** — fix the CRITICAL/HIGH findings from Stage 7
2. **Next run (workflow policy)** — the `active` policy adjustments
3. **Backlog (feature delivery)** — sprint/rollout actions from the artifacts produced
4. **Exploration (ε = 0.1)** — one deliberate experiment on a low-confidence state, with the
   hypothesis and the metric that will judge it in the next run

### Step 6: Emit `NEXT-STEPS-{RUN_ID}.md`

```
1. Learning Summary        — run score, grade, group-relative advantage, top reward drains
2. Worst-Rollout Review    — the 3 lowest-reward actions, what went wrong, prevention
3. Policy Updates Applied  — table: state bucket | action | old Q | new Q | visits | status
4. Policy Updates Proposed — candidates + off-policy uplift estimate, awaiting human approval
5. Next Best Actions       — Immediate / Next run / Backlog, each with owner + expected reward
6. Exploration Experiment  — hypothesis, action, success metric, review date
7. Open Risks & Regressions — dimensions trending down, rollback candidates, gaming suspicions
8. Evidence Appendix       — event_ids, verifier results and rewards backing every recommendation
```

### Step 7: Persist `rl-policy-state.json`

```json
{
  "policy_version": 4,
  "rubric_version": "1.0",
  "updated_at": "2026-06-11T13:40:00Z",
  "learning_rate": 0.2,
  "discount_factor": 0.8,
  "exploration_rate": 0.1,
  "max_threshold_delta_per_run": 5,
  "min_visits_to_activate": 3,
  "human_override_weight": 2.0,
  "episodes": 12,
  "q_values": [
    {
      "state_bucket": "confidence>=70|enhancement|complex",
      "action": "auto_route_enhancement_planning",
      "q": 0.71, "visits": 9, "status": "active"
    },
    {
      "state_bucket": "confidence 40-70|ambiguous",
      "action": "ask_user_single_clarifying_question",
      "q": 0.44, "visits": 4, "status": "active"
    }
  ],
  "rolling_baselines": {
    "run_score": 84.9, "coverage_percent": 95.1, "override_rate": 0.11,
    "group_window": 5
  },
  "pending_approval": [
    {
      "change": "Raise ambiguity lower bound 40 → 45",
      "evidence": ["RUN-2026-06-11-...-01/F-003"],
      "off_policy_estimate": { "estimated_uplift": 2.4, "supporting_episodes": 6,
                               "confidence": "medium" },
      "requires_human_approval": true
    }
  ],
  "change_history": [
    { "policy_version": 3, "change": "Add negative-path test rule",
      "activated_at": "2026-06-04T09:00:00Z", "status": "active",
      "post_change_advantage": [0.31, 0.44] }
  ]
}
```

### Step 8: Log your own work
Emit this stage's signals and actions through `ai-signal-auditor`, including every policy
change made or proposed — policy mutation is itself an auditable AI action.

## Skill Behavior Rules

- **Learn from rewards, not opinions** — every recommendation traces to a reward and evidence.
- **Small, reversible policy steps** — bounded updates, versioned policy, full history kept.
- **Humans stay in the loop** — approval-gate changes are proposals, never auto-applied.
- **Weight human corrections highest** — overrides are the ground truth.
- **Explore deliberately, not accidentally** — one labelled experiment per run, with a metric.
- **Prevent reward hacking** — never recommend actions that inflate scores without improving
  artifacts (e.g. generating extra tests with no acceptance-criteria source); never propose
  changes to the rubric, the verifiers or the audit log.
- **Detect regression** — if a previously applied policy change is followed by two runs with
  negative group-relative advantage on its target dimension, recommend rollback and mark it
  `reverted`.
- **Inspect failures, not just averages** — the worst rollouts of every run must be reviewed.
- **Never edit planning artifacts** — Stage 9 recommends; the next run executes.
