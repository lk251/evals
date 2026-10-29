# Internal Evals System — Specification v0.1

**Date:** 2026-10-29  
**Status:** Initial design / not implemented  
**Owner:** Mares Engineering  
**Target:** all accessible `lk251` and `mares-engineering` GitHub projects, with automatic discovery of additions.

## 1. Problem and decision

Choose a **model, provider, inference mode and agent policy** for each *task family* using evidence from actual repository work. The primary objective is **lowest expected total EUR cost per accepted, safe outcome**, subject to hard quality/reliability/time constraints. External benchmarks are priors and candidate-discovery signals, not deployment authority.

Do **not** optimize a generic intelligence-score / price ratio: a cheap model with a 5% completion rate may be more expensive after retries, recovery and human supervision. Model rankings are contextual, uncertain and time-dependent.

### 1.1 Candidate identity

A candidate is a fully specified executable policy, not just a model name:

```yaml
candidate_id: glm-local-opencode-v1
model:
  provider: local
  identifier: example-model-and-checkpoint  # placeholder
  quantization: null
  endpoint: null
harness:
  name: example-agent-harness
  version: pinned
  tool_permissions: ["read", "edit", "test"]
prompt_version: sha256:...
policy:
  kind: single_agent  # or router, cascade, orchestrator_workers
  escalation_rule: null
limits:
  max_wall_seconds: 1200
  max_total_eur: 1.00
  max_tool_calls: 80
```

Provider, exact model revision, reasoning effort, context window, tool interface, prompts, harness version, runtime, retrieval snapshot and delegation policy are part of the identity. Never merge scores from differing configurations without explicit stratification.

## 2. Scope and task taxonomy

The inventory process SHALL query GitHub installations for `lk251` and `mares-engineering`, page through **all** accessible repositories, and record `repo_full_name`, default branch, HEAD SHA, archived/fork flags, language, CI/test commands, criticality, project phase and task categories. Discovery must be repeatable; inaccessible projects remain marked `uninspected`, not invented. See [project inventory](PROJECT_INVENTORY.md).

Hierarchical taxonomy:
`repository -> product stage -> workstream -> task family -> risk tier -> difficulty`.

Canonical families include: bugfix; unit/integration test creation; dependency upgrade; security remediation; config maintenance; feature iteration; architecture/redesign; documentation; research; incident diagnosis; code review; release/deployment. Each family has its own workload weights, acceptance tests and quality floor.

Projects may move between `exploration`, `build`, `growth`, `maintenance` and `redesign`; this changes sampling weights but **does not retroactively rewrite historical results**.

## 3. Evaluation corpus

Three partitions per task family:

- **Calibration/dev:** inspectable tasks for harness and grader development.
- **Locked holdout:** immutable tasks and expected outcomes; limited runs; protected access; no model feedback loop; rotate after exposure.
- **Rolling production sample:** privacy-reviewed, deduplicated real tasks and incident regressions, added on a scheduled cadence.

Store input issue/commit, base repository SHA, environment image/flake lock, permitted files, allowed tools, acceptance criteria, difficulty/risk labels, task author, dataset version and provenance. Reproduce a task in an isolated checkout at the *pre-solution* commit; prevent reference fixes and future tests from leaking into model context. Maintain time-based splits and near-duplicate checks. Never publish private code, credentials or production traces in this public repository.

### 3.1 Executable graders first

For coding: compilation, unit/integration tests, regression tests, property tests, security scans, deterministic scenario checks and diff/side-effect constraints. Use **held-out tests** where possible. Do not reward text similarity to a reference patch as a substitute for correctness.

For NixOS: `nix flake check`, evaluation/build of target configurations, NixOS VM tests, rollback/boot/service assertions, package and security invariants; **never** run generated system mutations against the host. Pin nixpkgs and flake locks.

For product and design work: explicit rubric + independent blinded review, user-visible acceptance criteria, executable prototypes, consistency checks and a calibrated judge. Do not use an uncalibrated LLM judge as the sole gate for security-sensitive or high-stakes changes.

For multi-agent work: grade the final integrated result and log per-agent intermediate contributions; an orchestrator that delegates cheaply but fails integration is a failure.

## 4. Evaluation execution

Proposed pipeline:

```text
GitHub inventory + PR/issue mining + manually authored tasks
            |
    dataset registry (versioned, stratified)
            |
   scheduler -> candidate registry -> provider adapters
            |                         (API / local)
    ephemeral sandbox / VM / pinned harness
            |
      executable graders + calibrated human/judge review
            |
      append-only run records + artifacts + EUR ledger
            |
     paired analysis / Pareto frontier / policy selection
            |
     canary / manual approval / monitoring / reevaluation
```

All tasks run from the same initial repository snapshot with controlled tools and budgets. Randomize candidate order; record seeds where supported, timeouts, rate-limit errors, retries and environment drift. Do not assume temperature=0 makes runs deterministic. For multi-step agent tasks, preserve tool traces, file diffs and verification outputs. Local inference includes hardware, quantization, serving stack, power and utilization assumptions.

### 4.1 Minimum run schema

