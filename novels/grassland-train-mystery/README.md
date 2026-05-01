# 草原列车疑云

一部长篇悬疑小说项目：故事从一列奔驰在草原深处的列车开始，真相被埋在车轮声、风噪和旧案残骸之间。

## 项目定位

- 类型：现实质感的封闭空间悬疑 / 追凶 / 真相反转
- 目标长度：8-12 万字
- 核心快感：移动中的密室危机、互相伪装的乘客、草原地理与时间倒计时共同施压
- 当前状态：已完成 12-phase 长任务脚手架，当前待执行 phase 为 `phase-0-premise-promise`

## 一句话故事承诺

调查记者林雁在一列横穿草原的夜行列车上接到一封匿名求救信，随后车上发生一宗伪装成意外的命案；她逐步发现整列车上的关键乘客都与十三年前一起被掩埋的草原儿童失踪案有关，而列车抵达终点前若她找不出真凶与幕后利益链，她不仅会成为下一名受害者，也会让唯一还活着的证人永远消失。

## 12 Phase 地图

1. `phase-0-premise-promise`：锁定故事承诺、危险感与创作边界
2. `phase-1-cast-engine`：建立角色阵列与怀疑链
3. `phase-2-truth-lattice`：落盘隐秘真相、线索网和旧案真相
4. `phase-3-route-pressure`：设计列车路线、空间逻辑与时间压力
5. `phase-4-arc-wave-design`：搭建宏观剧情弧与悬念波形
6. `phase-5-opening-matrix`：拆解开篇场景矩阵
7. `phase-6-middle-matrix`：拆解中段陷阱矩阵
8. `phase-7-endgame-matrix`：拆解终局收束矩阵
9. `phase-8-opening-draft-batch`：起草开篇正文批次
10. `phase-9-middle-draft-batch`：起草中段正文批次
11. `phase-10-endgame-draft-batch`：起草终局正文批次
12. `phase-11-structural-revision`：做结构悬念修订与连续性清理

## 日常循环

```bash
ruby scripts/planctl advance --strict
ruby scripts/planctl complete <phase-id> --summary "..." --next-focus "..." --continue
```

所有 phase 完成后，再运行：

```bash
ruby scripts/planctl finalize
```