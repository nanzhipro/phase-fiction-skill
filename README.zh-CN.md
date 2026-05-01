# phase-fiction-skill

**简介**：一套把长篇小说创作状态外部化到磁盘、能跨上下文压缩、跨会话和跨 Agent 稳定续跑的 AI 工作流脚手架。

把小说项目建模为**有序的故事 phase 链**，让 premise、人物、设定、情节推进、章节批次和修订轮次都真正落盘，稳定性来自仓库文件，而不是模型记忆。

> _"把故事连续性从模型记忆迁移到仓库文件系统。"_

[![install](https://img.shields.io/badge/install-npx%20skills%20add-informational?logo=npm)](https://www.npmjs.com/package/skills)
[![Copilot](https://img.shields.io/badge/GitHub%20Copilot-supported-24292e?logo=github)](./references/agent-instructions-template.md)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-d97757)](./references/agent-instructions-template.md)
[![Codex](https://img.shields.io/badge/Codex-supported-10a37f)](./references/agent-instructions-template.md)

[English](./README.md) · **中文**

---

**快速导航**：[亮点](#亮点) · [推荐场景](#推荐场景) · [安装与更新](#安装与更新) · [快速开始](#快速开始) · [工作原理](#工作原理) · [框架与生成结构](#框架与生成结构) · [Profiles 层](#profiles-层) · [示例项目](#示例项目) · [文档索引](#文档索引)

## 为什么

AI 连续参与小说创作数小时后，最常丢的不是字数，而是故事承诺、人物动机、设定一致性、节奏目标和当前修订焦点。把要求继续堆进 prompt 并不能解决问题。这个项目用文件和脚本把这些状态外部化，再用 `planctl` 保证顺序、恢复和收尾。

## 亮点

- 把 premise、人物、设定、结构、正文和修订拆成明确 phase，状态落盘，不再依赖聊天记忆。
- 压缩或换会话后，始终按 `manifest -> handoff -> advance --strict` 恢复，续跑路径稳定可预期。
- 通过 `planctl` 原子写回进度，避免“聊天里好像已经改过”的隐性状态漂移。
- 通过 `project_profile` 和 `artifact_checks` 给完成稿目标加上机器可检查的交付门禁。
- 仓库内附一个完整生成出来的样例项目，可直接查看 [novels/grassland-train-mystery/plan/manifest.yaml](./novels/grassland-train-mystery/plan/manifest.yaml)、[novels/grassland-train-mystery/story/outline/arc-map.md](./novels/grassland-train-mystery/story/outline/arc-map.md) 和 [novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md](./novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md)。

## 推荐场景

如果你不想让 AI 每次都靠聊天上下文“重新理解这部小说”，而是希望它持续推进一部长篇、连载项目或完整修订周期，这个 Skill 就是为此设计的。

你可以在这些场景里使用它：

- **从 0 到 1 规划长篇小说**：premise、人物阵容、情节引擎、章节矩阵、修订轮次分开管理。
- **连载项目**：剧情弧、悬念、更新批次和连续性检查可以跨周续跑。
- **已有半成稿的抢救与重构**：把散乱提纲、旧设定和章节残稿整理成有依赖顺序的修复计划。
- **长修订周期**：结构修订、人物修订、行文修订、终稿打磨拆成不同 phase，不再混成一团。

最简单的用法是在 Agent 里直接说：

```text
用 phase-fiction-skill 规划并连续写完这部小说：<你的 premise>
```

如果项目已经生成了 plan，日常推进只需要遵循：

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "..." --next-focus "..." --continue
```

## 工作原理

三层设计，各层职责分离：

| 层 | 作用 | 写入者 |
| --- | --- | --- |
| **Enforcement**：`.github/copilot-instructions.md` · `CLAUDE.md` · `AGENTS.md` | 把规约从“建议”变成会话级前置条件 | 人（三份字节同步） |
| **Scheduler**：生成项目中的 `scripts/planctl` | 决定下一步做什么、校验依赖、原子写回账本 | 源文件来自本仓库的 `scripts/planctl.rb` |
| **Contracts**：`plan/manifest.yaml` · `plan/common.md` · `plan/phases/*` · `plan/execution/*` | 定义当前故事 phase、阶段目标和本轮写作/修订边界 | 人（AI 辅助） |

本仓库现在只保留 `scripts/planctl.rb` 这一份正式源码。生成新项目时，仍然把它复制成目标仓库里的 `scripts/planctl`，这样运行命令保持稳定，同时源仓库不再维护第二个入口文件。

执行态由脚本独占维护的两份文件承载：

- `plan/state.yaml` — 客观进度账本（`complete` 原子写入）
- `plan/handoff.md` — 压缩恢复锚点（`complete` 自动刷新）

生产使用时还应把两层新护栏写进 manifest：

- `repo_policy`：默认要求小说项目是独立仓库根，避免产物悄悄落进宿主仓库默认分支。
- `project_profile` + `artifact_checks`：把目标字数、章节数和 phase 级交付条件变成 `complete` / `doctor` / `finalize` 可消费的机器门禁。

## 框架与生成结构

这个 Skill 定义的是长篇小说工作流的元结构：Agent 该收集哪些输入、怎样切 phase、怎样把合同写到磁盘、以及压缩后如何恢复推进。

它**不会**内置一份固定小说结构，更不会偷偷塞一套“标准三幕”给所有项目。每个具体项目的 phase 切分、剧情弧、beat map、角色网络、正文批次和修订轮次，都是在实际执行时根据用户 premise、硬约束和本仓库的方法论文档生成出来，再写回该项目自己的文件里。

可以把它理解成：Skill 提供的是“长出并守住结构的机制”，而每部小说的具体结构，仍然是在运行过程中生成并固化为仓库状态的。

## Profiles 层

核心工作流本身不应感知具体题材。真正会随小说类型变化的默认值，应落在独立的 profile 层，而不是继续往调度器里硬编码。

- 核心层：`planctl`、双层合同、handoff、ledger、finalize、交付门禁
- Profile 层：题材 / 叙事引擎相关的默认产物、推荐 phase 图、修订轮次和质量焦点
- 项目层：某一部小说自己的 manifest、结构、正文和修订产物

边界与 schema 见 [profiles/README.md](./profiles/README.md)，共享 overlay 目录见 [profiles/overlays.yaml](./profiles/overlays.yaml)，具体派生示例见 [profiles/examples.md](./profiles/examples.md)。当前仓库先提供五个起步 profile：

- [profiles/mystery-thriller/profile.yaml](./profiles/mystery-thriller/profile.yaml)
- [profiles/romance/profile.yaml](./profiles/romance/profile.yaml)
- [profiles/epic-fantasy/profile.yaml](./profiles/epic-fantasy/profile.yaml)
- [profiles/literary/profile.yaml](./profiles/literary/profile.yaml)
- [profiles/horror/profile.yaml](./profiles/horror/profile.yaml)

## 示例项目

仓库内附带一个真实生成的样例项目 [novels/grassland-train-mystery](./novels/grassland-train-mystery/plan/manifest.yaml)，可以直接用来理解“生成后的小说项目结构”在实践里长什么样。

- 规划合同： [novels/grassland-train-mystery/plan/manifest.yaml](./novels/grassland-train-mystery/plan/manifest.yaml)
- 故事结构： [novels/grassland-train-mystery/story/outline/arc-map.md](./novels/grassland-train-mystery/story/outline/arc-map.md) 和 [novels/grassland-train-mystery/story/outline/tension-waves.md](./novels/grassland-train-mystery/story/outline/tension-waves.md)
- 正文产物： [novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md](./novels/grassland-train-mystery/story/draft/part-1/chapters-01-04.md)、[novels/grassland-train-mystery/story/draft/part-2/chapters-05-08.md](./novels/grassland-train-mystery/story/draft/part-2/chapters-05-08.md) 和 [novels/grassland-train-mystery/story/draft/part-3/chapters-09-12.md](./novels/grassland-train-mystery/story/draft/part-3/chapters-09-12.md)
- 修订产物： [novels/grassland-train-mystery/story/revision/structural-pass.md](./novels/grassland-train-mystery/story/revision/structural-pass.md)

## 安装与更新

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

安装后，在对应 Agent 会话里直接说「用 phase-fiction-skill 规划这部小说」即可触发；Skill 的发现描述见 [SKILL.md](./SKILL.md) 的 frontmatter。

## 标准循环

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

## 适用边界

**适用**：长篇小说、系列小说、连载项目、故事 bible 整理、半成稿抢救、结构修订、人物修订、终稿打磨。

**不适用**：单次灵感 prompt、短诗、广告文案、论文，以及 premise 还无法稳定描述的纯探索期。

## 前置条件

- 目标仓库是 Git 工作区（`git rev-parse --is-inside-work-tree` 为 `true`）。非 Git 目录下无法做 Phase 级白名单比对与回滚，默认禁止，仅允许显式 opt-out：`PHASE_CONTRACT_ALLOW_NON_GIT=1`。
- 默认仓库策略应是独立项目根；如果小说项目故意嵌在更大的 mono-repo 里，必须显式声明 `repo_policy.mode: embedded-explicit`，并避免继续把里程碑直接写到 `main` / `master`。
- 本地有 `ruby`，版本 ≥ 2.6。planctl 是单文件脚本，不依赖任何 gem。

## 快速开始

作为 Agent Skill 使用时，在 Copilot / Claude Code / Codex 里直接说「帮我用 phase-fiction-skill 规划这部小说」即可。Skill 会交互收集故事承诺、phase 切分和硬约束，并按 [SKILL.md](./SKILL.md) 的 Procedure 生成完整制品：

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

如果项目目标是“长篇完成稿”或“可审读全稿”，还应在 `plan/manifest.yaml` 里声明 `project_profile`，并给关键 phase 补 `artifact_checks`；否则 workflow 虽然能走完，但 `finalize` 也会明确指出当前稿件可能仍未达到目标交付层级。对 `full-draft`、`serialized-arc` 这类交付层级，`target_length_chars` 和 `target_chapters` 应写成显式的 `min/max` 正整数区间，`target_chapter_pattern` 也应显式声明，而不是依赖默认值。

如果你是在升级旧版生成项目，推荐顺序是：先替换 `scripts/planctl`，再跑 `ruby scripts/planctl doctor`，然后依次补 `repo_policy`、`project_profile`，最后给 delivery-bearing phase 补 `artifact_checks`。在真实迁移里，doctor 往往还会顺手暴露三份 agent 指令文件已经发生漂移的问题。

## 文档索引

- [SKILL.md](./SKILL.md) — 生成脚手架的完整流程与 Quality Gates
- [references/methodology.md](./references/methodology.md) — 小说方法论与失败模型
- [references/glossary.md](./references/glossary.md) — 故事 phase 术语表
- [references/templates.md](./references/templates.md) — `manifest` / `common` / `state` / `handoff` 模板
- [profiles/README.md](./profiles/README.md) — profile 层边界与 schema
- [profiles/overlays.yaml](./profiles/overlays.yaml) — base profile 之上的共享 overlay 目录
- [profiles/examples.md](./profiles/examples.md) — profile + overlay 如何展开成最终 phase 图的具体示例
- [profiles/profile-template.yaml](./profiles/profile-template.yaml) — 新 profile 的起步模板
- [references/phase-templates.md](./references/phase-templates.md) — phase 与 execution 合同模板
- [references/workflow-template.md](./references/workflow-template.md) — `plan/workflow.md` 模板
- [references/agent-instructions-template.md](./references/agent-instructions-template.md) — 三份 Agent 指令的共同模板
- [assets/README.md](./assets/README.md) — 当前资产语义与兼容性说明

## 许可证

本仓库继承上层 Agent Skill 库的许可证；`scripts/planctl.rb` 无外部依赖，按需单独复制复用即可。

[English](./README.md) · **中文**
