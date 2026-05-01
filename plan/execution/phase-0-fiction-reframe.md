# Phase 0 执行包

本文件不能单独使用。执行 Phase 0 时，必须同时携带完整的 `plan/common.md` 和 `plan/phases/phase-0-fiction-reframe.md`。

## 必带上下文

- `plan/common.md`
- `plan/phases/phase-0-fiction-reframe.md`

## 执行目标

- 在仓库中建立面向小说改造任务的 plan 基础设施。
- 同步三份 agent 指令并确保后续 strict 调度可用。
- 提供一个可直接由 Ruby 调用的 `scripts/planctl` 入口。

## 本次允许改动

- `plan/**`
- `.github/copilot-instructions.md`
- `CLAUDE.md`
- `AGENTS.md`
- `scripts/planctl`

## 本次不要做

- 不修改 `SKILL.md`、`README.md`、`README.zh-CN.md`。
- 不修改 `references/*` 的正式方法论文档与模板正文。
- 不修改 `scripts/planctl.rb` 或 `tests/*`。
- 不手工编辑 `plan/state.yaml` 伪造完成状态。

## 交付检查

- plan 基础文件与 phase 合同已创建且路径与 manifest 一致。
- 非当前 phase 的合同文件已按 manifest 落盘，且 strict 调度能够识别它们尚未进入实施状态。
- 三份 agent 指令文件字节一致。
- `scripts/planctl` 可通过 Ruby 执行，并能读到新建的 manifest/state/handoff。

## 执行裁决规则

- 如果任何 future phase 缺少配对占位合同，直接判定本阶段无效并补齐文件后重试。
- 如果三份 agent 指令内容不一致，直接判定本阶段无效，必须同步后再继续。
- 如果 `advance --strict` 不能返回当前 phase 的合法三文件上下文，直接回到 plan scaffold 修正，不得进入后续 phase。
