# Mares Engineering — Internal Evals

An **evaluation-as-code** system for choosing the best *verified task outcome per euro* across AI models, providers, local inference and agent/orchestrator policies, grounded in work on Mares Engineering repositories rather than external leaderboards.

**Status:** Specification v0.1 (design only). No harness, measurements or automated routing are implemented yet.

## Documents

- [System specification](docs/SPECIFICATION.md): requirements, architecture, protocols, metrics, acceptance criteria and phased rollout.
- [Literature review and open-source landscape](docs/LITERATURE_REVIEW.md): research rationale, credible OSS candidates, adoption decisions, limitations and references.
- [Project inventory and evaluation taxonomy](docs/PROJECT_INVENTORY.md): GitHub scope, initial task families and validation work.
- [Implementation roadmap](docs/ROADMAP.md): prioritized milestones and deliverables.

## Design principles

1. **Evaluate outcomes, not model reputation.** Prefer executable checks, tests and verified PR results to subjective scores.
2. **Compare full policies.** A model + provider + harness + tools + prompt + orchestration + retries is one candidate.
3. **Optimize subject to constraints.** Meet correctness, safety and reliability floors *before* minimizing expected all-in euros per accepted task.
4. **Preserve independent evidence.** Version tasks, execution environments, artifacts, prices, judges and evaluation code; publish uncertainty and failures.
5. **Prevent leakage and unsafe side effects.** Sandboxed runs, immutable holdouts, no production credentials, explicit approval for deployments or system mutations.
6. **Continuously refresh.** Inventory GitHub repos, mine representative tasks, retest on model/provider changes and measure drift.

The repository currently contains a design baseline, not an assertion that all connected projects have been audited. Project-specific details require repository inspection and owner confirmation.

**Branch note:** This new GitHub repository was empty and configured with default branch `main`. The requested specification is being authored on `master`; consider changing the default branch to `master` in GitHub settings if that is the intended long-term convention.
