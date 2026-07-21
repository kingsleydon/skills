# Independent whole-scope review

Review the frozen change below. Remain technically read-only: prefer true
plan/read-only mode; do not edit, commit, stash, push, create agents or
worktrees, open a PR, merge, deploy, publish, rename a remote, rewrite history,
or touch live or tenant systems. Do not inspect or request another reviewer's
conclusions.

## Scope

- Reviewer class: `<reviewer>`
- Requirement: `<requirement>`
- Acceptance criteria: `<criteria>`
- Repository: `<public-url>`
- Base SHA: `<full-base-sha>`
- Head SHA: `<full-head-sha>`
- Verification: `<commands-and-results>`

Confirm the frozen head and clean state before and after. Inspect the complete
two-dot diff, all tracked files at the frozen head, acceptance criteria, and
required evidence. Do not search the wider filesystem, write external plans or
caches, report empty directories, or pursue unrelated archaeology unless a
concrete risk requires it. If full access is unavoidable, forbid artifacts
outside the repository; any write invalidates the review and requires a fresh
run.

Within that scope, review correctness, security/privacy, data integrity,
compatibility, operability, tests, documentation, and public hygiene as relevant.

Return one concise report containing:

1. Scope and cleanliness confirmation.
2. `PASS` or `BLOCKER`.
3. Findings with ID, severity, evidence, user-goal harm, and recommended fix.
4. Commands/checks and redacted results.
5. Residual risks.

Use evidence, not confidence or votes. Never print a sensitive value; report
only its category, a safe or redacted path, and a one-way fingerprint.
