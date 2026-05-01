# phase-fiction-skill

**About**: Disk-backed scaffolding for long-form fiction projects that need to survive context compression, new sessions, and Agent handoffs.

Treat a novel or serial fiction project as an **ordered chain of story phases**: premise, cast, canon, plot engine, scene batches, and revision passes all live on disk instead of being left to model memory.

> _"Move story continuity out of model memory and into the repository filesystem."_

[![install](https://img.shields.io/badge/install-npx%20skills%20add-informational?logo=npm)](https://www.npmjs.com/package/skills)
[![Copilot](https://img.shields.io/badge/GitHub%20Copilot-supported-24292e?logo=github)](./references/agent-instructions-template.md)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-d97757)](./references/agent-instructions-template.md)
[![Codex](https://img.shields.io/badge/Codex-supported-10a37f)](./references/agent-instructions-template.md)

**Chinese** · [中文](./README.md)

---

**Quick links**: [Highlights](#highlights) · [Recommended scenarios](#recommended-scenarios) · [Install](#install--update) · [Quick start](#quick-start) · [How it works](#how-it-works) · [Framework vs generated structure](#framework-vs-generated-structure) · [Profiles](#profiles) · [Included example](#included-example) · [Documentation](#documentation-map)

## Why

Any AI that helps with a novel across many sessions will eventually lose track of something that matters: the story promise, character intent, canon, pacing, or the current revision target. A bigger prompt does not solve that. This project does it by moving story state into files and letting `planctl` enforce sequence and recovery.

## Highlights

- Keep premise, cast, canon, outline, draft, and revision work in explicit disk-backed phases instead of chat memory.
- Recover deterministically after context compression with the same `manifest -> handoff -> advance --strict` sequence every time.
- Enforce completion through `planctl`, so progress lives in repository state rather than in a previous conversation.
- Add machine-readable delivery gates with `project_profile` and `artifact_checks` when a manuscript needs real draft targets.
- Inspect a generated sample project end to end in [novels/grassland-train-mystery/plan/manifest.yaml](./novels/grassland-train-mystery/plan/manifest.yaml), [novels/grassland-train-mystery/story/outline/arc-map.md](./novels/grassland-train-mystery/story/outline/arc-map.md), and [novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md](./novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md).

## Recommended scenarios

Use this Skill when the project is a novel, novella, serial fiction project, or full-manuscript revision that will span multiple sessions and needs stable continuity.

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
| **Scheduler**: `scripts/planctl` in generated projects | Decides the next step, checks dependencies, writes the ledger atomically | Source from `scripts/planctl.rb` in this repo |
| **Contracts**: `plan/manifest.yaml` · `plan/common.md` · `plan/phases/*` · `plan/execution/*` | Defines the story phase, its objective, and the current writing or revision boundary | Human (AI-assisted) |

This repository now keeps only `scripts/planctl.rb` as the canonical source. Generated projects still copy that file into their own `scripts/planctl`, so runtime commands stay stable even though the source repo no longer carries a second entrypoint file.

Runtime state lives in two files, owned exclusively by the scheduler:

- `plan/state.yaml` — objective progress ledger (atomically written on `complete`)
- `plan/handoff.md` — compression-recovery anchor (auto-refreshed on `complete`)

Two additional guardrails now matter for production use:

- `repo_policy` — prevents a generated fiction project from silently living inside a parent repo's default branch unless you opt into `embedded-explicit` on purpose.
- `project_profile` + `artifact_checks` — turn target length, chapter count, and phase-level deliverables into machine-readable gates that `complete`, `doctor`, and `finalize` can inspect.

## Framework vs generated structure

This Skill defines the meta-framework for long-form fiction work. It tells the Agent how to collect inputs, split the work into phases, keep contracts on disk, and recover after compression.

It does **not** ship a hidden one-size-fits-all novel outline. The concrete structure of a given project — phase split, arc shape, beat map, character network, draft batches, revision passes — is generated during the run from the user's premise, constraints, and the methodology in this repo, then written into that project's own files.

In other words: the Skill provides the machine for building and preserving structure; each fiction project still generates its own structure at runtime and keeps it as repository state.

## Profiles

The core workflow stays genre-agnostic. Genre-specific defaults belong in the profile layer, not in a forked scheduler.

- Core layer: `planctl`, contracts, handoff, ledger, finalize, delivery gates
- Profile layer: genre / engine defaults such as required artifacts, recommended phase catalogs, revision passes, and quality focus
- Project layer: the concrete manifest, outline, draft, and revision output for a single novel

See [profiles/README.md](./profiles/README.md) for the boundary and schema, [profiles/overlays.yaml](./profiles/overlays.yaml) for the shared overlay catalog, [profiles/examples.md](./profiles/examples.md) for derived phase graph examples, and five starter profiles:

- [profiles/mystery-thriller/profile.yaml](./profiles/mystery-thriller/profile.yaml)
- [profiles/romance/profile.yaml](./profiles/romance/profile.yaml)
- [profiles/epic-fantasy/profile.yaml](./profiles/epic-fantasy/profile.yaml)
- [profiles/literary/profile.yaml](./profiles/literary/profile.yaml)
- [profiles/horror/profile.yaml](./profiles/horror/profile.yaml)

## Included example

This repo includes a generated sample project under [novels/grassland-train-mystery](./novels/grassland-train-mystery/plan/manifest.yaml). Use it as a concrete reference for what a generated fiction project looks like in practice.

- Planning contract: [novels/grassland-train-mystery/plan/manifest.yaml](./novels/grassland-train-mystery/plan/manifest.yaml)
- Story architecture: [novels/grassland-train-mystery/story/outline/arc-map.md](./novels/grassland-train-mystery/story/outline/arc-map.md) and [novels/grassland-train-mystery/story/outline/tension-waves.md](./novels/grassland-train-mystery/story/outline/tension-waves.md)
- Draft output: [novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md](./novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md), [novels/grassland-train-mystery/story/draft/part-2/chapters-05-08.md](./novels/grassland-train-mystery/story/draft/part-2/chapters-05-08.md), and [novels/grassland-train-mystery/story/draft/part-3/chapters-09-12.md](./novels/grassland-train-mystery/story/draft/part-3/chapters-09-12.md)
- Revision output: [novels/grassland-train-mystery/story/revision/structural-pass.md](./novels/grassland-train-mystery/story/revision/structural-pass.md)

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

Once installed, tell the Agent to plan or continue a novel with `phase-fiction-skill`. The discovery description lives in [SKILL.md](./SKILL.md).

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
- The default repo strategy is a standalone project root. If the fiction project is intentionally embedded inside a larger mono-repo, declare `repo_policy.mode: embedded-explicit` and do not keep writing milestones directly onto `main` / `master`.
- `ruby` 2.6 or newer is available locally. `planctl` is a single-file Ruby script with zero gem dependencies.

## Quick start

When you use it as an Agent Skill, just say "plan this novel with phase-fiction-skill" inside Copilot / Claude Code / Codex. The Skill gathers the premise, phase split, and hard constraints, then generates the artifact set described in [SKILL.md](./SKILL.md):

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

For serious long-form projects, also declare a `project_profile` in `plan/manifest.yaml` with draft targets, and add `artifact_checks` to any phase whose deliverables should be machine-gated before `complete` is allowed to write the ledger. Under delivery tiers such as `full-draft` and `serialized-arc`, `target_length_chars` and `target_chapters` should be explicit min/max ranges with positive integers, and `target_chapter_pattern` should be declared explicitly instead of relying on defaults.

If you are upgrading an older generated project, do it in this order: replace `scripts/planctl`, run `ruby scripts/planctl doctor`, add `repo_policy`, add `project_profile`, then add `artifact_checks` to delivery-bearing phases. In real migrations, doctor will often also reveal drift across the three agent instruction files.

## Documentation map

- [SKILL.md](./SKILL.md) - full scaffolding procedure and quality gates
- [references/methodology.md](./references/methodology.md) - fiction-specific methodology and failure model
- [references/glossary.md](./references/glossary.md) - glossary of story-phase terms
- [references/templates.md](./references/templates.md) - `manifest` / `common` / `state` / `handoff` templates
- [profiles/README.md](./profiles/README.md) - profile layer boundary and schema
- [profiles/overlays.yaml](./profiles/overlays.yaml) - shared overlay catalog used to modify base phase graphs
- [profiles/examples.md](./profiles/examples.md) - concrete examples of how profile + overlay selections expand into final phase graphs
- [profiles/profile-template.yaml](./profiles/profile-template.yaml) - starter template for a new fiction profile
- [references/phase-templates.md](./references/phase-templates.md) - phase and execution contract templates
- [references/workflow-template.md](./references/workflow-template.md) - `plan/workflow.md` template
- [references/agent-instructions-template.md](./references/agent-instructions-template.md) - shared template for the three Agent instruction files
- [assets/README.md](./assets/README.md) - current asset semantics and compatibility notes

## License

This repo inherits the license of the parent Agent Skill library. `scripts/planctl.rb` has no external dependencies and can be copied out and reused on its own.

[English](./README.md) · [中文](./README.zh-CN.md)
