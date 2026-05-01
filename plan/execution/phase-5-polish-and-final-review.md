# Phase 5 执行包

## 必带上下文

- `plan/common.md`
- `plan/phases/phase-5-polish-and-final-review.md`
- `plan/execution/phase-5-polish-and-final-review.md`

## 执行目标

- 只在最终白名单范围内清理仍然可见的旧品牌/旧工程语义残留。
- 对必须保留的兼容性项做最小且清晰的说明，不让兼容性文本继续充当主品牌。
- 用针对性搜索与 `planctl` 体检验证最终一致性，而不是凭主观感觉宣布完成。

## 允许改动

- `CHANGELOG.md`
- `TODO.md`
- `phase-contract-finalize-patch-brief.md`
- `assets/README.md`
- `assets/phase-contract-logo.svg`
- `assets/phase-contract-mark.svg`

## 本次不要做

- 不修改 `README.md`、`README.zh-CN.md`、`SKILL.md` 或 `references/*` 主体内容，除非发现与本轮清扫直接相关且无法绕开的冲突。
- 不改动 `scripts/planctl.rb`、`scripts/planctl`、测试逻辑或既有合同哨兵常量，只为了追求内部命名绝对统一而冒险破坏兼容性。
- 不重命名现有兼容性资产文件名。
- 不新增依赖、脚本或构建步骤。

## 交付检查

- 针对白名单范围的残留品牌搜索已执行，并且除兼容性保留项外不再有旧主品牌残留。
- `ruby scripts/planctl status` 与 `ruby scripts/planctl doctor` 已执行，确认最终清扫没有破坏 workflow 状态。
- 若修改了 SVG 或资产说明，可见标题/描述文本已经转为 fiction-first 语义，同时文件名兼容说明仍然成立。
- 任何保留的旧词都能指出明确理由，而不是遗漏未改。

## 执行裁决规则

- 若发现必须修改白名单外文件才能完成本轮清扫，停止并报告边界冲突。
- 若搜索结果显示剩余残留仍在把仓库主品牌表述为旧工作流品牌或工程工作流，视为未完成。
- 若 `planctl status` 或 `doctor` 因本轮改动失败，必须先修复或回退相关改动，再宣布本阶段完成。
