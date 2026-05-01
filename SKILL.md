---
name: phase-fiction-skill
description: "Set up a disk-backed Phase-Fiction workflow for long-form fiction projects. Use when the user wants to plan, continue, or revise a novel, novella, serial fiction project, multi-book arc, story bible, scene matrix, or full-manuscript rewrite across many sessions. Triggers: '写小说', '长篇小说规划', '续写长篇', '分阶段写小说', '故事 bible', '人物弧光规划', '章节矩阵', '修订轮次', 'serial fiction workflow', 'novel planning', 'story phase', 'scene matrix', 'revision pass', 'manuscript recovery', 'phase-fiction'. Scaffolds plan/manifest.yaml, plan/common.md, phase and execution contracts, a planctl scheduler script, synchronized agent instruction files, and a final story/README.md index after creation completes."
argument-hint: "(optional) target project path and one-line story premise"
---

# Phase-Fiction Skill（长篇小说续跑工作流脚手架）

把一部长篇或系列小说建模为**有序创作合同链**，让 AI 在小窗口里稳定推进 premise、人物、设定、情节、章节、修订，并在上下文压缩或换会话后继续写下去而不丢主线。

完整方法论见 [references/methodology.md](./references/methodology.md)。

## When to Use

**适用**：

- 从 0 到 1 规划一部长篇小说、系列小说或连载项目
- 已有零散灵感、角色卡或世界观，想整理成可执行创作计划
- 已有提纲或半成稿，想分阶段继续写完
- 对已有小说进行结构性重写、人物加强、节奏修订或终稿收束
- 多 POV、多时间线或多卷结构，需要长期保持 canon 与人物弧光一致

**不要用**：

- 只写单个短场景、一个段落润色或一次性灵感喷发
- 诗歌、广告文案、论文、短视频脚本等非小说项目
- premise 尚未成形、连主角与核心冲突都无法描述的纯探索阶段

## Prerequisites（前置条件，必须满足）

以下前提不满足，则本 Skill 不生成任何文件：

1. **目标项目根目录必须是 git 工作区**。`git rev-parse --is-inside-work-tree` 必须返回 `true`。
   - Phase-Fiction 依赖 git 记录每一轮创作或修订的客观里程碑，否则 `complete` 写回的 phase 无法对应到可审计 diff。
   - 修复方式：`cd <target> && git init && git add -A && git commit -m 'baseline'`。
   - 默认还要求目标项目根目录就是 git top-level；如果你故意把小说项目嵌在宿主仓库里，必须显式把 `repo_policy.mode` 设为 `embedded-explicit`，并避免直接落在 `main` / `master`。
2. **本地有 `ruby` 可用**，版本 ≥ 2.6（`ruby -v` 验证）。`planctl` 是单文件 Ruby 脚本，不依赖 gem。
3. **显式 opt-out（不推荐）**：只有在确实不能使用 git 的特殊环境下，才允许预先设置 `PHASE_CONTRACT_ALLOW_NON_GIT=1`，并在后续 `plan/common.md` 里写明偏离风险与补偿方式。

## What It Produces

运行本 Skill 会在目标项目里创建一套完整的小说创作外部状态：

```text
<novel-project>/
├── plan/
│   ├── manifest.yaml            # 创作 phase 顺序、依赖与 required_context
│   ├── common.md                # 全局硬约束：故事承诺、POV、时态、canon、边界
│   ├── workflow.md              # Phase-Fiction 执行说明
│   ├── state.yaml               # 执行账本（脚本写入）
│   ├── handoff.md               # 压缩恢复锚点（脚本写入）
│   ├── phases/
│   │   └── phase-0-<name>.md    # 阶段蓝图：做什么、做到哪儿算完成
│   └── execution/
│       └── phase-0-<name>.md    # 执行围栏：这轮允许改哪些文件
├── scripts/
│   └── planctl                  # 调度脚本（Ruby，可直接执行）
├── story/
│   └── README.md                # finalization 时自动生成的故事资料结构说明
├── .github/
│   └── copilot-instructions.md  # Copilot 强制层
├── CLAUDE.md                    # Claude Code 强制层
└── AGENTS.md                    # Codex / 通用 Agent 强制层
```

## Core Principles

