---
name: paseo-orchestration
description: Orchestrate substantial software initiatives with Paseo using a bounded control tower, one Codex writer, isolated worktrees, release-time security checks, and risk-sized independent review. Use when coordinating a major requirement across one or more repositories without conflating implementation, review, merge, and rollout authority.
---

# Paseo initiative orchestration

Run one major requirement as one accountable initiative. Keep the hierarchy shallow, give writing
authority to one Codex agent, and make every external action an explicit gate.

## Decide whether orchestration is warranted

Use this workflow for work that is multi-repository, security-sensitive, architecturally significant,
or too large for one focused session. Handle small edits directly.

Use a fresh initiative orchestrator for each major requirement. A material clean-slate scope pivot ends
the old initiative and starts a fresh one. Do not let one agent indefinitely accumulate implementation,
orchestration, review, or multiple product identities.

At initiative start, record the requirement, acceptance criteria, repositories/base revisions,
constraints, review depth, and an authority envelope marking local edits, local commits, push, PR,
merge, publish, remote rename, and rollout as allowed or approval-required. Operate automatically
inside it; crossing it requires approval. Rollout is never implied. Keep live or tenant systems outside
the envelope unless explicitly authorized, and ask the control tower when scope or authority is unclear.

## Keep the hierarchy bounded

Use at most three levels:

```text
optional control tower
└── initiative orchestrator
    ├── one Codex writer
    └── risk-sized reviewer set
```

Let the control tower route initiatives, set priorities, resolve dependencies, and choose review depth.
Let the initiative orchestrator own implementation, validation, evidence, and handoff. Never add another management tier.

## Prepare one workspace and isolated worktrees

Use one Paseo workspace for the initiative. Use one writing worktree per
repository and attach the writer and any reviewers to that existing workspace.

Before creating or reusing them:

1. Inspect active agents, workspaces, worktrees, `git status`, and `git worktree list`.
2. Preserve dirty user work; never reset, overwrite, or discard it.
3. Refresh the shared `main` from `origin/main` before branching only when authorized and safe.
4. Reuse an owner-supplied workspace or worktree.
5. For cross-repository work, create one worktree in each repository but keep one initiative workspace.
6. Inspect actual branch changes with a two-dot diff such as `git diff origin/main..HEAD`.

Create implementation and review agents as `subagent` children in the existing workspace. If the
available interface cannot create and verify that relationship, stop rather than substituting a detached agent.

Archive only after integration or explicit abandonment. First confirm tracked,
untracked, and ignored state is clean; treat worktree archive as destructive.

## Resolve the single writer from live preferences

Read `~/.paseo/orchestration-preferences.json` immediately before choosing the
writer. Require `providers.impl` to resolve to an available `codex/<model>`.

Never commit the resolved model or copy it into public prompts and examples.

Inspect the provider before creation and resolve the configured full-access mode without inventing
settings. If the provider, model, or mode is ambiguous or unavailable, stop and ask the owner.

Use one Codex writer for all implementation edits. Do not let the control tower, orchestrator, or
reviewers edit the worktree. If the current Codex session is explicitly the writer, do not create a duplicate.

Give the writer this compact charter:

```text
Requirement:
Repositories and worktrees:
Acceptance criteria:
Constraints and non-goals:
Validation required:
Authority envelope:
Handoff required: diff, commands/results, commits, residual risks
```

## Implement, verify, and freeze

Follow each repository's conventions and keep changes inside the charter.

Validate inputs and error paths at boundaries. Run focused checks first, then the repository's relevant
full validation. Inspect the actual diff rather than accepting the writer's summary.

Before review or handoff:

- map every acceptance criterion to evidence;
- record skipped or unavailable checks honestly;
- remove accidental generated artifacts and caches;
- preserve unrelated user changes;
- commit locally and capture the full base and head SHA for every repository;
- confirm the worktree is clean.

A branch name is not a frozen scope. Any content change creates a new head and invalidates review tied to the old head.

## Run release-time public-repository checks

Keep security verification a release process, not a product subsystem. Do not
add a custom validator or test framework to this skill directory.

For a public candidate, run the official `quick_validate.py` shipped with the
installed skill-creator package, then standard Git and secret checks:

