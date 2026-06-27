# Templates — init-dev-harness-agents 生成模板

> 本文件为 SKILL.md Step 2/6/7/9/10 引用的大段模板集合。Claude 在执行对应 Step 时按需读取，变量 `{项目名}` / `{技术栈}` / `{main_flow}` / `{tech_stack}` 替换为实际值。

## 一、harness.yaml 模板（Step 2）

```yaml
# {项目名} Harness 总配置
# 由 init-dev-harness-agents 生成，已初始化后走合并增强（不覆盖）
version: "1.0"
project:
  name: "{项目名}"
  goal: "{项目目标一句话}"
  tech_stack: "{技术栈}"
  main_flow: "{主要业务流程类型}"

agents:
  - name: architect
    emoji: "🏛️"
    color: "#1E40AF"
    capabilities: [architecture-design, module-split, interface-definition, tech-decision, risk-assessment, evolution-point-identification]
    role_file: "agents/roles/architect.md"
  - name: orchestrator
    emoji: "🎖️"
    color: "#DC2626"
    capabilities: [task-decomposition, work-triage, context-management, agent-routing, backlog-management, phase-gatekeeping]
    role_file: "agents/roles/orchestrator.md"
  - name: executor
    emoji: "⚔️"
    color: "#EA580C"
    capabilities: [code-generation, file-operation, command-execution, tdd-red-green-refactor, yagni-check]
    role_file: "agents/roles/executor.md"
  - name: reviewer
    emoji: "🔍"
    color: "#7C3AED"
    capabilities: [code-review, security-scan, quality-check, dod-verification, tdd-coverage-check]
    role_file: "agents/roles/reviewer.md"
  - name: devops
    emoji: "🚚"
    color: "#0891B2"
    capabilities: [start-stop-scripts, monitoring-setup, deployment, infra-automation, evolution-deployment, rollback-verification]
    role_file: "agents/roles/devops.md"
  - name: retro-optimizer
    emoji: "📜"
    color: "#8B5CF6"
    capabilities: [sprint-retrospective, strangler-fig-evaluation, role-knowledge-sync, pattern-extraction, big-cycle-sink]
    role_file: "agents/roles/retro-optimizer.md"

workflows:
  - name: standard-dev
    file: "agents/workflows/standard-dev.yaml"
    type: triage-route
    steps: [triage, route, implement, review, fix, deploy, retro]
  - name: architecture-first
    file: "agents/workflows/architecture-first.yaml"
    type: sequential
    steps: [architect-design, orchestrator-decompose, executor-implement, reviewer-review]
  - name: code-review
    file: "agents/workflows/code-review.yaml"
    type: review
    steps: [requesting-code-review, receiving-code-review]
  - name: prd-to-code
    file: "agents/workflows/prd-to-code.yaml"
    type: sequential
    steps: [clarify, plan, implement, review, verify]
  - name: debug-flow
    file: "agents/workflows/debug-flow.yaml"
    type: triage-route
    steps: [triage, diagnose, fix, review]
  - name: devops-ops
    file: "agents/workflows/devops-ops.yaml"
    type: sequential
    steps: [create-scripts, setup-monitoring]
  - name: dual-phase-dev
    file: "agents/workflows/dual-phase-dev.yaml"
    type: condition+loop
    enabled: false                      # 大决策项，默认不启用（重型工作流）
    ref: "patterns/dual-phase-engineering/agents/dual-phase-workflow.yaml"
    fallback: inline                    # ref 找不到时 orchestrator 自动内联标准步骤
    steps: [wf_scope, wf_milestone, wf_wbs, wf_risk, wf_freeze, sprint_plan, sprint_implement, sprint_review, sprint_devops, sprint_retro, sprint_loop, big_cycle_sink]

# 命名一致性约束：workflows[].name 必须与 agents/workflows/{name}.yaml 文件名严格一致
```

## 二、AGENTS.md 模板（Step 6）

