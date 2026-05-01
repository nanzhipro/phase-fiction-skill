# Fiction Plan Infrastructure Templates

本文件提供 `plan/manifest.yaml`、`plan/common.md`、`plan/state.yaml`、`plan/handoff.md` 四份基础制品的可复制模板。占位符用尖括号标注，生成时逐项替换。

---

## 1. `plan/manifest.yaml`

```yaml
version: 1
kind: <novel>-fiction-plan-manifest
project: <项目名>
entrypoints:
  overview: README.md
  common: plan/common.md
  workflow: plan/workflow.md
  handoff: plan/handoff.md
execution_rule:
  description: >-
    执行任一创作阶段时，必须同时携带完整通用上下文、当前阶段定位合同与当前执行合同。
    execution 文档负责声明这次写作 / 修订的输入、边界、交付物和完成标准。
  resolver: scripts/planctl
  state_file: plan/state.yaml
  handoff_file: plan/handoff.md
  repo_instructions:
    - .github/copilot-instructions.md
    - CLAUDE.md
    - AGENTS.md
  continuous_execution:
    next_command: ruby scripts/planctl advance --strict
    completion_command: >-
      ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue
  continuation:
    mode: autonomous
    stop_only_on:
      - dependency_missing
      - missing_context
      - required_gate_failed
      - git_conflict
      - destructive_operation_required
      - all_phases_completed
    non_stop_actions:
      - phase_completed
      - next_phase_ready
      - placeholder_contract_promotion
      - optional_check_failed
      - no_remote_configured
  enforcement:
    dependency_check: true
    stop_on_missing_context: true
    require_execution_file: true
  enforce_allowed_paths: false
  compression_control:
    enabled: true
    max_completion_history: 3
    resume_read_order:
      - plan/manifest.yaml
      - plan/handoff.md
      - next.phase.required_context
    rules:
      - 永远不要一次性加载所有 phase 文档。
      - 只在当前 phase 读取 plan/common.md、当前 phase plan 和当前 phase execution。
      - 每完成一个 phase 后更新 handoff，再进入下一 phase。
  read_order:
    - plan/common.md
    - phase.plan_file
    - phase.execution_file
  required_context:
    - plan/common.md
phases:
  - id: phase-0-premise-promise
    title: Lock the story promise and project constraints
    plan_file: plan/phases/phase-0-premise-promise.md
    execution_file: plan/execution/phase-0-premise-promise.md
    required_context:
      - plan/common.md
      - plan/phases/phase-0-premise-promise.md
      - plan/execution/phase-0-premise-promise.md
    depends_on: []
    allowed_paths:
      - story/premise.md
      - story/canon/**
      - plan/**
  - id: phase-1-cast-engine
    title: Build the cast and relationship engine
    plan_file: plan/phases/phase-1-cast-engine.md
    execution_file: plan/execution/phase-1-cast-engine.md
    required_context:
      - plan/common.md
      - plan/phases/phase-1-cast-engine.md
      - plan/execution/phase-1-cast-engine.md
    depends_on:
      - phase-0-premise-promise
    allowed_paths:
      - story/cast/**
      - story/canon/relationships.md
  # future phase 建议先生成成对占位文件而不是空文件：
  # - plan/phases/phase-X-<slug>.md
  # - plan/execution/phase-X-<slug>.md
  # 两份文件都在前 40 行内保留 `PHASE_CONTRACT_PLACEHOLDER` 哨兵；
  # 当该 phase 成为 current phase 时，`advance --strict` 会返回 ACTION: promote_placeholder，
  # 逼 agent 先补正式合同，再进入实施；这不是用户确认点。
```

**检查点**：

- `required_context` 恰好三项（common + plan + execution），不要多也不要少。
- `depends_on` 只写真依赖，禁止循环。
- `compression_control.rules` 三条硬规则保持不变。
- 未来阶段若暂不正式规划，必须使用带 `PHASE_CONTRACT_PLACEHOLDER` 的成对占位文件，而不是空文件。

---

## 2. `plan/common.md`

```markdown
# <项目名> 通用创作约束

本文件是 <项目名> 全部 Phase 的长期稳定约束来源。任何单步执行都必须把本文件作为完整上下文的一部分，而不是只看局部任务。

## 结论

<一句话说明作品定位，例如：这是一部面向成年读者的近未来悬疑小说，核心快感是权力博弈与真相反转。>

本规划将以下要求视为硬约束，而不是“后续优化项”：

- <故事承诺 / 类型承诺 1>
- <叙事纪律 2>
- <设定底线 3>

## 作品目标

<本项目想完成哪一类小说，以及要提供什么核心阅读体验>

## 非目标

- <明确不做的题材 / 结构 / 叙事路线 1>
- <明确不做的扩张方向 2>

## 长期创作约束

### 故事承诺与读者体验

- <高概念 / 类型 promise>
- <目标读者与情感体验>
- <不可丢失的核心矛盾>

### 设定与连续性底线

- <世界规则>
- <时间线 / 因果线底线>
- <角色不可破坏的核心设定>

### 叙事纪律

- <视角纪律>
- <时态纪律>
- <语言风格底线>

### 内容边界

- <尺度与禁区>
- <版权 / 参考边界>
- <不允许的偷懒套路>
```

**撰写判据**：一条规则是否该进 `common.md`？问自己——**任意未来 phase 都可能违反它吗？** 是，才写。

---

## 3. `plan/state.yaml`（初始态）

```yaml
---
version: 1
completed_phases: []
completion_log: []
updated_at: null
finalized_at: null
```

**注意**：此文件由 `planctl complete` 与首次成功的 `planctl finalize` 写入，人类禁止手改。

---

## 4. `plan/handoff.md`（初始态）

```markdown
# <项目名> Execution Handoff

本文件用于长流程创作时的压缩恢复。不要一次性重新加载全部 phase 文档；恢复时按本文档与 manifest 继续。

## 当前状态

- State file: `plan/state.yaml`
- Handoff file: `plan/handoff.md`
- Updated at: `<尚未开始>`
- Completed phases: `<none>`

## 最近完成

<尚未开始任何 phase>

## 下一 Phase

- `phase-0-premise-promise` Lock the story promise and project constraints
- plan: `plan/phases/phase-0-premise-promise.md`
- execution: `plan/execution/phase-0-premise-promise.md`

下一步读取顺序：
1. `plan/common.md`
2. `plan/phases/phase-0-premise-promise.md`
3. `plan/execution/phase-0-premise-promise.md`

## 压缩恢复顺序

1. `plan/manifest.yaml`
2. `plan/handoff.md`
3. `advance.phase.required_context`

## 压缩控制规则

- 永远不要一次性加载所有 phase 文档。
- 只在当前 phase 读取 plan/common.md、当前 phase plan 和当前 phase execution。
- 每完成一个 phase 后更新 handoff，再进入下一 phase。

## 连续执行命令

- next: `ruby scripts/planctl advance --strict`
- complete: `ruby scripts/planctl complete <phase-id> --summary "<summary>" --next-focus "<next-focus>" --continue`
- handoff-repair (manual recovery only): `ruby scripts/planctl handoff --write`
```

**注意**：初始手工留一份合格骨架只是为了首次 `advance` 之前可读；正常循环由 `complete` 自动刷新 handoff。
