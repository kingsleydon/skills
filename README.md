# Kingsley's Hermes skills

Kingsley's personal collection of independent, installable Hermes skills.
Each top-level skill directory contains its own `SKILL.md` and can be installed
without copying the rest of the collection. Additional sibling skills may be
added over time.

## Available skills

- [`paseo-orchestration`](paseo-orchestration/SKILL.md) — coordinate a
  substantial Paseo initiative with a bounded control tower, one Codex writer,
  isolated worktrees, and risk-sized independent review.

## Install one skill

Choose a skill directory and copy only that directory into the active Hermes
profile. For example:

```sh
skill="paseo-orchestration"
mkdir -p "${HERMES_HOME:-$HOME/.hermes}/skills"
cp -R "$skill" "${HERMES_HOME:-$HOME/.hermes}/skills/"
hermes skills list
```

PowerShell:

```powershell
$Skill = "paseo-orchestration"
$HomePath = if ($env:HERMES_HOME) { $env:HERMES_HOME } else { Join-Path $env:LOCALAPPDATA "hermes" }
New-Item -ItemType Directory -Force (Join-Path $HomePath "skills") | Out-Null
Copy-Item -Recurse $Skill (Join-Path $HomePath "skills")
hermes skills list
```

## Use

Ask Hermes to orchestrate a substantial initiative with Paseo, or preload it:

```sh
hermes --skills paseo-orchestration
```

See the selected skill's `SKILL.md` for its complete instructions.

## Public-repository note

Before a public release, run the official skill-creator `quick_validate.py`,
`git diff --check`, Gitleaks, and the compact filename-only pattern scan in the
selected skill. The collection intentionally ships no custom validator, test
suite, or CI subsystem. Record unavailable tools honestly and do not publish
until the required release checks pass.

License: [MIT](LICENSE).