| #   | 原则           | 落地动作                                                                   |
| --- | -------------- | -------------------------------------------------------------------------- |
| P1  | 状态外部化     | premise、phase 进度、handoff 全部写回仓库文件，而不是交给 AI 记忆          |
| P2  | 调度与执行分离 | `planctl` 决定下一步写什么，AI 只决定怎么写                                |
| P3  | 三文件上下文律 | 当前工作窗口只装 `common + phase + execution` 三份文档                     |
| P4  | 双层合同       | `phases/*` 定义阶段目标，`execution/*` 限定这轮可触碰路径                  |
| P5  | 依赖强制校验   | `depends_on` + `--strict` 阻止跳写、跳修、跳收尾                           |
| P6  | 完成即写入     | `complete` 原子写回 `state.yaml` 与 `handoff.md`，未写回不算完成           |
| P7  | 固定恢复协议   | 压缩恢复永远按 manifest → handoff → advance                                |
| P8  | 里程碑外部化   | 每个创作 phase 完成后留下 git 级里程碑，方便回看与回退                     |
| P9  | 显式整体收尾   | 全部 phase 完成后必须跑 `finalize`，再把是否连载 / 投稿 / 发布交还人类     |
| P10 | 交付门禁外部化 | 长度、章节数和 phase 级 artifact checks 由 manifest 驱动，而不是靠口头约定 |
| P11 | 仓库隔离显式化 | 独立仓库 / worktree / 嵌入模式必须写进 `repo_policy`，默认拒绝隐式嵌套     |

## Procedure

### Step 0: 环境前置检查

在收集任何输入、生成任何文件之前，先验证 Prerequisites：

1. 在目标项目根目录执行：`git -C <target> rev-parse --is-inside-work-tree`。
2. 若返回 `true`，继续 Step 1。
3. 若返回非零或 `false`，立刻停止，不创建 `plan/` 或任何 agent 强制层文件，并要求用户先建立 git 基线。
4. 若 `PHASE_CONTRACT_ALLOW_NON_GIT=1` 已显式设置，允许跳过门禁，但必须在 `plan/common.md` 中记录风险与补偿方案。

### Step 1: 收集输入

如果用户未指定，使用 ask-questions 工具逐项收集：

1. **项目根目录路径**（绝对路径）
2. **一句话故事承诺**：主角 + 目标 + 冲突 + 危险感
3. **作品类型与目标长度**：长篇 / 中篇 / 系列；大致字数或卷数
4. **基础 profile**：至少在当前内置 profile 中选择 `mystery-thriller`、`romance`、`epic-fantasy`、`literary`、`horror` 或 `custom`
5. **叙事引擎与 overlays**：例如 `clue-driven` / `relationship-driven`，再加 `closed-circle`、`dual-pov`、`countdown`、`slow-burn` 这类 overlay
6. **仓库策略**：独立 repo / worktree / 嵌入式项目（默认独立 repo）
7. **切分主维度**：按卷幕、按剧情弧、按章节波次、按修订轮次、按连载批次（必须选一）
8. **phase 覆写需求**：默认不手填整套 phase；只有当用户明确要求增删 phase 或替换某个默认 phase 时，才记录成覆写项
9. **全局硬约束**：POV、时态、文风、受众、内容边界、必须保留的设定、禁用套路、关键母题等

如果目标是“长篇完成稿”而 phase 只包含几批正文与一次修订，没有任何扩写或终稿层级 phase，应视为切分不足，先在规划期重切，不要把明显偏短的结构直接交给 `finalize`。

写不出客观完成判定的 phase，通常就是切错了，必须当场重切。

### Step 1.5: 从 profile 派生初始 phase 图

在真正生成 `plan/manifest.yaml` 之前，必须先把 `workflow_profile` 展开成一份**初始 phase 图**，而不是让用户从零手填整套 phase。

固定顺序如下：

1. 读取选中的 `profiles/<profile>/profile.yaml`
2. 以其中 `defaults.phase_catalog` 作为 base phase 列表
3. 读取 [profiles/overlays.yaml](./profiles/overlays.yaml) 中的标准 overlay 定义，只应用用户选中的 overlays
4. 按 overlay 顺序应用 `phase_merge.operations`：当前只允许 `require_phase` 和 `ensure_phase_after`，并且必须通过 overlay 内部的 `targets` / `anchor_targets` 显式解析到当前 profile 的 phase id
5. 最后才应用用户的 phase 覆写项；覆写项只应修改局部，不应重建整套 phase 图

生成约束：

- base profile 决定“这类小说通常需要哪些 phase”
- overlays 决定“这次项目有哪些次级结构要求”
- 用户覆写只解决项目特例，不替代 profile 层
- 如果 `custom` profile 被选中，才允许退化为人工定义 phase 图
- 不允许根据 phase 名称相似度去猜“等价 phase”；overlay 没有给出当前 profile 的 target map，就直接报错

交付要求：生成后的 phase 列表必须写回 `plan/manifest.yaml`，不能只停留在解释层。

### Step 2: 生成骨架文件

按 [references/templates.md](./references/templates.md) 的模板同时产出：

- `plan/manifest.yaml`
- `plan/common.md`
- `plan/workflow.md`
- `plan/state.yaml`
- `plan/handoff.md`
- `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md`