```markdown
# {项目名} — Harness Engineering 入口

## 项目定位
- 项目目标：{项目目标一句话}
- 技术栈：{技术栈}
- 主要业务流程：{main_flow}

## Agent 架构拓扑
6 角色，详见 `agents/harness.yaml`：

| 角色 | 隐喻 | 核心职责 |
|---|---|---|
| architect 🏛️ | 总经理 | 架构骨架、范围冻结、风险评估、演进点清单 |
| orchestrator 🎖️ | 参谋部 | triage-route 路由、阶段切换守门、backlog 管理 |
| executor ⚔️ | 先锋营 | TDD 红绿重构、小步快跑、YAGNI 约束 |
| reviewer 🔍 | 监军 | DoD 验收、TDD 覆盖检查、多维度审查 |
| devops 🚚 | 后勤辎重 | 演进式部署、监控、启停脚本自审视 |
| retro-optimizer 📜 | 军师史官 | Sprint 回顾、strangler fig 评估、角色武器库同步（飞轮） |

## 协作流程
- 日常开发：`standard-dev` 工作流（triage → route → implement → review → fix → deploy → retro）
- 架构优先：`architecture-first` 工作流
- 代码审查：`code-review` 工作流
- 调试：`debug-flow` 工作流
- 双阶段融合开发（条件启用）：`dual-phase-dev` 工作流

## 治理规则
- 大决策（用户）：项目目标 / 技术栈 / dual-phase-dev 启停 / Agent 范围 / 大周期 N / 通信超时
- 小决策（角色自决）：节点-skill 映射、FAQ 条目、经验归类、角色契约、启停脚本、武器库同步
- 决策升级：小决策遇阻 → orchestrator → 跨角色协调或升级大决策

## FAQ / 最佳实践入口
- 负向错误沉淀：`agents/faq/{role}.faq.md`
- 正向经验沉淀：`agents/best-practices/{role}.best-practices.md`
- 跨角色经验：`agents/evolution/lessons-learned.md`

## 自我迭代机制
- 小步快跑：每次交互 collect → analyze → sink → iterate（角色自决，最小改动）
- 大周期沉淀：每 N={N} 轮或 `/harness-evolve`（orchestrator 牵头，模式固化、角色定义升级）
- 详见 `agents/evolution/evolution.yaml`

## 协同进度
- 进度看板：`agents/sync/progress.yaml`
- 操作日志：`agents/sync/sync-log.md`
- 交接记录：`agents/sync/handoffs/`
- 经验总结：`agents/sync/experience-summary.md`

## 交互记录
用户询问与变更归档：`docs/interaction-changes/`（每次 Harness 配置调整同步追加）

## Skill 依赖表
| Skill | 必需/可选 | 用途 |
|---|---|---|
| brainstorming | 必需 | 需求澄清 |
| writing-plans | 必需 | 实施计划 |
| subagent-driven-development | 必需 | 逐任务实施 |
| test-driven-development | 必需 | TDD 红绿重构 |
| systematic-debugging | 必需 | 系统化调试 |
| verification-before-completion | 必需 | 完成前验证 |
| requesting-code-review | 必需 | 代码审查 |
| receiving-code-review | 必需 | 审查反馈修复 |
| codebase-design | 可选 | 启用 architecture-first/dual-phase-dev 时必需 |
| design-an-interface | 可选 | 启用 architecture-first/dual-phase-dev 时必需 |
| review | 可选 | 标准化代码审查 |
| setup-pre-commit | 可选 | 提交钩子 |
| git-guardrails-claude-code | 可选 | git 安全防护 |
```

## 三、communication.yaml 模板（Step 7）

```yaml
# Agent 间通信协议
version: "1.0"
format: json
transport: file-based        # 基于 agents/sync/ 文件交换
timeout_seconds: 30          # 大决策项，默认 30s
retry:
  max_attempts: 3            # 大决策项，默认 3 次
  backoff: exponential

message_schema:
  required_fields:
    - from: "{role}"
    - to: "{role}"
    - type: "handoff | query | report | alert"
    - workflow_id: "{workflow-name}-{timestamp}"
    - timestamp: "ISO-8601"
    - payload: {}
  optional_fields:
    - priority: "P0 | P1 | P2"
    - reply_to: "{message-id}"
    - artifacts: []          # 产出物路径列表

message_types:
  handoff:
    description: "角色间任务交接"
    payload_schema:
      summary: "本轮工作摘要"
      decisions: []
      artifacts: []
      next_action: "下一节点建议"
  query:
    description: "向其他角色询问"
    payload_schema:
      question: ""
      context: ""
      deadline: ""
  report:
    description: "向上汇报产出"
    payload_schema:
      outcome: ""
      evidence: []
      blockers: []
  alert:
    description: "阻塞或风险告警"
    payload_schema:
      severity: "P0 | P1 | P2"
      issue: ""
      affected_roles: []

routing:
  - condition: "type == alert && severity == P0"
    action: "broadcast to all roles + notify orchestrator"
  - condition: "type == handoff"
    action: "write to agents/sync/handoffs/{from}-to-{to}-{timestamp}.yaml"
  - condition: "timeout_exceeded"
    action: "retry per retry policy; exhausted → escalate to orchestrator"
```

