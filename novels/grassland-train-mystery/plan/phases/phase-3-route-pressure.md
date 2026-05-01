# Phase 3: Design the route logic and countdown pressure

## 阶段定位

把列车从抽象的封闭空间变成可操作的行动棋盘，明确车厢结构、停靠节奏、通讯衰减和人群流动如何共同给悬疑施压。

## 必带上下文

- `plan/common.md`
- Phase 2 已完成

## 阶段目标

- 明确列车编组、关键车厢、死角和能被操控的信息节点。
- 明确从发车到终点的站停、通讯窗口与风险上升节奏。
- 把时间压力写成后续场景可直接调用的倒计时规则。

## 实施范围

- `story/canon/route-map.md`
- `story/canon/train-logic.md`
- `story/canon/timeline.md`

## 本阶段产出

- 一份路线与站点图
- 一份列车空间逻辑文档
- 一份补足倒计时细节的统一时间线

## 明确不做

- 不拆 scene cards
- 不写线索台账新增条目以外的案件骨架
- 不起草正文

## 完成判定

- `story/canon/route-map.md` 明确列出至少 6 个关键站点或区段及各自风险意义。
- `story/canon/train-logic.md` 明确列出至少 8 个关键空间节点与可执行的行动限制。
- `story/canon/timeline.md` 补足列车旅程中的站停窗口、通讯窗口和终点前高压时段。

## 依赖关系

- 依赖 Phase 2