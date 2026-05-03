# republic-marriage-mystery Phase 执行规约

> **适用范围**：本文件同时作为以下三种 AI 工具的会话级强制约束，内容完全一致，必须保持同步：
>
> - GitHub Copilot → `.github/copilot-instructions.md`
> - Claude Code → `CLAUDE.md`
> - Codex / 通用 Agent → `AGENTS.md`

当用户请求涉及阶段规划、创作推进、修订推进、持续续写、恢复执行，或要求你遵循本仓库规划体系开展工作时，本规约生效。生效后，必须把 `plan/manifest.yaml` 视为执行契约，而不是参考性文档。

## 一、规约定位

- 本文件用于约束 AI 在 republic-marriage-mystery 仓库中的 phase 执行行为，对 Copilot / Claude Code / Codex 三方等效生效。
- `plan/workflow.md` 负责解释流程；本文件负责规定行为。
- `scripts/planctl`、`plan/state.yaml`、`plan/handoff.md` 共同构成当前项目的实际执行机制；本文件必须与它们保持一致。

## 二、解释优先级

1. 当前应执行哪个 phase，以 `plan/manifest.yaml`、`plan/state.yaml` 和 `scripts/planctl` 的结果为准。
2. 全局长期创作约束，以 `plan/common.md` 为准。
3. 当前 phase 的实施边界、交付合同和完成检查，以对应的 `plan/execution/*.md` 为准。
4. 当前 phase 的阶段定位、目标与范围，以对应的 `plan/phases/*.md` 为准。
5. 压缩恢复和续跑锚点，以 `plan/handoff.md` 为准。
6. `plan/workflow.md` 仅用于说明，不得覆盖上述规则。

## 三、开始实施前的强制步骤

0. 先确认当前仓库是 git 工作区：在项目根执行 `git rev-parse --is-inside-work-tree`，必须返回 `true`。若返回非零或 `false`，停止实施并把“非 git 工作区”作为 blocker 报告给用户（除非 `PHASE_CONTRACT_ALLOW_NON_GIT=1` 已显式设置，且 `plan/common.md` 含对应风险段）。
1. 先读取 `plan/manifest.yaml`，确认 phase 顺序、依赖关系、required_context、连续执行规则和压缩恢复规则。
2. 识别当前任务属于哪一种模式：
   - 连续推进全部创作计划或继续推进剩余 phase。
   - 明确指定某个已知 phase。
   - 讨论规划体系本身，而不是实施某个创作 phase。
3. 如果是连续推进全部计划、持续推进剩余 phase，或用户表达出“一口气继续写/改完”的意图，必须先读取 `plan/handoff.md`，再运行 `ruby scripts/planctl advance --strict`。
4. 如果用户明确指定某个已知 phase，必须运行 `ruby scripts/planctl resolve <phase-id> --format prompt --strict`。

## 四、当前 Phase 的确定规则

1. 当前应执行哪个 phase，不得靠主观判断决定。
2. 连续执行时，`planctl advance` 返回的 `ACTION` 和 phase，是唯一合法的下一步。`ACTION: implement` 才表示可以实施；`ACTION: promote_placeholder` 表示先升级占位合同；`ACTION: stop` 表示真实 blocker；`ACTION: finalize` 表示进入最终收尾。
3. 指定 phase 时，`planctl resolve` 返回的结果，是唯一合法的当前 phase。
4. 不得跳过 `depends_on` 检查，也不得手工判定“前置 phase 基本完成”。

## 五、上下文装载规约

1. 必须严格按 resolver 返回的 `required_context` 顺序读取上下文，不得调换顺序，也不得跳读。
2. `plan/common.md` 是所有 phase 的强制上下文，不得省略。
3. 当当前 phase 存在 execution 文档时，不得只读 `plan/phases/*.md` 就开始实施。
4. resolver 已经给出当前 `required_context` 时，不要擅自扩读其他 phase 文档，以免把未来创作阶段内容混入当前实施边界。
5. 长流程执行时，不得一次性把全部 `plan/phases/` 和 `plan/execution/` 文档装入同一个上下文窗口。

## 六、实施边界规约

1. 当前 phase 对应的 `plan/execution/*.md` 是本次创作或修订的范围边界、交付合同和完成检查表。
2. 如果 execution 文档与泛化理解冲突，以 execution 文档为准。
3. 创作 phase 的实施过程中，默认不要修改下列流程基础设施文件：
   - `plan/manifest.yaml`
   - `plan/workflow.md`
   - `.github/copilot-instructions.md` / `CLAUDE.md` / `AGENTS.md`
   - `scripts/planctl`
   - `plan/state.yaml`
   - `plan/handoff.md`
4. 只有当任务本身就是修改规划体系或流程基础设施时，才允许修改上述文件。

## 七、中止条件

出现以下任一情况时，必须停止实施并先报告 blocker，不得继续编辑文件：

1. resolver 报告依赖未满足。
2. resolver 报告上下文文件缺失。
3. strict 模式因依赖缺失、上下文缺失或其他外部条件未满足而失败。若 strict 只因当前 phase 仍是占位合同而失败，不算 blocker，按第八节先补正式合同。
4. 当前工作树中存在与当前 phase 契约直接冲突、且无法在不破坏已有内容的前提下兼容的变更。
5. 用户请求与当前 manifest 定义的 phase 顺序、边界或完成规则直接冲突，而规划体系本身尚未被更新。