```sh
python3 <skill-creator>/scripts/quick_validate.py paseo-orchestration
git diff --check <base>..HEAD
gitleaks git --redact --no-banner .
pattern='api[_-]?key|access[_-]?token|client[_-]?secret|authori''zation:|password[=:]|/ho''me/|/Us''ers/|[A-Z]:\\Us''ers\\|wks_[0-9a-f]{16}|ses_[A-Za-z0-9_-]{16,}|[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}'
rg -l -i --hidden --glob '!.git' --glob '!.git/**' "$pattern" .
```

Expect the compact `rg -l` scan to print no paths. It intentionally prints
filenames only, never matching values. Inspect positives privately, replace
secrets and private identity data, and rerun all checks. If a path is itself
sensitive, report `[REDACTED-PATH]` plus a one-way fingerprint.

Require Gitleaks for a public release. If it is unavailable, report the gap and
stop the release gate; do not claim equivalent coverage from the pattern scan.

Never allowlist a real private credential or identity merely to obtain green
output. History rewriting requires explicit owner authorization.

## Ask the control tower to size review

After freezing and validating the candidate, report its size, risk, changed
surfaces, verification, and unresolved risks to the control tower. Do not start
review automatically.

Choose one of these review gates explicitly:

- no additional agent review for a truly small, low-risk change;
- one independent whole-scope reviewer for a normal substantial initiative;
- a sealed Claude, fresh Codex, and K3 trio only for high-risk work or an
  explicit owner requirement.

Use [templates/reviewer-prompt.md](templates/reviewer-prompt.md) for every reviewer. Limit investigation
to the frozen base..head diff, tracked files at the frozen head, acceptance criteria, and required
evidence. Ban filesystem-wide searches, external plan/cache writes, empty-directory findings, and
unrelated archaeology unless tied to a concrete risk; every finding must state the user-goal harm.
Keep reviewers independent and never share another reviewer's result or a suspected answer.

For a sealed trio, create Claude, a fresh Codex session distinct from the
writer, and `opencode/clawdi/k3` as subagents in the existing workspace.

Prefer true plan/read-only mode. If Codex requires full access, forbid edits and repository-external
artifacts, verify the exact SHA and clean state before and after, and invalidate and rerun review if it writes.

Set `notifyOnFinish: true` for every agent. The main or control-tower session
must dispatch agents and immediately return a receipt with the initiative, current/frozen scope, roles,
phase, next notification triggers, and confirmation that mainline remains available.

Notify and resume only for started, permission, error, completed, or materially deadline-blocked
transitions. Send no repetitive still-running updates and never poll. The main or control tower must
never call `paseo wait`, a process wait, `sleep`, or any equivalent that blocks for completion. An
initiative orchestrator may manage its own event-driven barrier but must not block the control tower.

## Adjudicate evidence once

Adjudicate only after completion events arrive from every reviewer required by
the chosen gate. An error or partial message is incomplete, not an abstention.
While a sealed barrier is closed, do not edit, synthesize, or share results.

Keep raw reviewer reports in Paseo activity. By default, show the user only verdicts, blockers,
accepted/rejected findings, residual risk, and next action; expand on request. Track findings as:

```text
ID | reviewer | severity | evidence | impact | disposition | owner
```

Reproduce material claims and decide by evidence, not votes. Treat a proven
blocker as a veto. Send accepted fixes to the same writer, rerun validation,
commit, and freeze the new head.

Do not automatically start another full review round. Report the new head,
fix evidence, and remaining risk to the control tower; let it choose targeted
verification, one final review, or a new sealed trio.

## Keep lifecycle gates separate

Treat convergence only as permission to approach the next gate:

1. Bind local validation and review to the exact frozen head.
2. Run or observe CI separately and bind it to the same head.
3. Push, open a PR, or merge only with explicit authorization.
4. Roll out only with separate authorization, monitoring, and rollback plans.

A review, green CI, or merge never authorizes deployment or live changes.

## Hand off compactly

Report compactly:

```text
Initiative/status | repository/base/head | changes/validation
Review verdicts/blockers/dispositions | residual risk/next action
Commits | actions not taken
```

Keep local paths, runtime IDs, and private identity data out of public handoffs.

State which external actions were not performed. Finish only when the charter
is satisfied, the exact head is clean and validated, the chosen review gate is
complete, findings are resolved or owned, and actions are accurately reported.