## 四、evolution.yaml 模板（Step 9）

```yaml
# 自我迭代与沉淀机制
version: "1.0"
project: "{项目名}"

iteration:
  small_step:
    trigger: "every-user-interaction"
    scope: "本轮交互问题"
    change_granularity: "minimal"
    participants: "all-roles"           # 每个角色独立执行 collect → analyze → sink → iterate
    flow: [collect, analyze, sink, iterate]
    outputs: [faq-entry, best-practice-entry, node-skill-tweak]
  big_cycle:
    trigger: "every-n-interactions | /harness-evolve"
    n: 10                                # 大决策项，默认 10
    scope: "汇总小步快跑期间所有沉淀"
    change_granularity: "heavy"
    lead: "orchestrator"
    flow: [collect, analyze, sink, iterate]
    outputs: [pattern-fixation, role-definition-upgrade, workflow-restructure]

role_practice:
  architect:
    collect: "本轮架构决策与演进点触发情况"
    analyze: "决策是否被推翻、演进点是否生效"
    sink: "faq/architect | best-practices/architect | evolution/lessons-learned"
    iterate: "调整演进点清单模板或风险评估维度"
  orchestrator:
    collect: "本轮路由判断与阶段切换"
    analyze: "路由误判、backlog 超载、阶段切换是否合规"
    sink: "faq/orchestrator | best-practices/orchestrator | evolution/lessons-learned"
    iterate: "调整 triage 规则或 backlog 容量"
  executor:
    collect: "本轮 TDD 实施与 YAGNI 检查"
    analyze: "红绿重构是否完整、YAGNI 是否过度/不足"
    sink: "faq/executor | best-practices/executor | evolution/lessons-learned"
    iterate: "调整测试策略或 YAGNI 判断边界"
  reviewer:
    collect: "本轮审查覆盖与阻塞"
    analyze: "审查维度遗漏、DoD 不可验证、阻塞反复"
    sink: "faq/reviewer | best-practices/reviewer | evolution/lessons-learned"
    iterate: "调整审查清单或 DoD 模板"
  devops:
    collect: "本轮部署与启停脚本自审视"
    analyze: "部署是否可回滚、脚本是否失效、监控是否缺失"
    sink: "faq/devops | best-practices/devops | evolution/lessons-learned"
    iterate: "调整部署策略或自审视清单"
  retro-optimizer:
    collect: "本轮角色能力同步检查"
    analyze: "武器库增量、strangler fig 触发、新工作类型"
    sink: "各角色文件武器库待积累段 | evolution/role-changes | evolution/patterns"
    iterate: "升级种子条目或触发角色定义升级"

sinks:
  faq: "agents/faq/{role}.faq.md"                    # 单次错误，可规避，小步快跑
  best-practices: "agents/best-practices/{role}.best-practices.md"  # 单次成功经验，可复用，小步快跑
  lessons: "agents/evolution/lessons-learned.md"     # 跨角色可迁移洞察，小步快跑
  patterns: "agents/evolution/patterns.md"           # 高频工作类型稳定后（≥3 次引用），大周期
  role-delta: "agents/evolution/role-changes.md"     # 角色提示词调整 diff，大周期
  workflow-delta: "agents/evolution/workflow-changes.md"  # 节点/skill 映射调整，大周期

feedback_loop:
  small_to_big: "小步快跑沉淀积累到大周期时汇总分析"
  big_to_small: "大周期升级的角色定义/工作流反哺到小步快跑执行"
  threshold_upgrade:
    role_definition: "FAQ 同类错误 ≥3 次 → 升级到角色行为准则"
    seed_entry: "同类经验 ≥3 条 → 升级为武器库种子条目"
    pattern_fixation: "被引用 ≥3 次无调整 → 固化为可复用模式"

merge_policy:
  harness_yaml: "diff merge（保留已有 agents/workflows，追加新增）"
  roles: "append capabilities（保留已有武器库，追加新种子）"
  workflows: "append or update（按 name 匹配，存在则更新 steps，不存在则追加）"
  faq: "append entries（按日期+序号追加，不覆盖）"
  best_practices: "append entries"
  sync: "append entries"
  evolution: "append history"
  pre_merge: "必须向用户展示 diff，确认后合并"
```

