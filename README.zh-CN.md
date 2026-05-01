# phase-fiction-skill

**About**：一套把长篇小说创作状态外部化到磁盘、可跨压缩、跨会话与跨 Agent 续跑的 AI 工作流脚手架。

把小说项目建模为**有序故事 phase 链**，让 premise、人物、设定、情节推进、章节批次和修订轮次都落盘，稳定性来自仓库文件而不是模型记忆。

> _"把故事连续性从模型记忆迁移到仓库文件系统。"_

[![install](https://img.shields.io/badge/install-npx%20skills%20add-informational?logo=npm)](https://www.npmjs.com/package/skills)
[![Copilot](https://img.shields.io/badge/GitHub%20Copilot-supported-24292e?logo=github)](./references/agent-instructions-template.md)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-d97757)](./references/agent-instructions-template.md)
[![Codex](https://img.shields.io/badge/Codex-supported-10a37f)](./references/agent-instructions-template.md)

[English](./README.md) · **中文**

---

**快速导航**：[推荐场景](#recommended-scenarios) · [安装](#install--update) · [快速开始](#quick-start) · [工作原理](#how-it-works) · [文档索引](#documentation-map)

## Why

AI 连续参与小说创作数小时后，最常丢的不是字数，而是故事承诺、人物动机、设定一致性、节奏目标和当前修订焦点。把要求继续堆进 prompt 并不能解决问题。这个项目用文件和脚本把这些状态外部化，再用 `planctl` 保证顺序、恢复和收尾。

## Recommended scenarios

如果你不想让 AI 每次都靠聊天记忆“重新理解这部小说”，而是想让它持续推进一部长篇、连载或完整修订周期，这个 Skill 就是为你准备的。

你可以在这些场景里使用它：

- **从 0 到 1 规划长篇小说**：premise、人物阵容、情节引擎、章节矩阵、修订轮次分开管理。
- **连载项目**：剧情弧、悬念、更新批次和 continuity check 可以跨周续跑。
- **已有半成稿的抢救与重构**：把散乱提纲、旧设定和章节残稿整理成可执行的修复计划。
- **长修订周期**：结构修订、人物修订、 prose 修订、终稿打磨拆成不同 phase，不再混成一团。

最简单的用法是在 Agent 里直接说：

```text
用 phase-fiction-skill 规划并连续写完这部小说：<你的 premise>
```

如果项目已经生成了 plan，日常推进只需要遵循：

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "..." --next-focus "..." --continue
```

## How it works

三层设计，各层职责分离：

| 层 | 作用 | 写入者 |
| --- | --- | --- |
| **Enforcement**：`.github/copilot-instructions.md` · `CLAUDE.md` · `AGENTS.md` | 把规约从"建议"变成会话级前置条件 | 人（三份字节同步） |
| **Scheduler**：`scripts/planctl` | 决定下一步做什么、校验依赖、原子写回账本 | 复用本仓库脚本 |
| **Contracts**：`plan/manifest.yaml` · `plan/common.md` · `plan/phases/*` · `plan/execution/*` | 定义当前故事 phase、阶段目标和本轮写作/修订边界 | 人（AI 辅助） |

执行态由脚本独占维护的两份文件承载：

- `plan/state.yaml` — 客观进度账本（`complete` 原子写入）
- `plan/handoff.md` — 压缩恢复锚点（`complete` 自动刷新）

## Install & update

推荐用 [`skills`](https://www.npmjs.com/package/skills) CLI 把本仓库作为 Agent Skill 安装到 Copilot / Claude Code / Codex 的 skills 目录。

```bash
# 安装（自动识别当前 Agent 的默认 skills 目录）
npx skills add nanzhipro/phase-fiction-skill

# 显式指定目标 Agent
npx skills add github:nanzhipro/phase-fiction-skill --agent claude
npx skills add github:nanzhipro/phase-fiction-skill --agent copilot
npx skills add github:nanzhipro/phase-fiction-skill --agent codex

# 升级到最新 main（全局安装要加 `-g`）
npx skills update phase-fiction-skill -g

# 重装（覆盖本地修改，请先备份）
npx skills add nanzhipro/phase-fiction-skill --force

# 卸载
npx skills remove phase-fiction-skill -g
```

安装后在对应 Agent 会话里直接说「用 phase-fiction-skill 规划这部小说」即可触发；Skill 的发现描述见 [SKILL.md](./SKILL.md) 的 frontmatter。

## Golden loop

每个 phase 都走同一条环路；中断点随时可以压缩或换会话，下轮从起点重入即可无损续跑：

```text
advance --strict  →  读 3 份上下文  →  起草 / 修订（守 execution 边界）
                                             ↓
                       ← handoff (脚本自动)  ←  complete <id> --continue
                                             ↓
                                  （全部完成）→ finalize
```

一条命令即可启动、恢复或收尾：

```bash
ruby scripts/planctl advance --strict                  # 新会话 / 日常推进
ruby scripts/planctl resume --strict                   # 压缩后冷启动
ruby scripts/planctl complete <id> --summary "..." --next-focus "..." --continue
ruby scripts/planctl finalize                          # 首次成功执行会写最终 ledger + git 收尾，然后输出全计划仪表盘
ruby scripts/planctl doctor                            # 仓库体检（三份指令 SHA256 比对等）
```

phase 边界是内部动作，不是用户确认点。`complete --continue` 会自动接上 `advance --strict`；如果新 current phase 仍是占位合同，`advance` 返回 `ACTION: promote_placeholder`，先把两份合同升级成正式文档，再继续实现。当 `advance` 返回 `ACTION: finalize`，项目还不算结束，必须先完成 `finalize` 的 close-out ledger 和最终仪表盘。

## When to use

**适用**：长篇小说、系列小说、连载项目、故事 bible 整理、半成稿抢救、结构修订、人物修订、终稿打磨。

**不适用**：单次灵感 prompt、短诗、广告文案、论文，以及 premise 还无法稳定描述的纯探索期。

## Prerequisites

- 目标仓库是 Git 工作区（`git rev-parse --is-inside-work-tree` 为 `true`）。非 Git 目录下无法做 Phase 级白名单比对与回滚，默认禁止，仅允许显式 opt-out：`PHASE_CONTRACT_ALLOW_NON_GIT=1`。
- 本地有 `ruby`，版本 ≥ 2.6。planctl 是单文件脚本，不依赖任何 gem。

## Quick start

当作 Agent Skill 使用时，在 Copilot / Claude Code / Codex 里直接说「帮我用 phase-fiction-skill 规划这部小说」即可。Skill 会交互收集故事承诺、phase 切分、硬约束，并按 [SKILL.md](./SKILL.md) 的 Procedure 生成完整制品：

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

手工安装脚手架到已有项目时，直接把 `scripts/planctl.rb` 复制过去并按模板生成其他文件即可；细节见 [SKILL.md](./SKILL.md)。

初次搭建时只需要把当前 phase 写成正式合同；future phase 可以先保留成对占位合同，等 `advance --strict` 返回 `ACTION: promote_placeholder` 时再升级。

## Documentation map

- [SKILL.md](./SKILL.md) — 生成脚手架的完整流程与 Quality Gates
- [references/methodology.md](./references/methodology.md) — 小说方法论与失败模型
- [references/glossary.md](./references/glossary.md) — 故事 phase 术语表
- [references/templates.md](./references/templates.md) — `manifest` / `common` / `state` / `handoff` 模板
- [references/phase-templates.md](./references/phase-templates.md) — phase 与 execution 合同模板
- [references/workflow-template.md](./references/workflow-template.md) — `plan/workflow.md` 模板
- [references/agent-instructions-template.md](./references/agent-instructions-template.md) — 三份 Agent 指令的共同模板
- [assets/README.md](./assets/README.md) — 当前资产语义与兼容性说明

## License

与本 Agent Skill 库同源；单独使用 `scripts/planctl.rb` 无外部依赖，按需复制即可。

[English](./README.md) · **中文**
