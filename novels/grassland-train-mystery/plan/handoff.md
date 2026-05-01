# Phase-Fiction Execution Handoff

本文件用于长篇创作执行时的压缩恢复。不要一次性重新加载全部 phase 文档；恢复时按本文档与 manifest 继续。

## 当前状态

- State file: `plan/state.yaml`
- Handoff file: `plan/handoff.md`
- Updated at: `2026-05-01T03:01:46Z`
- Completed phases: `phase-0-premise-promise, phase-1-cast-engine, phase-2-truth-lattice, phase-3-route-pressure, phase-4-arc-wave-design, phase-5-opening-matrix, phase-6-middle-matrix`

## 最近完成

- `phase-4-arc-wave-design` Map the macro arc and suspense waves: Mapped the whole-novel arc into ten macro beats and an eleven-node suspense/relationship/action wave pattern anchored by the false solve, midpoint flip, and terminal showdown.
- next focus: Promote phase-5 into a formal contract and break the opening movement into scene cards for setup, the first murder, and the first suspect net.
- `phase-5-opening-matrix` Build the opening movement scene matrix: Broke the opening movement into a six-scene matrix covering boarding, the aborted handoff, the first murder, the system lockdown, and the first suspect net.
- next focus: Promote phase-6 into a formal contract and map the middle-movement trap scenes, false solve, and midpoint flip into concrete scene cards.
- `phase-6-middle-matrix` Build the middle movement trap matrix: Broke the middle movement into six trap scenes covering the old-case lock-in, the false solve, Han Songyuan's reversal, and the exposure of the living witness.
- next focus: Promote phase-7 into a formal contract and map the endgame pursuit, false handoff, terminal showdown, and aftermath into final scene cards.

## 下一 Phase

- `phase-7-endgame-matrix` Build the endgame convergence matrix
- plan: `plan/phases/phase-7-endgame-matrix.md`
- execution: `plan/execution/phase-7-endgame-matrix.md`
- status: `placeholder contracts need upgrade first (plan/phases/phase-7-endgame-matrix.md, plan/execution/phase-7-endgame-matrix.md)`

下一步读取顺序：
1. `plan/common.md`
2. `plan/phases/phase-7-endgame-matrix.md`
3. `plan/execution/phase-7-endgame-matrix.md`

## 压缩恢复顺序

1. `plan/manifest.yaml`
2. `plan/handoff.md`
3. `next.phase.required_context`

## 压缩控制规则

- 永远不要一次性加载所有 phase 文档。
- 只在当前 phase 读取 plan/common.md、当前 phase plan 和当前 phase execution。
- 每完成一个 phase 后更新 handoff，再进入下一 phase。

## 连续执行命令

- next: `ruby scripts/planctl advance --strict`
- complete: `ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue`
- handoff-repair (manual recovery only): `ruby scripts/planctl handoff --write`
