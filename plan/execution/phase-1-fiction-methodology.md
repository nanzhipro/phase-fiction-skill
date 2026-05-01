# Phase 1 执行包

本文件不能单独使用。执行 Phase 1 时，必须同时携带完整的 `plan/common.md` 和 `plan/phases/phase-1-fiction-methodology.md`。

## 必带上下文

- `plan/common.md`
- `plan/phases/phase-1-fiction-methodology.md`

## 执行目标

- 把现有长任务方法论改写成适用于长篇小说策划、起稿、续写与修订的工作流。
- 让 glossary 成为 fiction 语境下的配套术语表，而不是工程流程词典。

## 本次允许改动

- `references/methodology.md`
- `references/glossary.md`

## 本次不要做

- 不修改 `SKILL.md`、README、README.zh-CN。
- 不修改 `references/templates.md`、`references/phase-templates.md`、`references/agent-instructions-template.md`。
- 不修改 `scripts/planctl.rb`、`scripts/planctl` 或 `tests/*`。
- 不为了补充背景而扩展到具体小说正文示例库或新增外部依赖。

## 交付检查

- 方法论文档已从工程型任务示例切换到小说创作示例。
- 方法论文档中的阶段粒度、失败模式和恢复协议仍然可映射到 planctl 机制。
- 术语表中的核心词条能支撑后续模板改造，不再与旧领域冲突。
- 两份文档的关键术语和叙述口径一致。

## 执行裁决规则

- 如果文档改写削弱了 manifest/state/handoff/planctl 这套外部化机制，直接判定无效并回退到流程边界。
- 如果方法论只停留在泛泛的“写得更好”，没有给出阶段级或场景级操作框架，直接判定无效。
- 如果 glossary 仍以工程迁移、重构、合规等示例为主，直接判定无效并继续收敛到小说语境。
