# 概念与名词表 / Glossary

> 一份 Phase-Fiction Workflow 的词典。它把“小说创作”和“长任务调度”这两套概念钉在同一张图上。

> "稳定的长篇创作，不应依赖模型瞬时记忆，而应依赖落盘的故事秩序。"

---

## 1. 核心模型（Core Model）

| 名词 | 英文 | 在本工作流中的作用 |
| --- | --- | --- |
| 故事合同链 | Story Contract Chain | 把一部长篇/系列小说拆成 `Phase₀ → Phase₁ → … → Phaseₙ` 的有序序列。它不是章节列表，而是创作层次与修订层次的推进顺序。 |
| Phase | Phase | 一次可独立验收、可回退、可落盘的创作工作段。示例：故事承诺、人物引擎、场景矩阵、结构修订。 |
| 定位合同 | Positioning Contract (`plan/phases/phase-X.md`) | 回答“这个阶段是什么”。它定义目标、范围、产出与完成判定。 |
| 执行合同 | Execution Contract (`plan/execution/phase-X.md`) | 回答“这一轮能碰什么”。它定义允许改动、禁止改动、交付检查与一票否决规则。 |
| 双层合同 | Two-Layer Contract | 定位合同 + 执行合同。前者管目标，后者管边界，合并写会导致结构漂移。 |
| 故事承诺 | Story Promise | 这部小说最初向读者承诺的核心快感、问题或情感体验。承诺不清，长篇必散。 |
| 情节引擎 | Plot Engine | 让故事持续推进的动力结构，通常由“欲望 / 障碍 / 代价 / 时钟 / 揭示”组成。 |
| 场景矩阵 | Scene Matrix | 用表格或卡片管理场景序列的系统，至少记录目标、阻力、变化、回收点。 |
| 悬念账本 | Suspense Ledger | 记录伏笔、误导、揭示、回收与延迟解释的外部清单。 |
| 设定圣经 | Story Bible / Canon Ledger | 记录世界规则、时间线、角色关系、专有名词和连续性底线的权威文档。 |

## 2. 三不变量（Three Invariants）

| 代号 | 名词 | 含义 | 违反后果 |
| --- | --- | --- | --- |
| **I1** | 单一活跃合同 / Single Active Phase | 任意时刻只有一个创作阶段处于实施中 | 同时改结构、人物、语言，最后谁都没改稳 |
| **I2** | 三文件上下文律 / Three-File Context Law | 工作窗口恒定为 `common.md + phase + execution` | 设定、节奏、当前目标互相污染 |
| **I3** | 完成即事实 / Done Means Written | 阶段完成必须写入 `state.yaml` | 以为已经定稿的线索、人物或修订结论无法追溯 |

## 3. 小说长任务的典型失败模式（Failure Modes）

| 失败 | 症状 | Phase-Fiction 的封堵手段 |
| --- | --- | --- |
| 故事承诺漂移 / Promise Drift | 开头的类型 promise 后续被写丢 | `common.md` + premise phase + 恢复协议 |
| 人物内核失真 / Character Drift | 角色说话、选择和恐惧跨章节失真 | 角色 phase + canon ledger + revision pass |
| 张力塌陷 / Tension Collapse | 章节很多，但冲突与代价没有升级 | 场景矩阵 + 交付检查中的“变化”判据 |
| 连续性断裂 / Canon Break | 时间线、设定、关系网互相打架 | story bible + execution 边界 + handoff |
| 修订次序混乱 / Revision Blur | 结构、人物、语言问题混改 | 按修订轮次切 phase |
| 压缩失忆 / Compression Amnesia | 换会话后忘记伏笔与修订结论 | `handoff.md` + `resume --strict` |

## 4. 关键小说术语（Craft Vocabulary）

| 名词 | 英文 | 含义 |
| --- | --- | --- |
| 高概念 | High Concept | 一句话就能说清的故事钩子，通常带矛盾、反差或危险承诺。 |
| 欲望 | Want | 角色显性追求的目标。 |
| 需要 | Need | 角色真正必须面对的内在课题，常与欲望相冲。 |
| 缺口 | Lack / Inner Gap | 角色内在匮乏、误解、伤口或未完成之处。 |
| 误信念 | Misbelief | 角色错误但坚信的世界观，常驱动错误选择。 |
| 赌注 | Stakes | 失败会失去什么，必须逐步升级。 |
| 时钟 | Clock | 迫使故事加速的时间限制、机会窗口或必然逼近的灾难。 |
| 场景问题 | Scene Question | 本场景最重要的未解问题，驱动读者继续看。 |
| 变化 | Turn / Shift | 场景结束时信息、关系、风险、承诺至少有一项发生改变。 |
| 揭示 | Reveal | 让人物或读者得到新信息的节点。 |
| 反转 | Reversal | 让局势转向与先前预期相反，但回头看仍合理。 |
| 困境 | Dilemma | 两个选项都要付代价，没有轻松答案。 |
| 回收 | Payoff | 前文埋下的信息、情感或 motif 在后文兑现。 |
| 母题 | Motif | 反复出现、彼此呼应的意象、动作、台词或结构单元。 |
| 潜台词 | Subtext | 角色说出口的内容背后真实的欲望、恐惧或力量关系。 |
| 视角纪律 | POV Discipline | 视角人物知道什么、看见什么、误解什么必须稳定。 |
| 声口 | Voice | 某一作品或角色特有的语言节拍、句法偏好与观察方式。 |

