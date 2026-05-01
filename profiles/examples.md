# Derived Phase Graph Examples

本页展示的是 **当前 profile + overlay schema 在“无用户 phase override”前提下的确定性展开结果**。

用途只有一个：让使用者快速看懂“选了什么 profile / overlays，最后 `manifest.phases` 大概会长成什么样”。

阅读方式：

- 先看 base profile 自带的 `defaults.phase_catalog`
- 再按 overlay 选择顺序应用 `phase_merge.operations`
- 最后再把 `artifact_additions` 和 `common_md_must_cover` 合并进去
- 若用户额外提供 phase override，以 override 结果为最终版本

本页不演示 `custom` profile，因为 `custom` 的设计目标就是允许退化为手工 phase 图。

## Example 1: Mystery / Thriller + `closed-circle` + `countdown`

输入：

- profile: `mystery-thriller`
- engine: `clue-driven`
- overlays:
  - `closed-circle`
  - `countdown`

Base phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-truth-lattice`
4. `phase-3-pressure-system`
5. `phase-4-arc-wave-design`
6. `phase-5-plus-draft-batches`

Overlay effects:

- `closed-circle` 只要求当前图中必须存在：
  - `phase-1-cast-engine`
  - `phase-3-pressure-system`
- `countdown` 只要求当前图中必须存在：
  - `phase-3-pressure-system`
  - `phase-4-arc-wave-design`

Final phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-truth-lattice`
4. `phase-3-pressure-system`
5. `phase-4-arc-wave-design`
6. `phase-5-plus-draft-batches`

Additional artifacts:

- `story/canon/closed-circle-rules.md`
- `story/canon/countdown-clock.md`

Additional `plan/common.md` concerns:

- 封闭规则一旦建立就不能中途失效
- 倒计时不能只在嘴上存在，必须改变角色选择成本

说明：这个组合不会插入新 phase，但会强化 pressure-system 与 reveal arc 的存在性，因此很适合“列车 / 孤岛 / 大楼”这类封闭倒计时悬疑。

## Example 2: Romance + `dual-pov` + `slow-burn`

输入：

- profile: `romance`
- engine: `relationship-driven`
- overlays:
  - `dual-pov`
  - `slow-burn`

Base phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-relationship-arc`
4. `phase-3-scene-beat-design`
5. `phase-4-plus-draft-batches`

Overlay effects:

- `dual-pov` 要求必须存在：
  - `phase-1-cast-engine`
  - `phase-3-scene-beat-design`
- `slow-burn` 要求必须存在：
  - `phase-2-relationship-arc`
  - `phase-3-scene-beat-design`

Final phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-relationship-arc`
4. `phase-3-scene-beat-design`
5. `phase-4-plus-draft-batches`

Additional artifacts:

- `story/canon/pov-switch-rules.md`
- `story/outline/slow-burn-ladder.md`

Additional `plan/common.md` concerns:

- 每个 POV 都必须带来不可替代的信息或情绪压力
- 关系推进必须持续给读者可感知回报，不能只靠拖延

说明：这个组合同样不插 phase，但会把关系推进与 POV 分配规则显式落盘，避免 romance 项目在起稿阶段才临时决定“谁来讲这段关系”。

## Example 3: Epic Fantasy + `multi-pov` + `serial-release`

输入：

- profile: `epic-fantasy`
- engine: `quest-driven`
- overlays:
  - `multi-pov`
  - `serial-release`

Base phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-world-canon`
4. `phase-3-conflict-fronts`
5. `phase-4-arc-roadmap`
6. `phase-5-plus-draft-batches`

Overlay effects:

- `multi-pov` 在 `phase-1-cast-engine` 之后确保存在：
  - `phase-2-viewpoint-discipline`
- `serial-release` 要求必须存在：
  - `phase-5-plus-draft-batches`
- `serial-release` 在 `phase-5-plus-draft-batches` 之后确保存在：
  - `phase-6-reader-retention-pass`

Final phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-viewpoint-discipline`
4. `phase-2-world-canon`
5. `phase-3-conflict-fronts`
6. `phase-4-arc-roadmap`
7. `phase-5-plus-draft-batches`
8. `phase-6-reader-retention-pass`

