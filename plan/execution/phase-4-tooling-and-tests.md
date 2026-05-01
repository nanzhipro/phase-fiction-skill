# Phase 4 执行包

## 必带上下文

- `plan/common.md`
- `plan/phases/phase-4-tooling-and-tests.md`
- `plan/execution/phase-4-tooling-and-tests.md`

## 允许改动

- `scripts/planctl.rb`
- `scripts/planctl`
- `tests/planctl_autonomous_test.rb`
- `CHANGELOG.md`
- `TODO.md`
- `phase-contract-finalize-patch-brief.md`

## 本次不要做

- 不修改 `references/*`、顶层 `README*`、`SKILL.md` 或 `plan/*` 的其他文件。
- 不引入新的依赖、命令接口或额外测试基础设施。
- 不做与本轮 fiction 改造无关的脚本重构。

## 交付检查

- 针对 `status` 或相关 CLI 输出的验证已执行，证明 available phase 的 id 展示正常。
- 触及的测试已运行，且与当前脚本行为一致。
- 如果脚本副本 `scripts/planctl` 需要同步更新，已与 `scripts/planctl.rb` 保持一致。
- 任何新增说明只记录本轮工具层改造，不重新引入旧品牌主语义。

## 执行裁决规则

- 若修复问题必须扩展到白名单外文件，停止并报告边界冲突。
- 若 `scripts/planctl.rb` 与 `scripts/planctl` 最终内容不一致，视为未完成。
- 若测试失败且失败与本轮改动相关，必须先修复或回退该缺陷，再宣布本阶段完成。
