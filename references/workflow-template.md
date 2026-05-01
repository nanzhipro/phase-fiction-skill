# Phase-Fiction 执行流程说明

本文件说明 Phase-Fiction Workflow 如何确保小说项目按既定顺序、完整执行，以及在 AI 上下文压缩后如何继续执行。

## 环境前提

本仓库默认应是 **独立 git 工作区**：`git rev-parse --is-inside-work-tree` 返回 `true`，且 `git rev-parse --show-toplevel` 应与项目根目录相同。`scripts/planctl` 的 `advance` / `next` / `resolve` / `complete` / `handoff` 在检测到非 git 工作区，或 `repo_policy.mode=standalone` 但项目根嵌在上层仓库内时，会以 **exit code 3** 拒绝运行（`status` 只打警告不拦截，保证诊断可用）。仅当确需不使用 git 时，可通过 `PHASE_CONTRACT_ALLOW_NON_GIT=1` 环境变量显式绕过，且必须在 `plan/common.md` 中记录偏离风险与补偿方案。

## 目标

这套流程要解决四个问题：

- 如何开始执行全部创作计划
- 如何保证 phase 顺序不能被跳过
- 如何保证每个 phase 真正完成后才进入下一个 phase
- 如何在 AI 压缩或新会话后继续执行，而不是从头手工判断

## 核心角色

### `plan/manifest.yaml`

这是唯一的流程定义来源，负责声明：

- phase 顺序
- depends_on 依赖关系
- 每个 phase 的 `plan_file`
- 每个 phase 的 `execution_file`
- 每次执行必须读取的 `required_context`
- `workflow_profile`（base profile、engine、overlays，以及它们派生出的 phase 图）
- `repo_policy`（独立仓库 / 嵌入式项目策略）
- `project_profile`（目标体量、章节数、交付层级）
- `artifact_checks`（机器可执行的 phase 级交付门禁）
- 连续执行和压缩恢复规则

其中 `workflow_profile` 不是装饰字段。脚手架应先读取 `profiles/<profile>/profile.yaml` 的 base phase catalog，再应用 [profiles/overlays.yaml](../profiles/overlays.yaml) 里被选中的 overlays；overlay 只允许通过 `phase_merge.operations` 里显式的 `targets` / `anchor_targets` 解析当前 profile 的 phase id，最后才落成 `manifest.phases`。

### `plan/common.md`

这是全局创作硬约束。任何 phase 执行都必须带上它，用来保证故事承诺、叙事纪律、设定底线与内容边界不会在局部执行时丢失。

### `plan/phases/*.md`

这是阶段蓝图，定义每个 phase 的阶段定位、阶段目标、实施范围、本阶段产出、明确不做、完成判定。

### `plan/execution/*.md`

这是执行合同，定义一次实际创作或修订时的必带上下文、本次允许改动、本次不要做、交付检查、执行裁决规则。

### `scripts/planctl`

这是唯一的流程入口，负责把 manifest 中的定义转成可执行流程。主要命令有：

- `resolve`
- `next`
- `advance`
- `status`
- `complete`
- `handoff`
- `finalize`

### `plan/state.yaml`

这是执行账本，记录哪些 phase 已完成、完成顺序、每次完成的摘要、下一步焦点和时间戳。

### `plan/handoff.md`

这是压缩恢复锚点，记录当前状态、最近完成摘要、下一 phase、下一步要读的上下文、压缩后的恢复顺序。

### Agent 强制层（`.github/copilot-instructions.md` / `CLAUDE.md` / `AGENTS.md`）

以上三份文件内容保持一致，分别被 GitHub Copilot、Claude Code、Codex 自动注入，保证 AI 在创作 phase 相关任务里：

- 必须先读 manifest
- 必须走 `planctl`
- 不能跳过依赖检查
- 不能一次性加载全部 phase 文档
- 压缩或新会话后要从 handoff 恢复

## 如何确保顺序执行

顺序不是靠人工记忆，而是靠以下机制共同保证：

1. `plan/manifest.yaml` 明确写出 phase 的顺序和 `depends_on`
2. `planctl advance` 只围绕按 manifest 顺序找到的第一个未完成 phase 输出下一动作
3. `planctl resolve --strict`、`planctl next --strict` 和 `planctl advance --strict` 会校验依赖是否满足
4. 未完成前置 phase 时，后续 phase 会被标记为 blocked，不允许继续执行

## 如何确保完整执行

完整执行不是“写了一些就继续”，而是必须满足以下条件：

1. 当前 phase 执行前，必须读取完整 `required_context`
2. 必须同时携带：`plan/common.md`、当前 phase 文档、当前 execution 文档
3. 实施时必须服从 execution 文档中的允许改动、禁止项和交付检查
4. phase 只有在真正完成后，才能运行 `planctl complete`
5. 未写入 `plan/state.yaml` 的 phase，不视为完成

## 如何开始执行全部计划

如果目标是连续执行全部创作计划，标准起点如下：

1. 读取 `plan/manifest.yaml`
2. 读取 `plan/handoff.md`
3. 运行 `ruby scripts/planctl advance --strict`
4. 当输出 `ACTION: implement` 时，按输出结果读取当前 phase 的 `required_context`
5. 开始实施当前 phase

