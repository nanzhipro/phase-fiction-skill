# Phase 2 执行包

本文件不能单独使用。执行 Phase 2 时，必须同时携带完整的 `plan/common.md` 和 `plan/phases/phase-2-fiction-contracts.md`。

## 必带上下文

- `plan/common.md`
- `plan/phases/phase-2-fiction-contracts.md`

## 执行目标

- 把基础模板改写成适用于长篇小说创作与修订的默认脚手架。
- 让 workflow/agent template 保持严格流程控制，但示例与口径都切到 fiction 任务。

## 本次允许改动

- `references/templates.md`
- `references/phase-templates.md`
- `references/agent-instructions-template.md`
- `references/workflow-template.md`

## 本次不要做

- 不修改 `SKILL.md`、README、README.zh-CN。
- 不修改 `scripts/planctl.rb`、`scripts/planctl` 或 `tests/*`。
- 不新增脱离现有结构的新目录约定。
- 不把模板写成“如何写得更美”的抽象写作课，而忽略合同与边界机制。

## 交付检查

- 模板示例默认项目从工程/迁移项目切换为小说项目或手稿项目。
- phase template 中的完成判定与执行裁决规则可以直接约束创作层、结构层和修订层工作。
- workflow template 的恢复、complete、finalize 描述仍与 planctl 行为对齐。
- agent instructions template 与 workflow template 之间没有术语冲突。

## 执行裁决规则

- 如果模板改写导致三文件上下文律或 `complete` / `advance` / `finalize` 规则失真，直接判定无效。
- 如果 phase template 仍默认以“代码实现、模块改动、构建产物”为核心示例，直接判定无效。
- 如果 agent template 变成泛泛创意建议，而不是严格执行规约，直接判定无效。
