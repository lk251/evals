# Implementation Roadmap — v0.1

The specification is a **proposal**, not an implemented or tested system. Adopt existing OSS components before writing bespoke infrastructure.

## Milestone 0 — Audit and decision record (week 1)

- [ ] Confirm `master` is the intended default branch; update GitHub default branch in settings if desired.
- [ ] Inspect all accessible repositories (languages, README, CI, tests, forks, active work, security requirements). Maintain [inventory](PROJECT_INVENTORY.md).
- [ ] Locate and inspect the actual NixOS `budgeted delegation` skill. Record existing rules and reproducible baseline behavior before changing it.
- [ ] Pilot **Inspect AI vs Harbor** on a minimal terminal-agent fixture; document license, commit pin, sandbox support and runtime complexity.
- [ ] Pilot a provider abstraction (LiteLLM or direct adapters), with local inference compatibility.
- [ ] Establish evaluation budget, human-time cost, EUR rate-card policy, secrets storage and approved data retention.

**Exit:** tool selection ADR, reviewed project taxonomy, runnable single-task fixture.

## Milestone 1 — First evidence (days 1–30)

- [ ] Implement task/candidate/run schemas and CLI: `evals inventory`, `evals run`, `evals compare`.
- [ ] Build reproducible NixOS VM/config fixture and one real code-repair fixture from an inspected repo.
- [ ] Pin base SHA, toolchain, flake lock/container image and grader version.
- [ ] Implement single-model API/local candidates; collect tool traces, tokens, latency, retry and EUR costs.
- [ ] Add CI smoke tests with **synthetic fixtures only** and manual approval for any paid evaluation.
- [ ] Compare at least two models plus a baseline; publish per-task results and confidence intervals.

**Exit:** independently replayable report showing task outcomes, uncertainty and full costs.

## Milestone 2 — Multi-project and delegation (days 31–60)

- [ ] Create representative tasks for Motoko, Faber, VICA and other *verified active* repositories.
- [ ] Version calibration, locked holdout and rolling task partitions.
- [ ] Add objective verifiers, human review rubric and calibrated judge where needed.
- [ ] Instrument budgeted delegation with policy version, budget, escalation, integration checks and stop conditions.
- [ ] Compare single frontier, economical, economical+verifier, cascade and orchestrator-worker policies **on paired tasks**.
- [ ] Add cost/quality Pareto reports, safety gates, candidate selection record and failure taxonomy.

**Exit:** policy comparison with quality floors, EUR/accepted-outcome and documented failure modes.

## Milestone 3 — Continuous operation (days 61–90)

- [ ] Scheduled GitHub discovery and task-family drift review.
- [ ] Production-trace sampling with privacy filters and explicit data provenance.
- [ ] Retest triggers for model/version/provider, prompts, harness, price and policy changes.
- [ ] Optional OpenTelemetry + one observability backend (Langfuse, Phoenix or Opik) only if needed.
- [ ] Weekly decision report; monthly holdout rotation review; manual promotion/canary/rollback policy.
- [ ] Review the quality of the evaluation system itself: leakage checks, grader mutation tests, judge-human disagreement and false-positive acceptance rate.

**Exit:** repeatable ongoing selection decisions for at least three active project families, with audited costs and safeguards.

## Minimum operating cadence

| Trigger | Action |
|---|---|
| Every PR in `evals` | Free synthetic schema/fixture smoke tests |
| Weekly | Inventory sync; lightweight candidate comparison; price refresh |
| New model/provider/harness/policy | Controlled candidate evaluation on calibration set; holdout only for promotion |
| Monthly | Stratification and workload mix review; grader audit; uncertainty review |
| Incident / escaped defect | Add privacy-safe regression task, evaluate incumbent and challenger |
| Quarterly | Retire stale tasks, assess new OSS releases and whether router complexity is justified |

## Decision record template

```yaml
decision_id: evals-YYYYMMDD-project-family
project: lk251/nixos-configs
task_family: dependency_upgrade
dataset_version: version
incumbent_policy: policy-id
challenger_policy: policy-id
n_distinct_tasks: 0
n_attempts_per_task: 0
quality_floor: null
success_rates_with_ci: {}
expected_total_eur_per_accepted_task: {}
latency_slo: null
safety_findings: []
uncertainty_and_limitations: ""
decision: retain_incumbent
approved_by: null
```

## Risks

1. **Too few tasks:** avoid ranking models with spurious precision; aggregate cautiously or defer.
2. **Overfitting:** keep locked holdouts and audit benchmark exposure.
3. **False graders:** test the graders themselves with intentionally faulty patches and mutation testing.
4. **Agent side effects:** isolated environments, restricted credentials, approval gates.
5. **Cost undercounting:** include retries, compute, human review and failure recovery.
6. **Tool sprawl:** choose one runner, one adapter, one observability solution at most.
7. **Project drift:** repository inventory and task weighting are versioned and reviewed.
8. **Public repository leakage:** no sensitive production tasks, traces or customer data in git.

## Deferred architectural decisions

- NixOS flake vs Python package for the core CLI (prefer Nix flake devShell with pinned Python dependencies).
- SQLite + object storage vs Postgres for results (start SQLite if concurrency permits).
- Inspect AI vs Harbor (pilot, then ADR).
- Local GPU amortization and electricity tariff accounting.
- How to estimate the value of a partially correct but salvageable patch.
- Whether and when a learned router outperforms deterministic routing.

**Next engineering action:** inspect `lk251/nixos-configs` and find the actual `budgeted delegation` skill, then produce the first reproducible NixOS fixture and compare two pinned candidates.
