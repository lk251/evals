# Connected GitHub Project Inventory — Baseline

**Captured:** 2026-10-28 via the connected GitHub account's repository listing, `lk251` and `mares-engineering`.  
**Status:** Names confirmed accessible; contents, activity, tests, CI and business purpose **not yet audited**. The inventory is a snapshot, not a promise that the list is exhaustive forever. A later check confirmed `lk251/evals` itself exists (newly created and initially empty).

## 1. Accessible repositories

| Repository | Initial evaluation hypothesis (not a verified description) | Priority |
|---|---|---|
| [lk251/nixos-configs](https://github.com/lk251/nixos-configs) | NixOS modules, rebuilds, upgrades, security and regression maintenance | **P0** |
| [lk251/motoko](https://github.com/lk251/motoko) | Coding/maintenance tasks; determine actual language and scope from source | **P0** |
| [lk251/faber](https://github.com/lk251/faber) | Coding/maintenance; inspect to determine domain, tests and workflows | **P0** |
| [lk251/vica](https://github.com/lk251/vica) | Product software engineering; inspect current architecture and stage | **P0** |
| [mares-engineering/vica-business](https://github.com/mares-engineering/vica-business) | Business/product documents; inspect and define non-code rubrics if applicable | P1 |
| [lk251/texere](https://github.com/lk251/texere) | Unknown until inspected | P1 |
| [lk251/minimai](https://github.com/lk251/minimai) | Unknown until inspected | P1 |
| [lk251/aisuite](https://github.com/lk251/aisuite) | Unknown until inspected | P1 |
| [lk251/proofdesk](https://github.com/lk251/proofdesk) | Unknown until inspected | P1 |
| [lk251/agent-bounty-market](https://github.com/lk251/agent-bounty-market) | Unknown until inspected | P1 |
| [lk251/patient-knowledge-agent](https://github.com/lk251/patient-knowledge-agent) | Unknown until inspected; assess privacy and sensitive-data risks before sampling | P1 |
| [lk251/globe](https://github.com/lk251/globe) | Unknown until inspected | P1 |
| [lk251/leo](https://github.com/lk251/leo) | Unknown until inspected | P1 |
| [lk251/agentic-research](https://github.com/lk251/agentic-research) | Unknown until inspected | P1 |
| [mares-engineering/nanoengineer](https://github.com/mares-engineering/nanoengineer) | Unknown until inspected | P1 |
| [mares-engineering/mares-engineering.github.io](https://github.com/mares-engineering/mares-engineering.github.io) | Website maintenance hypothesis | P2 |
| [mares-engineering/pytudes](https://github.com/mares-engineering/pytudes) | Unknown until inspected | P2 |
| [mares-engineering/MastodonKit](https://github.com/mares-engineering/MastodonKit) | Unknown until inspected | P2 |
| [mares-engineering/Mastodon.swift](https://github.com/mares-engineering/Mastodon.swift) | Unknown until inspected | P2 |
| [mares-engineering/Auto-GPT](https://github.com/mares-engineering/Auto-GPT) | Unknown until inspected; check fork/upstream status | P2 |
| [lk251/evals](https://github.com/lk251/evals) | This system's own evaluation infrastructure | **P0** |

The first 20 entries were observed in the 2026-10-28 enumeration. The last entry was separately verified on 2026-10-29. **Priorities are proposed triage order**, not proof of activity or strategic importance.

## 2. Required automated discovery

At least weekly, and on demand:
1. List installed GitHub accounts and page all accessible repositories, including private and organization repos where permissions allow.
2. Capture `owner/name`, repository ID, default branch, HEAD, visibility, fork/archived status, primary languages, last commit, open issues/PRs, workflows and test/build manifests.
3. Diff against prior inventory; flag new, removed, renamed, archived and newly active repositories.
4. Inspect project files/CI, extract **candidate** task categories, and require review before promoting those categories to the evaluation registry.
5. For forks or archived projects, lower sampling priority unless there is actual local work; do not equate access with ownership.
6. Keep private code and traces in an access-controlled store; this public document records only safe metadata.

## 3. Task families by confirmed scope

### NixOS

Initial fixtures: flake lock upgrade, module option fix, service unit regression, package deprecation, secure configuration change, VM boot/service smoke test, rollback scenario. Acceptance: evaluation/build, NixOS VM test, service and security invariants, no unapproved host mutation. Record nixpkgs revision and architecture.

### Motoko, Faber and VICA

**Do not assume a domain from repository names.** First inspect README, source tree, package/build files, workflows, issue history and actual tests. Create one reproducible bugfix task, one test-writing task, one maintenance task and one feature task **only if** those categories reflect actual project work. For VICA, distinguish product coding from potential product-behavior evaluations and business-document tasks in `vica-business`.

### All other projects

Bootstrap a repo-specific manifest only after inspection:
```yaml
repository: lk251/example
inspection_status: pending
head_sha: null
phase: unknown
risk_tier: unknown
task_families: []
build_commands: []
test_commands: []
sensitive_data_policy: no_raw_production_data
owner_reviewed: false
```

No benchmark results should be published for `inspection_status: pending`.

## 4. Changes since inventory

The connected GitHub repo `lk251/evals` was found empty with default branch `main` on 2026-10-29. This specification was then initialized and authored on requested branch `master`. The repository inventory must discover itself in subsequent runs.
