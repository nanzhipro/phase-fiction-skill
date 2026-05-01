# Phase 2: Rewrite fiction contract templates

## 阶段定位

把仓库中所有可复用模板从“工程/代码任务模板”改造成“小说创作模板”，让后续任意新项目都能直接生成 fiction-first 的 plan/common/phase/execution/agent 约束。

## 必带上下文

- plan/common.md
- Phase 1 已完成，小说方法论与术语表已作为统一理论底座落盘

## 阶段目标

- 用小说创作场景重写基础 plan 模板、phase 模板与 workflow 模板。
- 让 agent instructions template 与 workflow template 在小说语境下仍保持严格、客观、可续跑。
- 让模板示例天然覆盖故事承诺、人物、场景、修订等长篇创作关键切面。

## 实施范围

- `references/templates.md`
- `references/phase-templates.md`
- `references/agent-instructions-template.md`
- `references/workflow-template.md`

## 本阶段产出

- 一套可直接复用的 fiction-first 模板体系
- 一份与小说创作阶段切分相匹配的 workflow template
- 一份同步于 fiction 语境的 agent instructions template

## 明确不做

- 不重写 `SKILL.md`、README、README.zh-CN 的对外入口文案
- 不修改 `scripts/planctl.rb` 或 `tests/*`
- 不新增新的模板文件命名体系或运行时依赖

## 完成判定

- `references/templates.md` 的示例项目、硬约束与 handoff 示例已切换到小说创作语境。
- `references/phase-templates.md` 的定位合同与执行合同示例已能直接用于故事承诺、角色、场景、修订等 fiction phase。
- `references/agent-instructions-template.md` 与 `references/workflow-template.md` 不再以“代码实现/业务 phase”为默认用词，而是能覆盖创作与修订阶段。
- 四份模板之间的术语一致，不再出现与 phase-1 方法论冲突的旧领域措辞。

## 依赖关系

- 依赖 Phase 1