Additional artifacts:

- `story/canon/pov-roster.md`
- `story/outline/release-batch-map.md`

Additional `plan/common.md` concerns:

- 群像扩张不能吞掉主线目标
- 每个连载批次都要有独立满足感与下一批牵引

说明：这是一个典型的“overlay 真正改 phase 图”的例子。`multi-pov` 和 `serial-release` 都会插入结构 phase，因此它比单纯的 base epic fantasy 更适合群像连载项目。

## Example 4: Literary + `nonlinear-timeline` + `dual-pov`

输入：

- profile: `literary`
- engine: `character-study`
- overlays:
  - `nonlinear-timeline`
  - `dual-pov`

Base phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-inner-conflict-map`
4. `phase-3-motif-structure`
5. `phase-4-scene-pressure-design`
6. `phase-5-plus-draft-batches`

Overlay effects:

- `nonlinear-timeline` 在 `phase-1-cast-engine` 之后确保存在：
  - `phase-2-timeline-map`
- `dual-pov` 要求必须存在：
  - `phase-1-cast-engine`
  - `phase-4-scene-pressure-design`

Final phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-timeline-map`
4. `phase-2-inner-conflict-map`
5. `phase-3-motif-structure`
6. `phase-4-scene-pressure-design`
7. `phase-5-plus-draft-batches`

Additional artifacts:

- `story/canon/timeline-split.md`
- `story/canon/pov-switch-rules.md`

Additional `plan/common.md` concerns:

- 时间线错位必须服务主题或揭示，而不是制造无意义迷雾
- 每个 POV 都必须带来不可替代的信息或情绪压力

说明：这个组合把文学向项目里最容易“写到一半才补救”的两个高风险点提前外部化了：时间线拆分和 POV 切换。

## Example 5: Horror + `closed-circle` + `countdown` + `serial-release`

输入：

- profile: `horror`
- engine: `survival-pressure`
- overlays:
  - `closed-circle`
  - `countdown`
  - `serial-release`

Base phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-threat-rules`
4. `phase-3-dread-escalation-design`
5. `phase-4-survival-wave-batches`
6. `phase-5-plus-draft-batches`

Overlay effects:

- `closed-circle` 要求必须存在：
  - `phase-1-cast-engine`
  - `phase-3-dread-escalation-design`
- `countdown` 要求必须存在：
  - `phase-3-dread-escalation-design`
  - `phase-4-survival-wave-batches`
- `serial-release` 要求必须存在：
  - `phase-5-plus-draft-batches`
- `serial-release` 在 `phase-5-plus-draft-batches` 之后确保存在：
  - `phase-6-reader-retention-pass`

Final phase graph:

1. `phase-0-premise-promise`
2. `phase-1-cast-engine`
3. `phase-2-threat-rules`
4. `phase-3-dread-escalation-design`
5. `phase-4-survival-wave-batches`
6. `phase-5-plus-draft-batches`
7. `phase-6-reader-retention-pass`

Additional artifacts:

- `story/canon/closed-circle-rules.md`
- `story/canon/countdown-clock.md`
- `story/outline/release-batch-map.md`

Additional `plan/common.md` concerns:

- 封闭规则一旦建立就不能中途失效
- 倒计时不能只在嘴上存在，必须改变角色选择成本
- 每个连载批次都要有独立满足感与下一批牵引

说明：这是一种很典型的“围困式长篇恐怖连载”配置。base horror 负责威胁规则与求生波次，overlays 负责封闭空间、倒计时和批次牵引。

## Maintenance Rule

如果之后你改了某个 starter profile 的 `defaults.phase_catalog`，或者改了 overlay 的 `phase_merge.operations`，本页里的对应示例也必须一起刷新。

判断标准很简单：**示例页描述的 phase 图必须能直接从当前数据文件推导出来，而不是保留历史口径。**