## 5. 创作制品（Artifacts）

| 文件 | 角色 | 写入者 |
| --- | --- | --- |
| `plan/manifest.yaml` | 阶段顺序、依赖与 `required_context` | 人（AI 辅助） |
| `plan/common.md` | 长期稳定的创作约束：题材、视角、底线、禁区 | 人 |
| `plan/phases/phase-X.md` | 当前创作阶段的定位合同 | 人 |
| `plan/execution/phase-X.md` | 当前创作阶段的执行合同 | 人 |
| `plan/state.yaml` | 已完成阶段 ledger | 脚本 |
| `plan/handoff.md` | 压缩恢复锚点 | 脚本 |
| `story/canon/**` | 世界规则、设定圣经、时间线 | 人 / AI |
| `story/cast/**` | 人物卡、关系图、欲望与缺口档案 | 人 / AI |
| `story/outline/**` | 幕结构、章结构、场景矩阵 | 人 / AI |
| `story/draft/**` | 章节正文 | 人 / AI |

## 6. 强制层（Enforcement Layer）

| 文件 | 目标 agent |
| --- | --- |
| `.github/copilot-instructions.md` | GitHub Copilot |
| `CLAUDE.md` | Claude Code |
| `AGENTS.md` | Codex / 通用 agent |

三份文件必须字节一致。它们的意义是：让所有 agent 都先看 manifest、按 handoff 恢复、遵守当前 phase 边界，而不是各自凭记忆走不同的创作路径。

## 7. 调度器（Scheduler：`scripts/planctl`）

| 命令 | 作用 |
| --- | --- |
| `planctl advance --strict` | 计算下一内部动作：实施当前阶段、升级占位合同、进入收尾或报告 blocker |
| `planctl resolve <id> --strict` | 解析指定阶段的上下文和依赖 |
| `planctl resume --strict` | 冷启动恢复：项目概览 + handoff 快照 + 下一步 ACTION |
| `planctl complete <id>` | 写回 state/handoff，并记录创作里程碑 |
| `planctl revert <id>` | 回退某个已完成阶段 |
| `planctl doctor` | 体检：检查 manifest/state/handoff/agent instructions 是否一致 |

## 8. Golden Loop（黄金环路）

```text
advance --strict
→ 读取 3 份必带上下文
→ 在 execution 边界内完成一段创作或修订
→ 用完成判定与交付检查自检
→ complete <id> --continue
```

## 9. 相邻概念对比（Adjacent Concepts）

| 概念 | 本质 | 与 Phase-Fiction 的关系 |
| --- | --- | --- |
| 爽文公式 / Beat Sheet | 结构提示清单 | 可作为单个 phase 的输入，但不是完整工作流 |
| Snowflake Method | 从一句话逐步扩展到大纲与人物 | 可嵌入故事承诺与角色阶段，但不负责长程恢复与状态管理 |
| Discovery Writing / 放手写 | 通过自由探索寻找故事 | 可作为某个 phase 的策略，但仍应在 execution 边界内进行 |
| Story Bible | 设定与连续性文档 | 是 Phase-Fiction 中常见产物，不等于完整流程 |
| Prompt Engineering | 优化单次写作提示 | 只能改善局部输出，不能替代长期状态管理 |
| Agent Framework | 编排工具与多 agent 协作 | 正交层；Phase-Fiction 提供的是创作秩序，不是工具链本身 |

## 10. 评估口径（Evaluation Vocabulary）

| 术语 | 含义 |
| --- | --- |
| **5h+ 连续创作** | 单个 agent 在跨压缩与跨会话前提下，能持续推进小说合同链 5 小时以上 |
| **无损续跑** | 换会话后，故事承诺、设定、修订次序与当前阶段都能恢复 |
| **场景有效** | 场景结束时至少有一项发生变化：信息、关系、风险、承诺、情感温度 |
| **可持续张力** | 冲突与代价持续升级，而非靠偶发爆点硬拉 |
| **回看增值** | 后续揭示能让前文细节变得更有意义 |
| **修订闭环** | 本轮修订只解决一种主问题，并有客观完成判定 |

---

<div align="center">

回到 [中文 README](../README.zh-CN.md) · [English README](../README.md)

</div>