开始时不要一次性加载全部 `plan/phases/` 和 `plan/execution/`，只加载当前 phase 所需上下文。

## 连续执行的标准循环

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
```

每个 phase 都遵循同一条环路：读上下文 → 在边界内实施 → 过完成判定 → `complete --continue` → 服从下一 `ACTION`。

## 如何结束单个 Phase

单个 phase 的结束条件是：

- 当前 phase 的 execution 文档中交付检查已经满足
- 当前 phase 的阶段目标已经达到
- 当前 phase 没有违反禁止项和裁决规则

然后运行：

```bash
ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
```

此时会发生三件事：

- `plan/state.yaml` 记录该 phase 已完成
- completion log 记录摘要和下一步焦点
- 若配置了 `artifact_checks`，completion log 同时记录该 phase 完成时的 evidence 快照（检查结果 + 文件摘要）
- `--continue` 会立即运行 `advance`，输出后续内部动作

## 如何进入下一 Phase

`complete --continue` 成功后，下一步不是停下来问用户“是否继续”，而是立刻做这组内部动作：

1. 自动运行 `ruby scripts/planctl advance --strict`
2. 如果输出 `ACTION: implement`，读取 `required_context` 并直接开始实施
3. 如果输出 `ACTION: promote_placeholder`，先把两份文件升级成正式合同，再重跑 `advance --strict`
4. 如果输出 `ACTION: stop`，报告真实 blocker；如果输出 `ACTION: finalize`，进入整体收尾

## 里程碑提交与推送（`complete` 自动）

每次 `complete` 成功写回 `state.yaml` / `handoff.md` 之后，`scripts/planctl` 会自动执行 git 收尾，为每个创作阶段留下可审计、可回退的里程碑记录。

在小说项目中，AI 需要先根据当前 phase 产生的未跟踪文件，判断哪些属于临时输出，并在需要时把规则写入根目录 `.gitignore`；随后再执行 `git add -A` → `git commit -F -` → `git push`，把设定、提纲、正文、修订 memo 以及 `state.yaml` / `handoff.md` 一并固化。

## 如何回退某个已完成的 Phase

当某个 phase 做坏需要回滚时，运行：

```bash
ruby scripts/planctl revert <phase-id> [--mode revert|reset] [--summary "<reason>"]
```

回退后再跑一次 `planctl advance --strict`，该 phase 会重新进入队列。

## 如何结束全部计划

当全部 phase 都完成后，先运行：

```bash
ruby scripts/planctl advance --strict
```

如果没有剩余 phase，脚本会返回 `ACTION: finalize`，并提示进入整体收尾：

```bash
ruby scripts/planctl finalize
```

首次成功执行时，`finalize` 会先写入最终 ledger，再输出仪表盘：

- 写入 `plan/state.yaml.finalized_at`
- 刷新 `plan/handoff.md`
- 执行最终 git 收尾：`git add -A` → `git commit -F -` → `git push`
- 打印最终执行仪表盘，其中包括 repo policy、delivery gate、phase evidence 与 doctor 级问题

AI 拿到 finalize 输出后，必须做一次深入审视，并把以下决策点交还人类：是否连载 / 投稿 / 对外发布，是否打手稿标签，是否归档 `plan/`，是否安排编辑、beta 读者、事实核查或敏感性审读。注意：全部 phase 完成只代表 workflow 结束；如果 delivery gate 仍未过线，例如字数或章节数明显低于目标，仍不能把当前产物当作目标层级的完成稿。

## 如何在压缩或新会话后继续执行

标准恢复顺序如下：

1. 读取 `plan/manifest.yaml`
2. 读取 `plan/handoff.md`
3. 运行 `ruby scripts/planctl advance --strict`
4. 若返回 `ACTION: promote_placeholder`，先把该 phase 的两份合同升级成正式合同，再重跑同一条 strict 命令
5. 若返回 `ACTION: implement`，按输出结果读取当前 phase 的 `required_context`
6. 继续执行

## 压缩控制原则

- 永远不要一次性加载全部 phase 文档
- 长流程时只保留：`plan/common.md` + 当前 phase plan + 当前 phase execution + `plan/handoff.md`
- 每完成一个 phase 后，立即再次解析下一 phase；若 current phase 仍是占位合同，先补正式合同
- 压缩后优先从 handoff 恢复，而不是重放整段历史聊天

## 推荐命令清单

```bash
ruby scripts/planctl status --format json
ruby scripts/planctl resolve <phase-id> --format prompt --strict
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
ruby scripts/planctl handoff --write
```

整体收尾仪表盘（所有 phase 完成后唯一的最后一步）：

```bash
ruby scripts/planctl finalize
```

## 一句话总结

这套流程通过 manifest 定义顺序，通过 planctl 推进状态，通过 state 记录完成事实，通过 handoff 处理压缩恢复，并在 phase 边界把“先补正式合同再实现”写成脚本可阻断的内部动作；最后由 `finalize` 把全部状态、里程碑和健康检查汇聚成最终执行仪表盘，再把发版、归档、维护等决策权显式交还人类，从而保证全部任务能按既定顺序、完整执行、稳定续跑，并以一次审计可见的收尾真正结束。
