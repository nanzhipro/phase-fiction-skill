# Phase 3: Rewrite skill discovery and bilingual docs

## 阶段定位

把仓库最外层、最容易被用户与 Agent 发现的入口文档全部改成 fiction-first 语义，使本项目从品牌、触发词、使用场景、安装指引到文档地图都明确指向长篇小说创作与修订，而不再保留“工程迁移 / 长任务编码工作流”作为默认印象。

## 阶段目标

- 将 `SKILL.md` 的技能名称、描述、触发词、适用/不适用场景、流程步骤改写为小说创作工作流。
- 将 `README.md` 与 `README.zh-CN.md` 的标题、简介、安装说明、推荐场景、快速开始、文档索引改写为 `phase-fiction-skill`。
- 将 `assets/README.md` 的视觉说明同步到 fiction 品牌语义，避免资产说明仍停留在 phase-contract 概念。
- 维持顶层 README 的概括性，把方法论细节继续下放到 `references/*`。

## 实施范围

本阶段只处理“发现入口”与“品牌表面层”的对外文案，不改底层调度脚本、模板机制和测试实现。

## 本阶段产出

- fiction-first 的 `SKILL.md`
- 对齐后的 `README.md` 与 `README.zh-CN.md`
- 对齐后的 `assets/README.md`

## 明确不做

- 不修改 `scripts/planctl`、`tests/*` 或 `references/*` 的底层机制。
- 不新增新的运行时依赖、构建步骤或发布流程。
- 不把顶层 README 扩写成完整方法论手册。

## 完成判定

- `SKILL.md` frontmatter 中的 `name` 已改为 `phase-fiction-skill`，描述与触发词已明确指向小说创作、续写、修订或系列规划。
- `README.md` 与 `README.zh-CN.md` 的主标题、简介、安装命令示例、推荐场景和快速开始均已改成 fiction 语境，且不再把工程迁移 / 新产品构建作为默认主案例。
- `assets/README.md` 已说明视觉符号对应的是故事 phase、创作上下文或稿件收束，而非 phase-contract 的工程执行语义。
- 至少完成一轮针对上述文件的语义校验，确认旧的工程主语义已被清除，新的 fiction 关键词已出现。
