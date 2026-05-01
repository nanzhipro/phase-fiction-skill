# Fiction Profiles

本目录承载 **题材 / 叙事引擎 profile 层**，用于表达“哪些东西会随着小说类型而变化”。

## 三层边界

Phase-Fiction 仓库应严格分成三层：

1. **通用内核**
   - 位置：`scripts/planctl.rb`、`references/templates.md`、`references/phase-templates.md`、`references/methodology.md` 的通用部分
   - 负责：状态机、双层合同、恢复协议、ledger、交付门禁、仓库基础设施
   - 原则：不感知悬疑、言情、奇幻等题材差异

2. **Profile 层**
   - 位置：`profiles/*/profile.yaml`
   - 负责：不同题材 / 叙事引擎的默认 phase 图、必带产物、补充问卷、修订轮次、质量关注点
   - 原则：表达“什么会变”，但不重写底层调度器

   profile 之间共享的 overlays 不应散落在各个 profile 文件里重复定义，而应统一收口到 [profiles/overlays.yaml](./overlays.yaml)。

3. **项目实例层**
   - 位置：生成后的项目仓库，如 `plan/manifest.yaml`、`story/**`
   - 负责：某一部小说自己的 premise、角色、世界、beat map、正文与修订结果
   - 原则：运行时生成，不能回流成所有项目的默认结构

## `profile.yaml` 最小 schema

每个 profile 至少应声明以下信息：

- `id`：稳定 profile 标识
- `label`：对人类可读的名称
- `family`：题材 / 大类，例如 `mystery-thriller`、`romance`
- `engine`：主要叙事驱动力，例如 `clue-driven`、`relationship-driven`
- `selection_hints`：适用场景与不适用场景
- `input_extensions.required_questions`：相对通用输入额外要问的问题
- `defaults.story_artifacts`：这个 profile 默认需要哪些故事制品
- `defaults.phase_catalog`：推荐 phase 家族与顺序
- `revision_passes`：这一类小说常见且必要的修订轮次
- `quality_focus`：这一类小说 finalize / review 时应重点盯什么
- `common_md_must_cover`：应该注入 `plan/common.md` 的长期约束主题
- `recommended_overlays`：可叠加的次级 profile 标签，如 `closed-circle`、`dual-pov`

模板见 [profiles/profile-template.yaml](./profile-template.yaml)。

overlay 定义见 [profiles/overlays.yaml](./overlays.yaml)，overlay 模板见 [profiles/overlay-template.yaml](./overlay-template.yaml)，派生结果示例见 [profiles/examples.md](./examples.md)。

## Overlay 规则

overlay 负责表达跨题材复用的“次级结构要求”，例如封闭空间、倒计时、多视角、慢热、连载批次等。

- base profile 回答：这类小说默认怎么组织 phase 图
- overlay 回答：这次项目额外带了什么结构压力
- 项目实例回答：这一部小说最后实际采用了哪些 phase 和产物

overlay 只能做三类事：

- 强化某个已有 phase 的必带产物或检查点
- 插入少量结构型 phase
- 向 `plan/common.md` 注入长期纪律

overlay 不应直接替代 base profile，更不应绕过 `workflow_profile` 自己偷偷造一套 phase 链。

## Overlay Merge 规范

overlay 不再允许写自由文本式的 `phase_adjustments`。从现在开始，只能通过 `phase_merge.operations` 表达对 phase 图的影响。

固定应用顺序：

1. 先验证 overlay 是否兼容当前 `workflow_profile.profile`
2. 再从 operation 里的 `targets` 或 `anchor_targets` 解析出**当前 profile 对应的确切 phase id**
3. 按 overlay 被选中的顺序、以及 operation 在文件里的顺序依次应用
4. 再合并 `artifact_additions`
5. 再合并 `common_md_must_cover`
6. 最后才允许用户 phase override 覆写局部

当前只允许两种 phase op：

- `require_phase`：要求当前 profile 的某个既有 phase 必须存在。它只能通过 `targets.<profile-id>` 指向确切 phase id，不能靠 Agent 猜“哪个 phase 差不多”。
- `ensure_phase_after`：要求某个结构 phase 在指定 anchor 之后存在。它只能通过 `anchor_targets.<profile-id>` 解析 anchor；若目标 phase 不存在就插入，若已存在则保留单份并移动到 anchor 之后。

硬失败规则：

- overlay 与当前 profile 不兼容：直接失败
- operation 类型不在支持列表里：直接失败
- 当前 profile 没有对应的 `targets` / `anchor_targets`：直接失败
- `require_phase` 指向的 phase 不存在：直接失败
- `ensure_phase_after` 的 anchor 不存在：直接失败

执行纪律：

- 不允许按 phase 名字相似度、人类直觉或题材常识去猜“等价 phase”
- 不能把 `mystery-thriller` 的 phase id 直接套给 `horror` 或 `literary`
- 需要跨题材复用时，必须在 overlay 内显式写出 per-profile target map

## 选择规则

- 先选一个 **base profile**，例如 `mystery-thriller` 或 `romance`
- 再选一个 **engine**，用于明确当前小说主要靠什么驱动
- 最后选若干 **overlays**，表达闭环结构、倒计时、多视角、连载化等次级要求

当前仓库先提供五个 starter profiles：

- [profiles/mystery-thriller/profile.yaml](./mystery-thriller/profile.yaml)
- [profiles/romance/profile.yaml](./romance/profile.yaml)
- [profiles/epic-fantasy/profile.yaml](./epic-fantasy/profile.yaml)
- [profiles/literary/profile.yaml](./literary/profile.yaml)
- [profiles/horror/profile.yaml](./horror/profile.yaml)

如果你想直接看“选完 profile 和 overlays 之后最终 phase 图会长什么样”，先看 [profiles/examples.md](./examples.md)。

后续如果要新增 profile，应遵守一个原则：**新增的是题材层默认值，不是新的调度器。**
