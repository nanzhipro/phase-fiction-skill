# Phase-Fiction Execution Handoff

本文件用于长篇创作执行时的压缩恢复。不要一次性重新加载全部 phase 文档；恢复时按本文档与 manifest 继续。

## 当前状态

- State file: `plan/state.yaml`
- Handoff file: `plan/handoff.md`
- Updated at: `2026-05-01T02:51:27Z`
- Completed phases: `phase-0-premise-promise`

## 最近完成

- `phase-0-premise-promise` Lock the steppe-train suspense promise: Locked the story promise, failure stakes, hidden-old-case anchor, and reader-facing suspense contract for the steppe train novel.
- next focus: Build the suspect cast, motive lattice, and relationship pressure around Lin Yan and the key passengers.

## 下一 Phase

- `phase-1-cast-engine` Build the suspect cast and relationship engine
- plan: `plan/phases/phase-1-cast-engine.md`
- execution: `plan/execution/phase-1-cast-engine.md`

下一步读取顺序：
1. `plan/common.md`
2. `plan/phases/phase-1-cast-engine.md`
3. `plan/execution/phase-1-cast-engine.md`

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
