# Phase 0: Establish fiction-first positioning scaffold

## 阶段定位

先把当前仓库从“工程/代码长任务 skill”切换到“小说创作长任务 skill”的明确方向，并为后续大改造建立可续跑的执行骨架。

## 必带上下文

- plan/common.md
- 当前仓库仍保留原有工程导向文案，需要先建立新定位边界

## 阶段目标

- 为本次改造创建完整 plan 骨架、state/handoff 与 phase 链路。
- 明确小说导向改造的主线、非目标与验证要求。
- 建立同步 agent 指令层与可运行的 scripts/planctl 入口，使后续 phase 能按 strict 模式续跑。

## 实施范围

- plan/ 下的 manifest、common、workflow、state、handoff 与 phase/execution 合同
- `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md`
- `scripts/planctl` 入口文件

## 本阶段产出

- 可运行的 plan/ 基础设施
- 一份围绕小说创作改造的 phase 路线图
- 三份同步的 agent 执行规约
- 一个可执行的 `scripts/planctl` 入口

## 明确不做

- 不在本阶段重写 SKILL.md、README.md、README.zh-CN.md 的主体文案
- 不在本阶段重写 references/methodology.md 与 references/templates.md 等主模板正文
- 不在本阶段调整 tests/ 或 scripts/planctl.rb 的主逻辑

## 完成判定

- `plan/manifest.yaml`、`plan/common.md`、`plan/workflow.md`、`plan/state.yaml`、`plan/handoff.md` 均已创建。
- 当前 phase 与后续 phase 在 manifest 中都有合法 plan/execution 引用，且所有引用文件都已落盘。
- `.github/copilot-instructions.md`、`CLAUDE.md`、`AGENTS.md` 三份文件存在且字节一致。
- `scripts/planctl` 存在且可通过 `ruby scripts/planctl status` 运行。
- `ruby scripts/planctl doctor` 与 `ruby scripts/planctl advance --strict` 均成功，且 `advance` 返回当前 phase 的三文件上下文。

## 依赖关系

- 无