三份 agent 指令必须字节一致，统一复制 [references/agent-instructions-template.md](./references/agent-instructions-template.md) 并替换 `<PROJECT>`。

同时在 manifest 里写入：

- `workflow_profile`：基础 profile、叙事引擎与 overlays
- `phases`：先从 profile 展开的 phase 图，再叠加用户覆写后的最终结果
- `project_profile`：目标长度、章节数、交付层级、delivery paths
- `repo_policy`：默认 `standalone`
- 对当前 phase 必须满足的 `artifact_checks`

profile 选择规则：核心 workflow（`planctl`、双层合同、handoff、ledger）保持不变；会随小说类型变化的，只能通过 `workflow_profile` 和 `profiles/*/profile.yaml` 改默认 phase 图、产物、补充问卷与修订轮次，不能为每个题材复制一套调度器。

规则补充：当 `delivery_tier` 是 `full-draft` 或 `serialized-arc` 时，凡是会写入 `delivery_paths` 的 phase，默认都要带 `artifact_checks`。如果缺失，`complete` 会直接拒绝落账本。

结构补充：当 `delivery_tier` 是 `full-draft` 或 `serialized-arc` 时，`project_profile.target_length_chars` 和 `project_profile.target_chapters` 不只是“要有”，还必须写成完整的 `min/max` 正整数区间；`target_chapter_pattern` 也必须显式声明，避免把默认正则误当成项目约束。

### Step 3: 安装 planctl

把 [scripts/planctl.rb](./scripts/planctl.rb) 复制到目标项目的 `scripts/planctl`，并加可执行位：`chmod +x scripts/planctl`。注意：本仓库不再额外保留 `scripts/planctl` 入口文件，真正需要分发和维护的只有 `scripts/planctl.rb` 这份实现；落到生成项目时，文件名仍然应保持为 `scripts/planctl`。随后至少跑一次：

```bash
ruby scripts/planctl status
ruby scripts/planctl doctor
```

### Step 4: 生成第一批 phase 合同

- 先根据 `workflow_profile` 的 profile + overlays 生成最终 phase 列表，再落 `plan/phases/*` 和 `plan/execution/*`
- 为 `phase-0` 和准备立刻启动的 phase 生成**正式合同**
- 为其余 future phase 生成带 `PHASE_CONTRACT_PLACEHOLDER` 的**成对占位合同**

不要一次把所有 future phase 都写成正式合同。小说项目在推进中会不断校正角色、设定和故事引擎，过早写死所有 future phase 会迅速失真。

写作铁律：

- 完成判定禁止使用“更精彩”“更抓人”“更立体”之类主观词
- execution 的“允许改动”必须是路径级白名单
- 对需要机器拒绝的交付条件，在 manifest phase 条目里补 `artifact_checks`
- 世界观整理、正文起草、结构修订、语言润色，尽量不要塞进同一个 phase
- 如果 profile 自带修订轮次或必带制品，phase 合同里必须显式落盘，不能只在 profile.yaml 里提到却不写进项目文件

### Step 5: 启动并验证

在目标项目根目录运行：

```bash
ruby scripts/planctl advance --strict
```

验收三条：

1. 返回 `ACTION: implement`
2. `required_context` 恰好三份：`common.md` + 当前 phase + 当前 execution
3. `plan/handoff.md` 已写出压缩恢复顺序和下一步指引
4. 若配置了 `project_profile` / `repo_policy`，`ruby scripts/planctl doctor` 不应报 top-level mismatch 或明显的 delivery gate 问题

### Step 6: 输出使用指南

告诉用户日常循环命令：

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "<本轮完成内容>" --next-focus "<下一轮焦点>" --continue
ruby scripts/planctl finalize
```

并提醒：

- `ACTION: promote_placeholder` 不是用户确认点，而是先补正式合同再继续
- 压缩或新会话恢复优先用 `ruby scripts/planctl resume --strict`
- `ruby scripts/planctl doctor` 用于检查三份 agent 指令、manifest 引用和 state/handoff 一致性
- `ruby scripts/planctl finalize` 现在还会检查 delivery gate：workflow 完成不等于目标交付层级达标

### Step 6.5: 升级既有项目到新 schema

如果目标项目是用旧版本 Skill 生成的，建议按下面的顺序升级，而不是一次性手改所有文件：

1. 先把最新的 [scripts/planctl.rb](./scripts/planctl.rb) 覆盖到项目里的 `scripts/planctl`。
2. 运行 `ruby scripts/planctl doctor`，先看迁移提示，不要急着补正文。
3. 若 manifest 缺 `repo_policy`，先补 `repo_policy.mode`：通常是 `standalone`；只有明确嵌在宿主仓库里时才用 `embedded-explicit`。
4. 若 manifest 缺 `project_profile`，补 `delivery_tier`、`delivery_paths`、`target_length_chars`、`target_chapters`。
5. 对所有会写入 `delivery_paths` 的 phase，补 `artifact_checks`。
6. 再跑一次 `ruby scripts/planctl doctor`，直到只剩可接受 warning。
7. 最后再继续新的 phase 或重新跑 `finalize`。

真实迁移时还要顺手检查 `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 是否仍然字节一致；旧项目很容易在后续手改中把这三份文件改散。