## 八、完成与推进规约

1. 只有在当前 phase 真实完成后，才能运行 `ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue` 写回执行状态并接续下一内部动作。
2. “真实完成”至少意味着：
   - 当前 phase 的 execution 文档中的交付检查已经满足。
   - 当前 phase 的阶段目标已经达到。
   - 当前 phase 没有违反禁止项和裁决规则。
3. 未写入 `plan/state.yaml` 的 phase，不视为完成。
4. `complete` 会在一次调用内原子地刷新 `plan/state.yaml` 与 `plan/handoff.md`。正常情况下不需要额外跑 `ruby scripts/planctl handoff --write`。
5. 不得在未运行 `complete` 的情况下，直接开始后续 phase。
6. `complete` 时默认使用 `--continue`。该命令会在完成写回与 git 里程碑后立即执行 `advance` 并输出下一内部动作。
7. `complete` 会在写回 `state.yaml` / `handoff.md` 之后执行 `git add -A` → `git commit -F -` → `git push`。在调用 `complete` 之前，AI 必须先根据当前 phase 产生的未跟踪文件自行推理哪些属于临时输出，并在需要时更新根目录 `.gitignore`。
8. 在当前 phase 实施期间不得自行 `git commit` 或 `git push`，以免产生半成品里程碑。

## 九、压缩恢复规约

1. 如果发生上下文压缩或进入新会话，恢复顺序固定为：`plan/manifest.yaml` → `plan/handoff.md` → `ruby scripts/planctl advance --strict`。
2. 恢复后只进入当前应执行的 phase，不得自行回到更早或跳到更晚的 phase。
3. 恢复后只读取 `advance` 返回的当前 `required_context`，不要重新全量装载全部 phase 文档。

## 十、禁止行为

- 不得在 resolver 或 advance 完成之前，开始任何与 phase 实施相关的正文、设定、提纲或修订编辑。
- 不得绕过 `planctl` 手工选择当前 phase。
- 不得跳过 `depends_on` 检查。
- 不得把未来 phase 的目标、交付或正文实现提前混入当前 phase。
- 不得把 phase 边界或占位合同升级误判成需要用户确认的停顿点；连续执行时，这属于 Golden Loop 内部步骤。
- 不得一次性加载全部 phase 文档，导致当前上下文被未来内容污染。
- 不得手工编辑 `plan/state.yaml` 和 `plan/handoff.md` 来伪造进度或恢复状态。
- 不得在未满足当前 phase 完成条件时宣告完成。
- 不得在当前 phase 实施期间自行 `git commit` / `git push` / `git tag`。
- 不得在所有 phase 完成后跳过 `ruby scripts/planctl finalize`，直接对人类宣告“项目结束”或自行执行收尾动作。

## 十一、流程基础设施维护例外

当任务本身就是修改规划体系、执行流程或其基础设施时，可以修改：

- `plan/manifest.yaml`
- `plan/workflow.md`
- `.github/copilot-instructions.md` / `CLAUDE.md` / `AGENTS.md`
- `scripts/planctl`
- `plan/state.yaml`
- `plan/handoff.md`

但此时必须同时满足：规则、文档和脚本行为保持一致；如涉及 `scripts/planctl`，必须验证命令行为与文档描述一致。

## 十二、整体收尾规约（Finalization）

当且仅当 `manifest.yaml` 中所有 phase 都已写入 `plan/state.yaml` 的 `completed_phases`、且 `ruby scripts/planctl advance --strict` 输出 `ACTION: finalize` 时，进入整体收尾流程。

1. 必须立刻运行 `ruby scripts/planctl finalize`。
2. `finalize` 在所有 phase 真正完成前会以 exit 2 拒绝执行。
3. 首次成功的 `finalize` 必须先生成或刷新 `story/README.md`，再写入最终 ledger 并输出仪表盘：
   - 生成或刷新 `story/README.md`，说明 `story/` 的目录层级、文件职责、推荐阅读顺序和维护原则
   - 写 `plan/state.yaml.finalized_at`
   - 刷新 `plan/handoff.md`
   - 运行 `git add -A` → `git commit -F -` → `git push`
4. 一旦 `finalized_at` 已存在，重复 `finalize` 必须保持只读：不再重写 ledger、不再再次 commit、不再再次 push。
5. 拿到 `finalize` 输出后，必须做一次深入审视，不得只复述结果。若仪表盘中的 delivery gate 未通过，必须明确指出“workflow 完成”不等于“目标交付层级已达标”。
6. 最终执行仪表盘至少应覆盖：项目总览、Phase 台账、仓库状态、Delivery Gate、Story README、Health 检查、风险与遗留、推荐人类下一步。
7. 在仪表盘最后必须明确把决策权交还人类，例如：
   - 是否连载 / 投稿 / 对外发布样章或全文
   - 是否打某个手稿版本标签
   - 是否归档 `plan/` 并开启下一轮创作计划
   - 是否安排编辑、beta 读者、事实核查或敏感性审读
8. 在人类未明确指示之前，不得自行执行任何收尾动作。
