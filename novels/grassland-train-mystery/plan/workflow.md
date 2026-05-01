# Phase-Fiction 执行流程说明

本文件说明《草原列车疑云》如何按既定顺序推进，并在 AI 上下文压缩后恢复执行。

## 环境前提

本项目默认运行在 git 工作区中。`scripts/planctl` 的 `advance`、`resolve`、`complete` 与 `handoff` 在非 git 工作区下会拒绝执行；仅 `status` 保留诊断能力。

## 目标

这套流程解决四件事：

- 明确当前应该推进哪个故事 phase
- 防止跳过前置依赖或越界改动
- 把每一轮阶段完成事实写回仓库文件
- 让压缩恢复只需 manifest、handoff 和当前 phase 三份上下文

## 核心角色

### `plan/manifest.yaml`

唯一流程定义来源，声明 phase 顺序、依赖关系、`required_context` 与连续执行规则。

### `plan/common.md`

全局长期创作约束，负责守住故事承诺、叙事纪律、设定底线和内容边界。

### `plan/phases/*.md`

阶段蓝图，定义“这个阶段是什么、产出什么、做到什么算完成”。

### `plan/execution/*.md`

执行围栏，定义“这次实施能碰什么、不能碰什么、交付检查是什么”。

### `scripts/planctl`

唯一流程入口。用它决定当前 phase、推进账本、刷新 handoff，并在 phase 完成后留下 git 里程碑。

### `plan/state.yaml`

执行账本，记录哪些 phase 已完成、摘要、下一焦点和时间戳。

### `plan/handoff.md`

压缩恢复锚点，记录当前状态、最近完成和下一步读取顺序。

### Agent 强制层

`.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 三份内容保持一致，强制任何兼容 Agent 在 phase 任务里先读 manifest、遵守 `planctl`、只加载三份必带上下文。

## 连续执行的标准循环

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
```

若 `advance --strict` 返回 `ACTION: promote_placeholder`，先把当前 phase 的两份占位合同升级成正式合同，再重跑同一条命令。

## 压缩恢复顺序

1. 读取 `plan/manifest.yaml`
2. 读取 `plan/handoff.md`
3. 运行 `ruby scripts/planctl advance --strict`

不要一次性重新加载全部 phase 文档。每轮只保留：`plan/common.md`、当前 phase 的 plan、当前 phase 的 execution，以及必要时的 handoff。

## 结束规则

当 `advance --strict` 返回 `ACTION: finalize` 时，说明全部 phase 已完成。此时必须运行：

```bash
ruby scripts/planctl finalize
```

在 `finalize` 之前，不得直接对人类宣告项目结束。