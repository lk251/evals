# Literature Review — Continuous, Internal LLM and Agent Evaluation

**Version:** 0.1 · 2026-10-29  
**Purpose:** trace the evidence behind [SPECIFICATION.md](SPECIFICATION.md) and assess credible, reusable open-source infrastructure. This is a **design-oriented, non-systematic review**, not a claim that every upstream project has been independently audited at a pinned release. Licenses, current maintenance and enterprise-feature boundaries MUST be verified before adoption.

## 1. Research questions

1. How can internal evaluations reflect changing engineering work rather than opaque general-purpose leaderboards?
2. How should multi-step agents and model-routing policies be evaluated for verified task completion, safety, latency and *all-in euros*?
3. How can we minimize evaluation overfitting, judge bias, stochastic variance and provider drift?
4. Which existing OSS frameworks should be adopted rather than reimplemented?

## 2. Findings and methodological implications

### 2.1 Real tasks and executable outcomes

- **SWE-bench** formalizes real GitHub issue resolution against repository snapshots and tests; it motivates pre-solution checkout, held-out tests, reproducible environments and end-to-end patch verification. Limitations: test coverage and issue representativeness; public benchmark contamination. [SWE-bench paper](https://arxiv.org/abs/2310.06770), [repository](https://github.com/SWE-bench/SWE-bench).
- **SWE-agent** demonstrates the importance of agent-computer interfaces and tool affordances; model and harness must be compared together. [Paper](https://arxiv.org/abs/2405.15793), [repository](https://github.com/SWE-agent/SWE-agent).
- **Terminal-Bench / Harbor** provide executable terminal-task evaluation and agent harness infrastructure; valuable for protocol inspiration and compatibility, not a substitute for project-specific tests. [Terminal-Bench](https://github.com/harbor-framework/terminal-bench), [Harbor](https://github.com/harbor-framework/harbor).
- **Inspect AI** from the UK AI Security Institute provides a research-oriented framework for datasets, solvers, scorers, sandboxes and reproducible agent evaluations. Strong first candidate for an evaluation execution engine. [Repository](https://github.com/UKGovernmentBEIS/inspect_ai), [documentation](https://inspect.aisi.org.uk/).

**Implication:** objective graders, pinned environments, complete agent traces and repository-specific acceptance tests should be the backbone. Public benchmark scores are useful to shortlist candidates, not to authorize deployment.

### 2.2 Continuous evaluation and changing distributions

- *Tracking the Moving Target: A Framework for Continuous Evaluation of LLM Test Generation in Industry* explicitly discusses continuous evaluation of rapidly evolving commercial LLMs in DevSecOps, rather than static point-in-time scores. [Preprint](https://arxiv.org/abs/2504.18985).
- Weights & Biases describes monitoring changing input, model and retrieval behavior and feeding production examples back into evaluations. This is **industry guidance**, not independent proof that any particular tool improves performance. [Article](https://wandb.ai/site/articles/evaluating-llms-in-production/).
- OpenAI's Evals framework and EleutherAI's lm-evaluation-harness show repeatable eval-as-code patterns, though generic QA/perplexity suites do not measure our complete engineering-agent workflows. [OpenAI Evals](https://github.com/openai/evals), [LM Evaluation Harness](https://github.com/EleutherAI/lm-evaluation-harness).

**Implication:** maintain a stable holdout and a separate rolling, representative task sample. Preserve per-project and per-stage strata; do not conflate performance on NixOS upgrades with VICA product work.

### 2.3 Judges and human calibration

- Human-reviewed gold sets and comparison of automated judges to human judgments are standard operational safeguards; disagreements often reveal ambiguous rubrics. [Label Studio guidance](https://labelstud.io/learning-center/getting-started-with-llm-evaluation/).
- *Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena* identifies biases in LLM judges, including position and verbosity. [Paper](https://arxiv.org/abs/2306.05685).
- *G-Eval* proposes rubric-based LLM evaluation; a judge's apparent agreement with humans does not guarantee unbiased results on a new domain. [Paper](https://arxiv.org/abs/2303.16634).

**Implication:** use deterministic graders first. Blind and randomize subjective pairwise review, calibrate judges against human labels per task family, and escalate uncertain or high-risk decisions.

### 2.4 Cost-sensitive selection, routing and orchestration

- **FrugalGPT** studies reducing LLM inference costs with cascades and selective invocation. [Paper](https://arxiv.org/abs/2305.05176).
- **RouteLLM** studies learned routing between strong and weak models with a quality/cost tradeoff. [Paper](https://arxiv.org/abs/2406.18665), [code](https://github.com/lm-sys/RouteLLM).
- **DSPy** provides programmable optimization of language-model pipelines, but tuning against an evaluation set risks overfitting; maintain independent holdouts. [Paper](https://arxiv.org/abs/2310.03714), [code](https://github.com/stanfordnlp/dspy).
- **HELM** emphasizes multidimensional evaluation and transparency beyond a single accuracy number. [Paper](https://arxiv.org/abs/2211.09110), [code](https://github.com/stanford-crfm/helm).

**Implication:** route on estimated *expected accepted outcome per euro* with quality and risk floors. Measure complete cascades and delegation policies as independent candidates; report uncertainty, human time and recovery overhead.

### 2.5 Reproducibility and statistics

Paired evaluation on the same tasks reduces variance from task difficulty. Multiple independent agent runs quantify stochasticity; repeated attempts on the same task are not independent *tasks*. Predefine success metrics, non-inferiority margins and stopping rules. Bootstrap across task identities, stratify by repository and task family, and report uncertainty alongside point estimates. Be alert to adaptive selection, holdout reuse and task contamination.

**Evidence boundary:** These are defensible experimental-design recommendations, not claims that one universal sample size or router always wins. Statistical power and costs must be estimated from pilot data.

## 3. Open-source landscape: adoption shortlist

Categories below are **recommendations**, not claims of verified 2026 release/license state. All upstream versions must be pinned and license-reviewed.

| Repository | Steward / credibility | Role | Fit / trade-off | Decision |
|---|---|---|---|---|
| [UKGovernmentBEIS/inspect_ai](https://github.com/UKGovernmentBEIS/inspect_ai) | UK AI Security Institute, government research | Eval runner, agent tasks, scorers, sandboxes | Research-focused; requires adapters and our own task sets | **Pilot first** |
| [harbor-framework/harbor](https://github.com/harbor-framework/harbor) | Terminal-Bench ecosystem | Agent execution/benchmark harness | Useful for terminal agents; inspect compatibility | Pilot alternative |
| [harbor-framework/terminal-bench](https://github.com/harbor-framework/terminal-bench) | Agent benchmark community | Terminal tasks / reproducible tests | Not a substitute for our repos | Reference tasks |
| [SWE-bench/SWE-bench](https://github.com/SWE-bench/SWE-bench) | Academic benchmark | GitHub issue replay | Heavy environments, public task contamination | Reuse protocol |
| [SWE-agent/SWE-agent](https://github.com/SWE-agent/SWE-agent) | Academic agent research | Agent harness | Interface affects results | Candidate harness |
| [All-Hands-AI/OpenHands](https://github.com/All-Hands-AI/OpenHands) | OpenHands community/industry | Software agent | More operational complexity | Candidate harness |
| [openai/evals](https://github.com/openai/evals) | OpenAI | Eval registry/framework | Less directly focused on full agent side effects | Reference/adapter |
| [EleutherAI/lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) | EleutherAI research | Broad model benchmarks | Mostly model-level tasks, not full repo agents | Optional calibration |
| [stanford-crfm/helm](https://github.com/stanford-crfm/helm) | Stanford CRFM | Multimetric evaluation | Broad benchmark infrastructure | Methodology reference |
| [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) | Stanford research | Pipeline/prompt optimization | Requires strict held-out evaluation | Later experiments |
| [lm-sys/RouteLLM](https://github.com/lm-sys/RouteLLM) | LMSYS academic/industry research | Strong/weak model routing | Needs domain-specific calibration | Router baseline |
| [BerriAI/litellm](https://github.com/BerriAI/litellm) | LiteLLM maintainers | Provider abstraction / gateway | Centralizes provider and price integration; verify operational needs | **Pilot adapter** |
| [langfuse/langfuse](https://github.com/langfuse/langfuse) | Industry OSS/open-core | Tracing, prompt and eval UX | Self-hosting cost and feature boundary | Optional observability |
| [comet-ml/opik](https://github.com/comet-ml/opik) | Comet industry | Eval and observability | Alternative to Langfuse; avoid running both initially | Optional alternative |
| [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) | Arize industry | OpenTelemetry-oriented LLM traces/evals | Integration vs hosting complexity | Optional alternative |
| [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) | Industry OSS | Prompt regression / red-team tests | Good smoke tests; less comprehensive for repo agents | **Pilot smoke tests** |
| [confident-ai/deepeval](https://github.com/confident-ai/deepeval) | Industry OSS/open-core | LLM-as-judge testing | Subjective metrics require calibration | Optional |
| [explodinggradients/ragas](https://github.com/explodinggradients/ragas) | Research/industry OSS | RAG metrics | Only if project has retrieval workflows | Conditional |
| [mlflow/mlflow](https://github.com/mlflow/mlflow) | MLflow community / industry | Experiments, evaluation, artifacts | Broad MLOps platform may be heavy | Alternative |
| [open-telemetry/opentelemetry-specification](https://github.com/open-telemetry/opentelemetry-specification) | CNCF ecosystem | Trace/metric interoperability | Not an evaluation runner | **Adopt conventions** |
| [HumanSignal/label-studio](https://github.com/HumanSignal/label-studio) | Industry OSS/open-core | Human labeling | Extra service; spreadsheet/JSONL may suffice at MVP | Later |
| [ServiceNow/BrowserGym](https://github.com/ServiceNow/BrowserGym) | ServiceNow research | Browser agent benchmark environment | Relevant only for browser tasks | Conditional |
| [xlang-ai/OSWorld](https://github.com/xlang-ai/OSWorld) | Academic agent research | Desktop/computer-use evaluation | Heavy environments | Conditional |

**Licensing note:** The table deliberately does **not** assert specific SPDX licenses without checking pinned tags and transitive dependencies. Open-source, source-available and open-core are not interchangeable. Before installation, record upstream commit, license, security policy, release recency, community adoption and any enterprise-only restrictions in a tool decision record.

### 3.1 Avoid weakly substantiated recommendations

The earlier research summary mentioned `vignesh2027/LLM-Evaluation-Framework` based primarily on a GitHub discussion and project self-description. That is **not sufficient evidence of academic validation or operational maturity**. It should not be a core dependency without independent code, security and maintenance review. Similarly, generic commercial comparison articles are discovery sources, not proof of benchmark validity.

### 3.2 Recommended build-vs-adopt

**Adopt:** Inspect AI or Harbor for eval execution; LiteLLM or thin direct adapters for model access; existing repo build/test commands as graders; OpenTelemetry trace conventions; optionally Langfuse **or** Phoenix **or** Opik once traces justify the operational overhead.

**Build only the differentiating glue:** GitHub project inventory, repository-specific task manifests, secure issue/PR replay fixtures, all-in EUR ledger, per-task risk/quality gates, paired decision reports and the budgeted-delegation policy interface. Keep outputs in JSONL/Parquet/SQLite first; defer a multi-service dashboard.

**Do not adopt yet:** a complex contextual-bandit router, an expensive MLOps stack, or multiple overlapping observability systems before pilot evidence warrants them.

## 4. Proposed evaluation of tools (to be executed)

Score shortlisted tools on: (a) full agent trace capture, (b) sandbox isolation, (c) custom executable graders, (d) local/API model compatibility, (e) eval-as-code and NixOS packaging, (f) CI integration, (g) license/security/maintenance, (h) actual deployment effort. Use one NixOS fixture and one repository bugfix fixture to benchmark the tools themselves. This document records a research-backed shortlist, **not** completed integration tests.

## 5. Research provenance and limitations

The initial deep-research synthesis supplied these themes: continuous evaluation, human-judged calibration, observability, model/policy comparison and open-source reuse. This document extends that synthesis with foundational academic papers and a more discriminating tool shortlist. Some earlier synthesis made **unverified project assumptions** (e.g. conflating VICA with a different public project, or assuming Faber's function). Those assumptions are intentionally excluded here.

Research was reviewed for specification drafting on 2026-10-29. Upstream code and documentation can change. Cite paper versions and pinned tool commits in future implementation decisions; do not treat vendor claims or a single leaderboard as ground truth.