## 五、sync 模板（Step 10）

### 5.1 progress.yaml

```yaml
# 协同进度看板
version: "1.0"
project: "{项目名}"
updated_at: "ISO-8601"

active_workflows:
  - workflow_id: "{name}-{timestamp}"
    type: "standard-dev | architecture-first | ..."
    current_node: "{node-id}"
    started_at: "ISO-8601"
    owner: "{role}"

agent_queues:
  architect: { pending: [], in_progress: null, blocked: false }
  orchestrator: { pending: [], in_progress: null, blocked: false }
  executor: { pending: [], in_progress: null, blocked: false }
  reviewer: { pending: [], in_progress: null, blocked: false }
  devops: { pending: [], in_progress: null, blocked: false }
  retro-optimizer: { pending: [], in_progress: null, blocked: false }

blockers:
  - id: "BLK-{seq}"
    severity: "P0 | P1 | P2"
    raised_by: "{role}"
    description: ""
    raised_at: "ISO-8601"
    status: "open | resolved"
    resolved_at: ""

milestones:
  - name: ""
    target_date: ""
    progress: "0-100"
    status: "pending | in-progress | done | blocked"

stats:
  total_workflows: 0
  completed_workflows: 0
  total_sprints: 0
  faq_entries: 0
  best_practice_entries: 0
```

### 5.2 handoffs/handoff-template.yaml

```yaml
# 角色间交接记录模板（复制使用，重命名为 {from}-to-{to}-{timestamp}.yaml）
handoff_id: "HO-{seq}"
from: "{role}"
to: "{role}"
workflow_id: "{name}-{timestamp}"
node_from: "{node-id}"
node_to: "{node-id}"
timestamp: "ISO-8601"

summary: "本轮工作摘要"
decisions:
  - "决策 1"
  - "决策 2"
artifacts:
  - "path/to/artifact"
next_action: "下一节点建议"
open_questions:
  - "待接收方确认的问题"
acknowledged: false        # 接收方确认后置 true，未确认阻塞后续节点
```

### 5.3 sync-log.md

```markdown
# 协同操作时间线日志

> 按时间倒序追加，每条记录角色操作。格式：`[时间] [角色] [操作类型] [工作流ID] 详情`

| 时间 | 角色 | 操作类型 | 工作流 | 详情 |
|---|---|---|---|---|
| 2026-06-27T10:00 | orchestrator | triage | standard-dev-001 | 路由到 executor |
| 2026-06-27T10:15 | executor | handoff | standard-dev-001 | 交接给 reviewer |
```

### 5.4 experience-summary.md

```markdown
# 跨角色经验总结

> "如果重来"的工程洞察。大周期时从 evolution/lessons-learned.md 提炼可迁移洞察。
> sync/（当前状态）与 evolution/（历史沉淀）的桥梁。

## 分类索引
- 架构决策
- 协作流程
- 技术选型
- 调试技巧
- 性能优化

## 经验条目模板
### EXP-{seq} {标题}
- **背景**：
- **决策**：
- **结果**：
- **反思**：
- **可迁移洞察**：
- **标签**：`#tag`
- **参见**：EXP-{related}
```

### 5.5 README.md（sync/ 说明）

```markdown
# 协同工作区

本目录是多角色协同的"当前状态"中枢，回答"现在做到哪了"。

## 文件职责
| 文件 | 职责 |
|---|---|
| `progress.yaml` | 全局进度看板（工作流状态、角色队列、阻塞项、里程碑） |
| `sync-log.md` | 操作时间线日志 |
| `handoffs/` | 角色间交接记录（未确认阻塞后续） |
| `experience-summary.md` | 跨角色经验总结（桥梁，大周期从 evolution/ 提炼） |

## 与 evolution/ 的边界
- `sync/` = 当前状态（进度、日志、交接）—— 回答"现在做到哪了"
- `evolution/` = 历史沉淀（经验、模式、变更）—— 回答"过去学到什么"
- 边界模糊时优先归 evolution/lessons-learned.md，大周期再升华

## 维护规则
- 每次节点切换追加 sync-log.md
- 每次角色交接生成 handoffs/ 记录
- 每次状态变更更新 progress.yaml
- 大周期时提炼 experience-summary.md
```
