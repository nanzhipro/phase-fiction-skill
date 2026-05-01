# Phase 4: Align tooling copy and verification

## 阶段定位

把仓库中仍然暴露旧品牌或旧工程语义的调度脚本、测试与配套说明同步改到 fiction-first 语境，并补齐最关键的工具层验证，确保对外文案已经改名后，底层 CLI 输出与自动化检查不会继续把项目表现成 phase-contract 工具。

## 阶段目标

- 修正 `scripts/planctl.rb` 与可执行副本 `scripts/planctl` 中仍然暴露旧品牌或错误状态展示的用户可见输出。
- 修复已知的 `status` 可用 phase 展示问题，避免 phase id 丢失。
- 让测试覆盖与当前流程语义保持一致，至少验证 placeholder 推进、autonomous continuation 或 finalize 相关关键行为。
- 视需要补充 `CHANGELOG.md`、`TODO.md` 或说明文档，记录本轮工具层改造结果，但不扩写成新方法论文档。

## 实施范围

本阶段只处理调度脚本、脚本副本、测试和必要的仓库级补充说明，不再回头修改顶层 README、SKILL 或 references 方法论。

## 本阶段产出

- 对齐后的 `scripts/planctl.rb` 与 `scripts/planctl`
- 对齐后的 `tests/planctl_autonomous_test.rb`
- 需要时更新的 `CHANGELOG.md`、`TODO.md` 或补充说明文件

## 明确不做

- 不新增运行时依赖或测试框架。
- 不重写 `planctl` 的底层架构，只做与当前仓库改造直接相关的用户可见语义和验证修复。
- 不修改已经在前几个 phase 完成的对外入口或 methods 文档，除非工具验证要求回补一句说明。

## 完成判定

- `scripts/planctl.rb` 与 `scripts/planctl` 的用户可见文案、状态说明或示例，已经与 fiction-first 项目语义保持一致，且不再把本仓库默认表述为 phase-contract 工程工作流。
- 已知的 status/available phase 展示缺陷已被修复，并有可执行验证证明 phase id 不再丢失。
- `tests/planctl_autonomous_test.rb` 已与当前行为保持一致，并且相关测试可以成功执行。
- 若本阶段补充了 `CHANGELOG.md`、`TODO.md` 或说明文件，内容只记录本轮工具层结果，不与前面 phase 的对外文案产生冲突。
