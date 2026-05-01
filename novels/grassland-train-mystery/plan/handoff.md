# Phase-Fiction Execution Handoff

本文件用于长篇创作执行时的压缩恢复。不要一次性重新加载全部 phase 文档；恢复时按本文档与 manifest 继续。

## 当前状态

- State file: `plan/state.yaml`
- Handoff file: `plan/handoff.md`
- Updated at: `2026-05-01T02:54:00Z`
- Completed phases: `phase-0-premise-promise, phase-1-cast-engine`

## 最近完成

- `phase-0-premise-promise` Lock the steppe-train suspense promise: Locked the story promise, failure stakes, hidden-old-case anchor, and reader-facing suspense contract for the steppe train novel.
- next focus: Build the suspect cast, motive lattice, and relationship pressure around Lin Yan and the key passengers.
- `phase-1-cast-engine` Build the suspect cast and relationship engine: Built the passenger cast engine with nine motive-driven character files and a relationship map covering suspicion, leverage, and future rupture points.
- next focus: Promote phase-2 into a formal contract and lock the hidden-truth lattice, clue ledger, and unified old-case timeline.

## 下一 Phase

- `phase-2-truth-lattice` Establish the hidden truth and clue lattice
- plan: `plan/phases/phase-2-truth-lattice.md`
- execution: `plan/execution/phase-2-truth-lattice.md`
- status: `placeholder contracts need upgrade first (plan/phases/phase-2-truth-lattice.md, plan/execution/phase-2-truth-lattice.md)`

下一步读取顺序：
1. `plan/common.md`
2. `plan/phases/phase-2-truth-lattice.md`
3. `plan/execution/phase-2-truth-lattice.md`

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
