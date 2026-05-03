# Phase-Fiction 执行流程说明

本文件说明《回门之前》如何按既定顺序完成 premise、人物、真相、压力系统、正文分批和修订，并在 AI 上下文压缩后稳定续跑。

## 环境前提

本项目嵌在宿主仓库中，采用 embedded-explicit 模式。执行 `scripts/planctl` 时必须把当前项目目录视为工作根目录，而不是直接在宿主仓库根级手工判断 phase 状态。

## 目标

这套流程要解决四个问题：

- 如何在婚嫁悬疑题材里保持 phase 顺序不能被跳过
- 如何让故事承诺、人物引擎、线索公平性与正文钩子稳定衔接
- 如何保证每个 phase 真正完成后才进入下一个 phase
- 如何在 AI 压缩或新会话后继续执行，而不是重建整套故事记忆

## 核心角色

### `plan/manifest.yaml`

这是唯一的流程定义来源，负责声明：

- phase 顺序
- depends_on 依赖关系
- 每个 phase 的 `plan_file`
- 每个 phase 的 `execution_file`
- 每次执行必须读取的 `required_context`
- `workflow_profile`
- `repo_policy`
- `project_profile`
- `artifact_checks`
- 连续执行和压缩恢复规则

### `plan/common.md`

这是全局创作硬约束。所有 phase 都必须服从单 POV、现实向解释、强场景钩子和婚事即陷阱的类型承诺。

### `plan/phases/*.md`

阶段蓝图，定义每个 phase 的阶段定位、阶段目标、实施范围、本阶段产出、明确不做和完成判定。

### `plan/execution/*.md`

执行合同，定义当前实施能碰什么、不能碰什么、交付检查是什么、哪些越界会直接判定无效。

### `scripts/planctl`

流程入口，负责 `resolve`、`next`、`advance`、`status`、`complete`、`handoff`、`finalize`。

### `plan/state.yaml`

执行账本，记录 phase 完成事实。

### `plan/handoff.md`

压缩恢复锚点，记录下一 phase 与恢复顺序。

### Agent 强制层

`.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 内容保持一致，保证后续 AI 必须先读 manifest，再走 planctl，而不是跳过 phase 边界。

## 顺序执行规则

1. `advance --strict` 只输出当前合法 phase。
2. 任何 future phase 若仍是占位合同，必须先升级成正式合同，再继续执行。
3. 未完成前置 phase 时，后续 phase 一律视为 blocked。

## 连续执行的标准循环

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
```

## 压缩恢复顺序

1. `plan/manifest.yaml`
2. `plan/handoff.md`
3. `ruby scripts/planctl advance --strict`
4. 按输出读取当前 phase 的 `required_context`

## 当前项目的题材提醒

- 强钩子不是加大量死人，而是让每场戏结束时都改变裴见月对婚事、丈夫或旧案的判断。
- 误导必须公平，不能用作者强行遮挡信息。
- 正文批次必须兼顾单批满足感和下一批牵引。