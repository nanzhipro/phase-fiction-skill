# story 目录说明

> 本文件由 `ruby scripts/planctl finalize` 根据 `story/` 当前文件树生成或刷新。

`story/` 是小说正文与创作资料库。它不是单一草稿目录，而是一套从故事承诺、事实真相、人物关系、结构设计到正文和修订账本逐层展开的工作区。

## 总体层级

```text
story/
|-- premise.md
|-- canon/
|   |-- case-file.md
|   |-- closed-circle-rules.md
|   |-- clue-ledger.md
|   |-- countdown-clock.md
|   |-- relationships.md
|   |-- suspect-web.md
|   `-- timeline.md
|-- cast/
|   |-- a-jiang.md
|   |-- lu-ziheng.md
|   |-- pei-chongli.md
|   |-- pei-jianyue.md
|   |-- su-wenping.md
|   |-- yan-shaotang.md
|   `-- zhou-momo.md
|-- outline/
|   |-- arc-map.md
|   |-- pressure-map.md
|   |-- reveal-beats.md
|   `-- tension-waves.md
|-- draft/
|   |-- part-1/
|   |   `-- chapters-01-08.md
|   |-- part-2/
|   |   `-- chapters-09-16.md
|   `-- part-3/
|       `-- chapters-17-24.md
`-- revision/
    `-- structural-suspense-ledger.md
```

这套层级可以按五层理解：

1. `premise.md` 是故事承诺层，回答这本小说是什么、要给读者什么体验、谜面和终局揭示的基本形状是什么。
2. `canon/` 是事实真相层，记录案件、时间线、线索、规则、倒计时和关系张力，是后续写作不能随意违背的事实底座。
3. `cast/` 与 `outline/` 是执行设计层，前者拆人物动机和关系压力，后者拆结构弧线、揭示节点和张力波形。
4. `draft/` 是正文层，承载章节草稿和分部文件。
5. `revision/` 是复盘修订层，用来记录整稿结构检查、悬念公平性和下一轮修订建议。

## 文件树与职责

### `premise.md`

故事承诺、作品定位、核心谜面、隐藏真相、终局模型和长期推进承诺。

后续任何新增设定或正文修订，都应先检查是否仍然满足这里的故事承诺。

### `canon/`

事实真相层和连续性层，记录案件、时间线、线索、封闭规则、倒计时和关系张力。

- `canon/case-file.md`：案件真相层，记录旧案、现案、假解答、真相闭环和终局公开机制。
- `canon/closed-circle-rules.md`：封闭空间规则，约束现场、权限、开锁、离场和外部介入条件。
- `canon/clue-ledger.md`：线索账本，记录关键线索、误导方式和回收位置。
- `canon/countdown-clock.md`：倒计时节点，说明时间压力如何改变角色选择成本。
- `canon/relationships.md`：人物关系张力，记录利益、旧怨、隐瞒和互相牵制。
- `canon/suspect-web.md`：嫌疑网络，定义嫌疑节点与关键关系压力。
- `canon/timeline.md`：时间线，固定历史事件与正文当下事件的先后关系。

### `cast/`

人物执行层，记录角色动机、恐惧、秘密、资源、暴露代价和叙事功能。

- `cast/a-jiang.md`：《阿绛》相关资料。
- `cast/lu-ziheng.md`：《陆子衡》相关资料。
- `cast/pei-chongli.md`：《裴崇礼》相关资料。
- `cast/pei-jianyue.md`：《裴见月》相关资料。
- `cast/su-wenping.md`：《苏文屏》相关资料。
- `cast/yan-shaotang.md`：《严绍棠》相关资料。
- `cast/zhou-momo.md`：《周嬷嬷》相关资料。

### `outline/`

结构设计层，把事实真相和人物压力转换成章节弧线、揭示节拍与张力波形。

- `outline/arc-map.md`：整体弧线图，说明分部、章节批次和主要转折。
- `outline/pressure-map.md`：压力系统地图，说明外部压力、人物压力和叙事压力如何叠加。
- `outline/reveal-beats.md`：揭示节拍，规定信息何时被误读、翻面、公开和回收。
- `outline/tension-waves.md`：张力波形，控制假解答、中段反转和终局揭示的节奏。

### `draft/`

正文层，承载章节草稿和分部文件。

- `draft/part-1/chapters-01-08.md`：正文文件：Part 1。
- `draft/part-2/chapters-09-16.md`：正文文件：Part 2。
- `draft/part-3/chapters-17-24.md`：正文文件：Part 3。

### `revision/`

修订复盘层，记录整稿结构检查、悬念公平性和下一轮修订建议。

- `revision/structural-suspense-ledger.md`：结构修订账本，记录已成立的悬念点、线索公平性和后续修订建议。

## 推荐阅读顺序

1. `premise.md`
2. `canon/case-file.md`
3. `canon/timeline.md`
4. `canon/clue-ledger.md`
5. `canon/suspect-web.md`
6. `canon/relationships.md`
7. `cast/a-jiang.md`
8. `cast/lu-ziheng.md`
9. `cast/pei-chongli.md`
10. `cast/pei-jianyue.md`
11. `cast/su-wenping.md`
12. `cast/yan-shaotang.md`
13. `cast/zhou-momo.md`
14. `outline/arc-map.md`
15. `outline/pressure-map.md`
16. `outline/reveal-beats.md`
17. `outline/tension-waves.md`
18. `draft/part-1/chapters-01-08.md`
19. `draft/part-2/chapters-09-16.md`
20. `draft/part-3/chapters-17-24.md`
21. `revision/structural-suspense-ledger.md`
22. `canon/closed-circle-rules.md`
23. `canon/countdown-clock.md`

如果只是修正文风或句段，通常从 `draft/` 进入即可；如果修动事实、动机、线索或章节顺序，必须回查 `canon/`、`cast/` 和 `outline/`。

## 维护原则

- 事实优先级：`premise.md` 和 `canon/` 高于 `outline/`，`outline/` 高于 `draft/`，`revision/` 只记录诊断和建议。
- 不把新反转直接写进正文而不更新 `canon/`；否则线索公平性会失效。
- 不让角色为了推动情节突然改变动机；先更新或核对 `cast/`。
- 不让倒计时、封闭规则或世界规则只停留在台词里；如果压力改变了剧情，需能在 `canon/` 或 `outline/` 找到对应机制。
- 新增、合并或拆分章节时，应同步检查结构文件和修订账本，避免正文层和设计层脱节。