### Step 7: 里程碑提交与推送（`complete` 自动执行）

`complete` 会在当前 phase 真正完成后，自动写回 `state.yaml` 和 `handoff.md`，然后执行 git 里程碑收尾。小说项目里，AI 需要先判断哪些文件属于临时产物，避免把缓存或无关导出物一并带进提交；真正应该保留的通常是：故事 bible、角色卡、提纲、章节草稿、修订 memo、状态文件。

### Step 8: 整体收尾（`planctl finalize`）

当最后一个 phase 也完成后，必须运行：

```bash
ruby scripts/planctl finalize
```

`finalize` 会在首次成功执行时生成或刷新 `story/README.md`，再写 `finalized_at`、刷新 `handoff.md`、执行最终 git 收尾，并输出最终执行仪表盘。`story/README.md` 是给人类和后续 agent 的故事资料入口，会说明 `story/` 的目录层级、文件职责、推荐阅读顺序和维护原则。此后 AI 必须把以下决策交还人类：

- 是否继续连载、投稿或公开发布
- 是否打某个手稿版本标签
- 是否归档 `plan/` 并开启下一轮创作
- 是否安排编辑、beta 读者、事实核查或敏感性审读

## Decision Points

**phase 数 > 12**：通常切分过碎，应改按卷、按剧情弧或按修订波次聚合。

**依赖成环**：直接判定 phase 切错，必须重构边界。

**故事承诺写不清**：先补 premise，不要急着拆 phase。

**用户说“future phase 你全写好”**：只把当前 phase 和紧邻下一 phase 写成正式合同，其余保持占位合同。

**`advance --strict` 指向的新 phase 仍是占位合同**：先补正式合同，再 rerun 同一条 strict 命令。

**已有散乱提纲或半成稿，但没有 planctl 体系**：直接生成基础设施，把现有资料纳入 manifest 与 common 约束。

**已有旧版 phase-fiction 项目，需要接入新 gate**：先升级 `scripts/planctl`，跑 `doctor` 看迁移提示，再按 `repo_policy` → `project_profile` → `artifact_checks` 的顺序补 schema；不要先补 artifact_checks 再回头定义 delivery tier。

**某个 phase 做坏了需要回退**：运行 `ruby scripts/planctl revert <phase-id>`，并按依赖逆序回退。

**最后一个 phase 已完成**：不要直接宣告项目结束，必须先跑 `finalize`。

## Quality Gates

- [ ] 目标项目根目录是 git 工作区，或已显式设置 `PHASE_CONTRACT_ALLOW_NON_GIT=1` 且 `plan/common.md` 含偏离风险段
- [ ] 默认 `repo_policy.mode=standalone`，且项目根目录与 git top-level 一致；若不是，必须显式改为 `embedded-explicit`
- [ ] `manifest.yaml` 的 `phases[].required_context` 恰好三项
- [ ] `manifest.yaml` 已声明 `project_profile`，其目标长度 / 章节数与项目定位一致
- [ ] `manifest.yaml` 的 `compression_control.rules` 明确禁止一次性加载全部 phase 文档
- [ ] `common.md` 只写长期稳定约束，不混入具体实施步骤
- [ ] `phases/phase-0-*.md` 的完成判定全部可客观勾选
- [ ] `execution/phase-0-*.md` 的允许改动是路径白名单
- [ ] 需要机器兜底的 phase 已配置 `artifact_checks`
- [ ] 当前 phase 使用正式合同，future phase 使用带 `PHASE_CONTRACT_PLACEHOLDER` 的成对占位合同
- [ ] `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 三份内容完全一致
- [ ] `ruby scripts/planctl status`、`doctor`、`advance --strict` 能跑通
- [ ] 当 current phase 仍是占位合同，`advance --strict` 会返回 `ACTION: promote_placeholder`
- [ ] `finalize` 在 phase 未全部完成时拒绝运行；全部完成后能输出最终仪表盘并生成 `story/README.md`

## References

- [完整方法论（Phase-Fiction Workflow）](./references/methodology.md)
- [manifest / common / state / handoff 模板](./references/templates.md)
- [phase 定位合同 + 执行合同模板](./references/phase-templates.md)
- [plan/workflow.md 模板](./references/workflow-template.md)
- [Agent 强制层模板](./references/agent-instructions-template.md)
- [planctl 调度脚本](./scripts/planctl.rb)