```json
{
  "schema_version": "0.1",
  "run_id": "uuid",
  "timestamp_utc": "ISO-8601",
  "project": "lk251/nixos-configs",
  "task_id": "nixos/upgrade/example-001",
  "task_family": "dependency_upgrade",
  "dataset_version": "2026-10-29",
  "base_commit": "git-sha",
  "environment_digest": "sha256:...",
  "candidate_id": "policy-id",
  "model_revision": "exact-version",
  "harness_revision": "git-sha",
  "policy_revision": "git-sha",
  "grader_revision": "git-sha",
  "attempt": 1,
  "success": false,
  "safety_violation": false,
  "failure_class": "test_failure",
  "duration_seconds": 0,
  "input_tokens": 0,
  "output_tokens": 0,
  "tool_calls": 0,
  "provider_cost_eur": 0,
  "local_compute_cost_eur": 0,
  "human_review_cost_eur": 0,
  "recovery_cost_eur": 0,
  "total_cost_eur": 0,
  "artifacts_uri": "private://..."
}
```

In production: validate with a schema, attach hashes, store secrets outside traces, and redact PII. `total_cost_eur` must be derived from versioned rate cards and recorded exchange rates. Include API fees, cached-token billing, tool/CI execution, local electricity/depreciation where relevant, retry/escalation and estimated human time. Record estimated and actual costs separately.

## 5. Statistics and decision policy

Evaluate candidates **paired on identical tasks**, preferably with repeated independent attempts for stochastic agents. Report per-family sample size, task-level success, confidence intervals, paired win/loss/tie and cost distributions. Bootstrap at **task** level (not individual tokens or correlated attempts), preserving project strata; disclose wide uncertainty. Pre-register primary metrics, minimum detectable effect, stopping rules and multiple-comparison policy; avoid peeking until a desired winner appears.

Primary deployment gates (initial policy defaults, not empirically validated thresholds):

1. No critical safety violation in the candidate's audited evaluation set; a single severe failure triggers manual investigation, not automatic promotion.
2. Non-inferior quality to incumbent within a pre-agreed family-specific margin, or a clearly justified improvement; insufficient evidence => keep incumbent.
3. Meet task-specific latency and reliability SLOs and an explicit per-task EUR cap.
4. Among eligible candidates, choose the lowest **expected total EUR / accepted outcome** with uncertainty-aware tie handling. Show a quality/cost/latency Pareto frontier rather than a universal rank.

For simple repeated independent attempts, `P(success by k)=1-(1-p)^k` is only a *model under independence*; real retries are correlated. Estimate the actual end-to-end policy (including escalation) on held-out tasks instead.

Run a candidate-selection policy against a fixed baseline and against alternate policies as first-class experiments. Initial router: deterministic rules by task family, risk and cost ceiling. Later, consider contextual bandits only after enough unbiased logged exploration data exists, with propensity logging, safe exploration, a holdout control and off-policy evaluation. Do not let an LLM's self-reported confidence alone determine escalation.

## 6. Budgeted delegation

Treat the existing `budgeted delegation` skill as **uninspected until located**. Audit its prompts, routing rules, token ceilings, context handoff, sub-agent outputs, tool permissions and recovery logic. Introduce:
- explicit task classification, risk level and success rubric before delegation;
- per-subtask budget and global budget, time and tool limits;
- cheap-first attempts only for suitable low-risk work, with measurable escalation triggers;
- independent integration verification and a mandatory stop condition;
- policy-version tags, cost and success traces, and replayable ablations.

Compare `single frontier`, `single economical`, `economical + verifier`, `economical -> frontier escalation`, and `frontier planner + economical workers` on the same tasks. Delegation must outperform a single-model baseline **net of orchestration overhead**.

## 7. Operations, security and governance

- Evaluation runs use disposable sandboxes/VMs, no production secrets, outbound network allowlists, resource quotas, and least-privilege tokens.
- No automatic merges, deployments, NixOS rebuilds on real hosts, financial transactions or irreversible actions based solely on evaluation outcomes.
- Capture artifacts privately with retention policy, access controls, redaction and deletion support; public GitHub holds only non-sensitive specifications, schemas and synthetic fixtures.
- Trigger evaluation on new model/version/provider, harness/prompt/policy change, price change, major repository change, drift, incident and on a weekly rolling schedule.
- CI smoke suite on PR; broader scheduled suite; full holdout only on promotion candidates.
- Dashboard by project/family: eligible candidates, pass rate + interval, severe failures, median/p95 latency, EUR per accepted outcome, budget burn, trend and sample freshness.
- Human approval required for changes to safety floors, evaluation datasets, graders and production routing policies.

## 8. Implementation requirements and acceptance criteria

**P0:** repository inventory, task schema, one deterministic NixOS evaluation fixture, one coding fixture, model/provider adapter, run ledger, reproducible local CLI, baseline comparison and CI smoke run.

**P1:** replayable multi-step agent tasks, provider price ledger, paired comparison with confidence intervals, held-out datasets, project-specific tests for Motoko/Faber/VICA after inspecting actual code, and policy comparison.

**P2:** observability, rolling production samples, automated drift alerts, controlled canaries, router/cascade experiments and dashboards.

An MVP is accepted only when an independent engineer can rerun the same fixture from pinned inputs, inspect grader outputs and costs, and explain why a candidate is recommended **without relying on an external intelligence index**.

## 9. Non-goals and open questions

Not a universal AGI IQ score, not a public model leaderboard, not training/fine-tuning infrastructure, and not a production auto-deployer. Open: deployment environment, private artifact store, acceptable monthly evaluation budget, human hourly cost, which project repositories are active, exact `budgeted delegation` location, privacy requirements and initial candidate shortlist.

See [literature review](LITERATURE_REVIEW.md), [inventory](PROJECT_INVENTORY.md), and [roadmap](ROADMAP.md).
