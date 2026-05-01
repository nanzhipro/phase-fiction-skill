# phase-fiction-skill

**About**: Disk-backed scaffolding for long-form fiction projects that need to survive context compression, fresh sessions, and Agent switches.

Model a novel or serial fiction project as an **ordered chain of story phases**: premise, cast, canon, plot engine, scene batches, and revision passes all live on disk instead of being entrusted to the model's memory.

> _"Move story continuity out of model memory and into the repository filesystem."_

[![install](https://img.shields.io/badge/install-npx%20skills%20add-informational?logo=npm)](https://www.npmjs.com/package/skills)
[![Copilot](https://img.shields.io/badge/GitHub%20Copilot-supported-24292e?logo=github)](./references/agent-instructions-template.md)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-d97757)](./references/agent-instructions-template.md)
[![Codex](https://img.shields.io/badge/Codex-supported-10a37f)](./references/agent-instructions-template.md)

**English** · [中文](./README.zh-CN.md)

---

**Quick links**: [Recommended scenarios](#recommended-scenarios) · [Install](#install--update) · [Quick start](#quick-start) · [How it works](#how-it-works) · [Documentation](#documentation-map)

## Why

Any AI that tries to help with a novel across many sessions will eventually lose one of the things that matters most: the story promise, character intent, canon, pacing, or revision target. A bigger prompt does not fix that. This project fixes it by externalizing story state into files and letting `planctl` enforce sequence and recovery.

## Recommended scenarios

Use this Skill when the project is a novel, novella, serial fiction run, or full-manuscript revision that will take multiple sessions and needs stable continuity.

It fits especially well when you are doing:

- **A new novel from scratch**: premise, cast, plot engine, scene planning, chapter drafting, and revision stay separated instead of blurring together.
- **A serial fiction workflow**: arcs, cliffhangers, continuity checks, and rolling revision passes can be tracked across weekly or daily batches.
- **A rescue pass on an existing manuscript**: turn scattered notes, half-finished chapters, and canon drift into a dependency-aware repair plan.
- **A long revision cycle**: structure pass, character pass, prose pass, and line-level polish become distinct phases with explicit completion checks.

The simplest way to start is to tell the Agent:

```text
Plan and continuously write this novel with phase-fiction-skill: <your premise>
```

Once a plan exists, the daily loop is:

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "..." --next-focus "..." --continue
```

## How it works

Three layers stay separate:

| Layer | Role | Authored by |
| --- | --- | --- |
| **Enforcement**: `.github/copilot-instructions.md` · `CLAUDE.md` · `AGENTS.md` | Turns the rules from "suggestions" into session-level preconditions | Human (all three kept byte-identical) |
| **Scheduler**: `scripts/planctl` | Decides the next step, checks dependencies, writes the ledger atomically | Reuse the script in this repo |
| **Contracts**: `plan/manifest.yaml` · `plan/common.md` · `plan/phases/*` · `plan/execution/*` | Defines the story phase, its objective, and the current writing or revision boundary | Human (AI-assisted) |

Runtime state lives in two files, owned exclusively by the scheduler:

- `plan/state.yaml` — objective progress ledger (atomically written on `complete`)
- `plan/handoff.md` — compression-recovery anchor (auto-refreshed on `complete`)

## Install & update

Use the [`skills`](https://www.npmjs.com/package/skills) CLI to install this repo as an Agent Skill into the skills directory of Copilot / Claude Code / Codex.

```bash
# Install into the current Agent's default skills directory (auto-detected)
npx skills add nanzhipro/phase-fiction-skill

# Target a specific Agent explicitly
npx skills add github:nanzhipro/phase-fiction-skill --agent claude
npx skills add github:nanzhipro/phase-fiction-skill --agent copilot
npx skills add github:nanzhipro/phase-fiction-skill --agent codex

# Update to latest main (add `-g` if it was installed globally)
npx skills update phase-fiction-skill -g

# Force reinstall (overwrites local edits - back up first)
npx skills add nanzhipro/phase-fiction-skill --force

# Remove
npx skills remove phase-fiction-skill -g
```

Once installed, just tell the Agent to plan or continue a novel with `phase-fiction-skill`. The discovery description lives in [SKILL.md](./SKILL.md).

## Golden loop

Every phase runs the same loop. You can compress or swap sessions at any breakpoint and continue later without rebuilding the whole prompt.

```text
advance --strict  →  load 3 docs  →  draft / revise inside the current boundary
                                           ↓
                    ← handoff (by script) ← complete <id> --continue
                                           ↓
                              (all phases done) → finalize
```

A single command kicks off, resumes, or wraps up:

```bash
ruby scripts/planctl advance --strict                  # new session / daily driver
ruby scripts/planctl resume --strict                   # cold start after compression
ruby scripts/planctl complete <id> --summary "..." --next-focus "..." --continue
ruby scripts/planctl finalize                          # first success writes finalization ledger + git close-out, then prints the dashboard
ruby scripts/planctl doctor                            # repo health check (SHA256-diff the three instruction files, etc.)
```

Phase boundaries are internal, not user confirmation points. If the next current phase is still a placeholder pair, `advance` returns `ACTION: promote_placeholder`; promote both contracts first, then keep going. When `advance` returns `ACTION: finalize`, the project still is not done until `finalize` writes the close-out ledger and returns the dashboard.

## When to use

**Use it for**: new novels, serial fiction, multi-book arcs, story-bible stabilization, manuscript rescue plans, structure passes, character passes, prose passes.

**Do not use it for**: one-off short prompts, poetry, copywriting, or moments where the story premise is still too fuzzy to define phases.

## Prerequisites

- The target repo is a Git worktree (`git rev-parse --is-inside-work-tree` returns `true`). Outside a Git workspace there is no objective basis for Phase-level whitelist diffing or rollback; this is blocked by default. Explicit opt-out only: `PHASE_CONTRACT_ALLOW_NON_GIT=1`.
- `ruby` 2.6 or newer is available locally. `planctl` is a single-file Ruby script with zero gem dependencies.

## Quick start

When used as an Agent Skill, just say "plan this novel with phase-fiction-skill" inside Copilot / Claude Code / Codex. The Skill collects the premise, phase split, and hard constraints, then generates the artifact set described in [SKILL.md](./SKILL.md):

```text
<project>/
├── .github/copilot-instructions.md
├── CLAUDE.md
├── AGENTS.md
├── plan/
│   ├── manifest.yaml
│   ├── common.md
│   ├── workflow.md
│   ├── state.yaml
│   ├── handoff.md
│   ├── phases/phase-0-*.md
│   └── execution/phase-0-*.md
└── scripts/planctl
```

Only the current phase needs a formal contract on day one. Future phases can stay as placeholder pairs until they become current.

## Documentation map

- [SKILL.md](./SKILL.md) - full scaffolding procedure and quality gates
- [references/methodology.md](./references/methodology.md) - fiction-specific methodology and failure model
- [references/glossary.md](./references/glossary.md) - glossary of story-phase terms
- [references/templates.md](./references/templates.md) - `manifest` / `common` / `state` / `handoff` templates
- [references/phase-templates.md](./references/phase-templates.md) - phase and execution contract templates
- [references/workflow-template.md](./references/workflow-template.md) - `plan/workflow.md` template
- [references/agent-instructions-template.md](./references/agent-instructions-template.md) - shared template for the three Agent instruction files
- [assets/README.md](./assets/README.md) - current asset semantics and compatibility notes

## License

Shares the license of the parent Agent Skill library. `scripts/planctl.rb` has no external dependencies and can be copied out and reused standalone.

[English](./README.md) · [中文](./README.zh-CN.md)
