# Phase-Fiction Execution Handoff

本文件用于长篇创作执行时的压缩恢复。不要一次性重新加载全部 phase 文档；恢复时按本文档与 manifest 继续。

## 当前状态

- State file: `plan/state.yaml`
- Handoff file: `plan/handoff.md`
- Updated at: `2026-05-01T02:58:04Z`
- Completed phases: `phase-0-premise-promise, phase-1-cast-engine, phase-2-truth-lattice, phase-3-route-pressure`

## 最近完成

- `phase-1-cast-engine` Build the suspect cast and relationship engine: Built the passenger cast engine with nine motive-driven character files and a relationship map covering suspicion, leverage, and future rupture points.
- next focus: Promote phase-2 into a formal contract and lock the hidden-truth lattice, clue ledger, and unified old-case timeline.
- `phase-2-truth-lattice` Establish the hidden truth and clue lattice: Locked the hidden truth: the old child-transfer crime, the on-train murder purpose, the leverage chain, a 14-item clue ledger, and a 31-node unified timeline.
- next focus: Promote phase-3 into a formal contract and design the train route, compartment logic, station pressure, and movement constraints around the countdown.
- `phase-3-route-pressure` Design the route logic and countdown pressure: Turned the train into a pressure system with route hotspots, compartment control nodes, and a countdown timeline tied to stations, signal gaps, and the approach to the terminal.
- next focus: Promote phase-4 into a formal contract and map the macro suspense arc, reversals, and pressure-wave pattern across the whole novel.

## 下一 Phase

- `phase-4-arc-wave-design` Map the macro arc and suspense waves
- plan: `plan/phases/phase-4-arc-wave-design.md`
- execution: `plan/execution/phase-4-arc-wave-design.md`
- status: `placeholder contracts need upgrade first (plan/phases/phase-4-arc-wave-design.md, plan/execution/phase-4-arc-wave-design.md)`

下一步读取顺序：
1. `plan/common.md`
2. `plan/phases/phase-4-arc-wave-design.md`
3. `plan/execution/phase-4-arc-wave-design.md`

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
