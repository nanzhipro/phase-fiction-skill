---
name: phase-fiction-skill
description: "Set up a disk-backed Phase-Fiction workflow for long-form fiction projects. Use when the user wants to plan, continue, or revise a novel, novella, serial fiction project, multi-book arc, story bible, scene matrix, or full-manuscript rewrite across many sessions. Triggers: '写小说', '长篇小说规划', '续写长篇', '分阶段写小说', '故事 bible', '人物弧光规划', '章节矩阵', '修订轮次', 'serial fiction workflow', 'novel planning', 'story phase', 'scene matrix', 'revision pass', 'manuscript recovery', 'phase-fiction'. Scaffolds plan/manifest.yaml, plan/common.md, phase and execution contracts, a planctl scheduler script, plus synchronized agent instruction files so story state survives context compression across Copilot, Claude Code, and Codex."
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
├── .github/
│   └── copilot-instructions.md  # Copilot 强制层
├── CLAUDE.md                    # Claude Code 强制层
└── AGENTS.md                    # Codex / 通用 Agent 强制层
```

## Core Principles

| #   | 原则           | 落地动作                                                               |
| --- | -------------- | ---------------------------------------------------------------------- |
| P1  | 状态外部化     | premise、phase 进度、handoff 全部写回仓库文件，而不是交给 AI 记忆      |
| P2  | 调度与执行分离 | `planctl` 决定下一步写什么，AI 只决定怎么写                            |
| P3  | 三文件上下文律 | 当前工作窗口只装 `common + phase + execution` 三份文档                 |
| P4  | 双层合同       | `phases/*` 定义阶段目标，`execution/*` 限定这轮可触碰路径              |
| P5  | 依赖强制校验   | `depends_on` + `--strict` 阻止跳写、跳修、跳收尾                       |
| P6  | 完成即写入     | `complete` 原子写回 `state.yaml` 与 `handoff.md`，未写回不算完成       |
| P7  | 固定恢复协议   | 压缩恢复永远按 manifest → handoff → advance                            |
| P8  | 里程碑外部化   | 每个创作 phase 完成后留下 git 级里程碑，方便回看与回退                 |
| P9  | 显式整体收尾   | 全部 phase 完成后必须跑 `finalize`，再把是否连载 / 投稿 / 发布交还人类 |

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
4. **切分主维度**：按卷幕、按剧情弧、按章节波次、按修订轮次、按连载批次（必须选一）
5. **初始 phase 列表**：phase id + 一句话标题 + depends_on（建议 5–12 个）
6. **全局硬约束**：POV、时态、文风、受众、内容边界、必须保留的设定、禁用套路、关键母题等

写不出客观完成判定的 phase，通常就是切错了，必须当场重切。

### Step 2: 生成骨架文件

按 [references/templates.md](./references/templates.md) 的模板同时产出：

- `plan/manifest.yaml`
- `plan/common.md`
- `plan/workflow.md`
- `plan/state.yaml`
- `plan/handoff.md`
- `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md`

三份 agent 指令必须字节一致，统一复制 [references/agent-instructions-template.md](./references/agent-instructions-template.md) 并替换 `<PROJECT>`。

### Step 3: 安装 planctl

把 [scripts/planctl.rb](./scripts/planctl.rb) 复制到目标项目的 `scripts/planctl`，并加可执行位：`chmod +x scripts/planctl`。随后至少跑一次：

```bash
ruby scripts/planctl status
ruby scripts/planctl doctor
```

### Step 4: 生成第一批 phase 合同

- 为 `phase-0` 和准备立刻启动的 phase 生成**正式合同**
- 为其余 future phase 生成带 `PHASE_CONTRACT_PLACEHOLDER` 的**成对占位合同**

不要一次把所有 future phase 都写成正式合同。小说项目在推进中会不断校正角色、设定和故事引擎，过早写死所有 future phase 会迅速失真。

写作铁律：

- 完成判定禁止使用“更精彩”“更抓人”“更立体”之类主观词
- execution 的“允许改动”必须是路径级白名单
- 世界观整理、正文起草、结构修订、语言润色，尽量不要塞进同一个 phase

### Step 5: 启动并验证

在目标项目根目录运行：

```bash
ruby scripts/planctl advance --strict
```

验收三条：

1. 返回 `ACTION: implement`
2. `required_context` 恰好三份：`common.md` + 当前 phase + 当前 execution
3. `plan/handoff.md` 已写出压缩恢复顺序和下一步指引

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

### Step 7: 里程碑提交与推送（`complete` 自动执行）

`complete` 会在当前 phase 真正完成后，自动写回 `state.yaml` 和 `handoff.md`，然后执行 git 里程碑收尾。小说项目里，AI 需要先判断哪些文件属于临时产物，避免把缓存或无关导出物一并带进提交；真正应该保留的通常是：故事 bible、角色卡、提纲、章节草稿、修订 memo、状态文件。

### Step 8: 整体收尾（`planctl finalize`）

当最后一个 phase 也完成后，必须运行：

```bash
ruby scripts/planctl finalize
```

`finalize` 会写 `finalized_at`、刷新 `handoff.md`、执行最终 git 收尾，并输出最终执行仪表盘。此后 AI 必须把以下决策交还人类：

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

**某个 phase 做坏了需要回退**：运行 `ruby scripts/planctl revert <phase-id>`，并按依赖逆序回退。

**最后一个 phase 已完成**：不要直接宣告项目结束，必须先跑 `finalize`。

## Quality Gates

- [ ] 目标项目根目录是 git 工作区，或已显式设置 `PHASE_CONTRACT_ALLOW_NON_GIT=1` 且 `plan/common.md` 含偏离风险段
- [ ] `manifest.yaml` 的 `phases[].required_context` 恰好三项
- [ ] `manifest.yaml` 的 `compression_control.rules` 明确禁止一次性加载全部 phase 文档
- [ ] `common.md` 只写长期稳定约束，不混入具体实施步骤
- [ ] `phases/phase-0-*.md` 的完成判定全部可客观勾选
- [ ] `execution/phase-0-*.md` 的允许改动是路径白名单
- [ ] 当前 phase 使用正式合同，future phase 使用带 `PHASE_CONTRACT_PLACEHOLDER` 的成对占位合同
- [ ] `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 三份内容完全一致
- [ ] `ruby scripts/planctl status`、`doctor`、`advance --strict` 能跑通
- [ ] 当 current phase 仍是占位合同，`advance --strict` 会返回 `ACTION: promote_placeholder`
- [ ] `finalize` 在 phase 未全部完成时拒绝运行；全部完成后能输出最终仪表盘

## References

- [完整方法论（Phase-Fiction Workflow）](./references/methodology.md)
- [manifest / common / state / handoff 模板](./references/templates.md)
- [phase 定位合同 + 执行合同模板](./references/phase-templates.md)
- [plan/workflow.md 模板](./references/workflow-template.md)
- [Agent 强制层模板](./references/agent-instructions-template.md)
- [planctl 调度脚本](./scripts/planctl.rb)
