---
name: init-dev-harness-agents
description: >-
  Initializes code-dev multi-agent Harness (architect/orchestrator/executor/reviewer/devops/retro-optimizer),
  workflows (standard-dev/architecture-first/code-review/prd-to-code/debug-flow/devops-ops/dual-phase-dev),
  triage-routing, FAQ, best-practices, progress-tracking, experience-summary, collaboration-sync, and evolution
  (small-step + big-cycle). Features role-specific responsibilities, retro-optimizer knowledge-base flywheel,
  and devops start-script self-review. Invoke on `/init-dev` or when setting up code-development agent collaboration.
---

# Init Dev Harness Agents — 代码开发多 Agent 协作框架初始化

## Overview

为代码开发项目搭建 AI Agent 协作框架（Harness Engineering），定义开发专用 Agent 角色、工作流、通信协议、治理规则，以及**协同进度跟踪、经验总结、工作状态可视化**等工程化管理基础设施。

**核心理念：软件是工程，不是代码**。Harness 不是单节点的文档集合，而是一个有机的协同系统——角色之间通过标准化的交接、同步、跟踪机制形成闭环，确保工作状态透明、经验可追溯、进度可量化。

**核心定位**：
- **本 Skill（Harness）= 定义角色 + 工作流程 + 协同机制**：每个工作流由若干"工作节点"组成，规定角色在什么阶段做什么、何时交接、如何同步状态
- **其他 Skills = 具体工作步骤**：角色在执行某个工作节点时，优先调用已有 skill 完成具体操作（怎么做）
- **关系**：Harness 是"骨架"（角色 + 流程 + 节点 + 协同），Skills 是"肌肉"（每个节点上的具体执行步骤）

**黑盒原则**：对用户（总经理）来说，本 skill 是黑盒——只暴露决策点与输出产物。下面的人（角色）做好自测，上面的人（architect/orchestrator）做好规划和监督。大决策用户明确（少而精），小决策角色自决（多而快）。

## When to Use

- 新代码项目需要多 Agent 开发协作框架
- 已有代码项目接入 Harness Engineering（开发模式）
- `/init-dev` 命令触发
- 需要定义开发 Agent 角色分工与协作流程

## 用户决策点（大决策，少而精）

初始化时一次性收集，默认值优先，不阻塞流程：

| 决策项 | 默认值（用户不答时） |
|---|---|
| 项目目标一句话 | 从 package.json/README 推断 |
| 技术栈确认 | 扫描依赖文件自动识别 |
| 是否启用 dual-phase-dev | 不启用（用 standard-dev；双阶段瀑布+Scrum 是重型工作流） |
| Agent 角色范围 | 启用 6 角色（含 retro-optimizer 飞轮） |
| 大周期节奏 N | N=10（每 10 轮交互触发深度回顾） |
| 通信超时/重试 | 30s / 3 次 |

**小决策由角色自决**，不问用户：节点-skill 映射、FAQ 条目、经验归类、角色契约细化、启停脚本产出、skill 必需/可选判断、武器库同步、角色动态创建等。

**决策升级规则**：小决策遇阻 / 影响范围超出角色边界 / 同类问题反复出现(≥3次) → 上报 orchestrator → orchestrator 判断（角色内可解决退回自决 / 跨角色协调 / 影响项目方向升级为大决策问用户）。

**决策可错性判断**：老板（用户）会犯错，大模型（角色）也会犯错——不能盲从。每个决策必须标注「可证伪条件 + 推翻触发器 + 回溯路径」，retro-optimizer 在每个 Sprint 回顾时执行**决策回顾**：检查本轮决策是否被实践推翻。三层决策都可错、都可判断、都可回溯：

| 决策层级 | 决策者 | 可证伪信号 | 回溯路径 |
|---|---|---|---|
| 大决策 | 用户 | 连续 ≥2 Sprint 因该决策阻塞；项目目标与技术栈不匹配 | retro-optimizer 在大周期沉淀时反馈用户调整 |
| 架构决策 | architect | 后续 Sprint 反复绕过此架构；演进点触发条件频繁命中 | 回溯到 architect 重新决策 |
| 小决策 | 各角色 | 工作类型误判返工；DoD 反复不可验证；同类问题 ≥3 次 | 回溯到原角色重新判断，或升级到 orchestrator |

被推翻的决策不静默修正——必须记录到 `evolution/lessons-learned.md`，标注「原决策 → 推翻信号 → 修正决策」，供后续 Sprint 复用。

## 触发命令

- `/init-dev` — 初始化 Harness（扫描项目 → 大决策清单 → 生成全套配置 → 自洽性检查 → 自动 commit）
- `/harness-evolve` — 手动触发大周期沉淀（深度回顾，升级角色定义与工作流）
- `/harness-evolve dogfooding` — 启动 dogfooding（用项目 harness 反向优化本 skill）

## 输出产物清单

初始化完成后产出以下文件：

| 产物 | 服务目标 |
|---|---|
| `agents/harness.yaml` | 角色与工作流总配置（6 角色 + 6/7 工作流） |
| `agents/roles/*.md` (6 个) | 角色人格与武器库（六段结构 + 输入输出契约） |
| `agents/workflows/*.yaml` (6/7 个) | 多 Agent 协作流程 |
| `agents/faq/*.md` (6 个) | 负向错误沉淀（避坑） |
| `agents/best-practices/*.md` (6 个) | 正向经验沉淀（复用） |
| `agents/sync/` | 协同进度跟踪（progress.yaml + sync-log.md + handoffs/ + experience-summary.md） |
| `agents/evolution/` | 自我迭代（evolution.yaml + lessons-learned.md + patterns.md + role-changes.md + workflow-changes.md） |
| `agents/communication.yaml` | Agent 间通信协议 |
| `AGENTS.md` | Harness 入口文档 |
| `docs/interaction-changes/` | 用户询问与变更归档 |

## 执行流程

收到 `/init-dev` 指令后，按以下步骤执行：

### Step 1: 扫描项目 + 收集大决策

```
扫描内容：
├── package.json / pom.xml / Cargo.toml → 技术栈
├── src/ / app/ / lib/ → 目录结构
├── agents/ → 现有 agent 结构（如有）
├── .git/ → 是否 git 仓库
├── existing harness.yaml → 是否已有 Harness 配置
└── docs/ → 已有文档
```

扫描完成后，一次性列出大决策清单（不逐项询问）：

```
基于扫描结果，以下大决策采用默认值（如需修改请告知）：
1. 项目目标：{从 package.json/README 推断}
2. 技术栈：{扫描识别}
3. 是否启用 dual-phase-dev：不启用（默认用 standard-dev）
4. Agent 角色范围：6 角色（含 retro-optimizer 飞轮）
5. 大周期节奏：N=10
6. 通信超时/重试：30s / 3 次

直接回车采用以上默认值，或指明要修改的项。
```

**原则**：默认值优先；只在大决策上问用户；已有 harness.yaml 时跳过此清单走合并增强。

### Step 2: 生成 Harness 配置

创建 `agents/harness.yaml`，定义 6 角色 + 6/7 工作流。完整模板见 `references/templates.md` 的「harness.yaml 模板」。

**角色清单**（capabilities 与 `patterns/dual-phase-engineering/agents/roles/*.md` 一致）：

| 角色 | capabilities |
|---|---|
| architect | architecture-design, module-split, interface-definition, tech-decision, risk-assessment, evolution-point-identification |
| orchestrator | task-decomposition, work-triage, context-management, agent-routing, backlog-management, phase-gatekeeping |
| executor | code-generation, file-operation, command-execution, tdd-red-green-refactor, yagni-check |
| reviewer | code-review, security-scan, quality-check, dod-verification, tdd-coverage-check |
| devops | start-stop-scripts, monitoring-setup, deployment, infra-automation, evolution-deployment, rollback-verification |
| retro-optimizer | sprint-retrospective, strangler-fig-evaluation, role-knowledge-sync, pattern-extraction, big-cycle-sink |

**工作流清单**（命名与 `agents/workflows/{name}.yaml` 严格一致）：

| 工作流 | 步骤 |
|---|---|
| standard-dev | triage → route → implement → review → fix → deploy → retro |
| architecture-first | architect(design) → orchestrator(decompose) → executor(implement) → reviewer(review) |
| code-review | requesting-code-review → receiving-code-review |
| prd-to-code | clarify → plan → implement → review → verify |
| debug-flow | triage → diagnose → fix → review |
| devops-ops | create-scripts → setup-monitoring |
| dual-phase-dev | 条件启用，引用 `patterns/dual-phase-engineering/agents/dual-phase-workflow.yaml`，ref 找不到时 orchestrator 自动内联 |

### Step 3: 生成 Agent 角色定义

创建 `agents/roles/` 目录，为每个角色生成完整提示词模板。角色文件采用**六段结构**：

```
frontmatter（name + description + emoji + color + capabilities）
├── 核心立场（2-3 条原则 + 职责边界）
├── 分内工作 + 专属技能（分内必做 / 分外交回 / 专属技能清单）
├── 方法论武器库（种子条目 + 待积累占位，retro-optimizer 飞轮同步）
├── 输出格式（markdown 模板）
└── 行为准则（硬约束 + 各司其职 + 协作边界）
```

6 角色生成规范见 `references/roles.md`（权威来源为 `patterns/dual-phase-engineering/agents/roles/*.md`）。以下为角色隐喻与 Scrum 映射：

| 角色 | 隐喻 | 方法论偏向 | Scrum 角色 |
|---|---|---|---|
| architect | 总经理（谨慎经营） | 瀑布 + Martin Fowler 渐进式 | Product Owner 兼任 |
| orchestrator | 大将军参谋部 | Scrum 编排 + 阶段切换守门 | Scrum Master |
| executor | 大将军先锋营 | TDD + 小步快跑 + YAGNI | Development Team |
| reviewer | 大将军监军 | TDD 验证 + DoD 验收 | Development Team |
| devops | 大将军后勤辎重 | 演进式部署 + 监控 | Development Team |
| retro-optimizer | 大将军军师史官 | Sprint 回顾 + strangler fig + 飞轮 | Development Team（回顾牵头） |

**角色输入输出契约**（architect/orchestrator 自决定义，不问用户）：

| 角色 | 输入 | 输出 |
|---|---|---|
| architect | 用户需求（requirement + main_flow + tech_stack） | 架构决策记录 → orchestrator；演进点清单 → retro-optimizer |
| orchestrator | 架构决策 + 任务 | WBS 任务清单 → executor；阶段切换信号 → 全员 |
| executor | WBS 任务 | 代码 + 测试报告 → reviewer；阻塞上报 → orchestrator |
| reviewer | 代码 + 测试报告 | 评审报告 → executor；DoD 达标信号 → orchestrator |
| devops | 部署信号 + 代码 | 部署状态 → orchestrator；启停脚本 → 全员 |
| retro-optimizer | Sprint 完成信号 + 各角色产出 | 武器库同步 → 各角色文件；大周期沉淀 → evolution/ |

### Step 4: 生成工作流定义

创建 `agents/workflows/` 目录，按 Step 2 工作流清单生成对应 yaml 文件。**命名一致性约束**：`workflows[].name` 必须与文件名严格一致（如 `debug-flow` 而非 `debug-workflow`）。

**工作流类型**：sequential / parallel / debate / review / triage-route / condition（基于上一步输出变量守门）/ loop（基于退出条件重复执行）。

**节点-skill 映射原则**：工作流只定义"做什么、谁来做、何时做"；"怎么做"由 skill 提供；`skill: null` 表示角色内禀职责。

### Step 5: 生成角色 FAQ

创建 `agents/faq/` 目录，为每个角色生成 FAQ：architect/orchestrator/executor/reviewer/devops/retro-optimizer 各一份。FAQ 维护原则：每次角色执行中发现的重复错误必须回写。

### Step 6: 生成 Harness AGENTS.md

按 `references/templates.md` 的「AGENTS.md 模板」生成项目 Harness 入口文档，含项目定位、Agent 架构拓扑、协作流程、治理规则、FAQ/best-practices 入口、自我迭代、协同进度、交互记录、Skill 依赖表。

### Step 7: 生成通信配置

创建 `agents/communication.yaml`，模板见 `references/templates.md` 的「communication.yaml 模板」。JSON 格式 + 超时重试 + 消息 schema。

### Step 8: 记录交互变更

记录用户的询问与本次初始化决策，存放于 `docs/interaction-changes/`，在 AGENTS.md 中说明位置。后续每次 Harness 配置调整同步追加。

### Step 9: 生成自我迭代与沉淀机制

创建 `agents/evolution/` 目录。**双节奏机制**：小步快跑（每次交互轻量迭代）+ 大周期沉淀（每 N 轮深度回顾）。

完整 `evolution.yaml` 模板见 `references/templates.md` 的「evolution.yaml 模板」，含：
- `iteration.small_step`（per-interaction，最小改动，all-roles）
- `iteration.big_cycle`（every-n-interactions，pattern-extraction，orchestrator-led）
- `role_practice`（6 角色各自的 collect/analyze/sink/iterate）
- `sinks`（faq/best-practices/lessons/patterns/role-delta/workflow-delta 六类沉淀去向）
- `feedback_loop`（双节奏反馈闭环）
- `merge_policy`（已初始化时合并策略）

**沉淀目录初始文件**：best-practices/（6 个）+ sync/（progress.yaml + sync-log.md + experience-summary.md + handoffs/ + README.md）+ evolution/（lessons-learned.md + patterns.md + role-changes.md + workflow-changes.md + evolution.yaml）。

**自我迭代触发条件**：小步快跑每次交互必走 / 大周期每 N=10 轮或 `/harness-evolve` / FAQ 同类错误 ≥3 次强制升级角色定义 / 新工作类型无匹配工作流时创建并沉淀。

### Step 10: 生成协同进度跟踪与经验总结文件

创建 `agents/sync/` 目录。模板见 `references/templates.md` 的「sync 模板」：
- `progress.yaml` — 全局进度看板（active_workflows + agent_queues + blockers + milestones + stats）
- `handoffs/handoff-template.yaml` — 交接记录模板
- `sync-log.md` — 操作时间线日志
- `experience-summary.md` — 跨角色经验总结（"如果重来"的工程洞察）
- `README.md` — 协同工作区说明

**sync/ vs evolution/ 职责边界**（retro-optimizer 自决归类，不问用户）：
- **sync/ = 当前状态**：进度、操作日志、交接记录——回答"现在做到哪了"
- **evolution/ = 历史沉淀**：经验、模式、变更日志——回答"过去学到什么"
- **sync/experience-summary.md = 桥梁**：大周期时从 evolution/lessons-learned.md 提炼可迁移洞察

| 沉淀类型 | 归属目录 | 判断依据 | 写入节奏 |
|---|---|---|---|
| 具体错误 | `agents/faq/{role}.faq.md` | 单次错误，可规避 | 小步快跑 |
| 角色最佳实践 | `agents/best-practices/{role}.best-practices.md` | 单次成功经验，可复用 | 小步快跑 |
| 跨角色经验 | `agents/evolution/lessons-learned.md` | 跨角色可迁移洞察 | 小步快跑 |
| 可复用模式 | `agents/evolution/patterns.md` | 高频工作类型稳定后（被引用 ≥3 次） | 大周期 |
| 角色定义变更 | `agents/evolution/role-changes.md` | 角色提示词调整 diff | 大周期 |
| 工作流变更 | `agents/evolution/workflow-changes.md` | 节点/skill 映射调整 | 大周期 |

边界模糊时优先归 lessons-learned，大周期再升华。

### Step 11: 自洽性检查 + 提交

**自决检查清单**（角色自决，不问用户）：

```
□ 角色数 = 6（architect/orchestrator/executor/reviewer/devops/retro-optimizer）
□ 工作流数 = 6 或 7（+ dual-phase-dev if enabled）
□ harness.yaml 中 agents[].capabilities 与 roles/*.md frontmatter capabilities 一致
□ harness.yaml 中 workflows[].name 与 agents/workflows/{name}.yaml 文件名一致
□ AGENTS.md 中 Skill 依赖表与各角色专属技能清单一致
□ faq/ 文件数 = 角色数
□ best-practices/ 文件数 = 角色数
□ sync/progress.yaml 中 agent_queues 含 6 角色条目
□ evolution/evolution.yaml 中 role_practice 含 6 角色条目
□ dual-phase-dev.enabled 与 Step 1 大决策一致
```

**提交方式**：自洽性检查全通过 → 自动 git commit（记录到 docs/interaction-changes/）；不通过 → 自动修复后重检，仍不通过才问用户；已有 harness.yaml 走合并增强 → 展示 diff 后自动合并提交。

## 工作路由机制

orchestrator 收到工作后，执行 triage-route 流程：

1. **识别工作类型**：架构设计 / 代码实施 / 质量评审 / 运维操作 / 调试 / 双阶段融合开发
2. **路由到匹配角色**：
   - 架构设计 → architect（`codebase-design` / `design-an-interface` / `brainstorming`）
   - 代码实施 → executor（`subagent-driven-development` / `test-driven-development`）
   - 质量评审 → reviewer（`requesting-code-review` / `review` / `verification-before-completion`）
   - 运维操作 → devops（`setup-pre-commit` / `git-guardrails-claude-code`）
   - 调试 → executor（`systematic-debugging`）
   - 代码审查反馈修复 → executor（`receiving-code-review`）
   - 双阶段融合开发 → dual-phase-dev 工作流
3. **角色执行工作节点**：按工作流定义的节点序列推进，每个节点优先调用对应 skill
4. **无匹配时创建**：先创建新角色定义或新工作流（含节点-skill 映射），再执行
5. **记录到 FAQ**：路由误判或新工作类型回写到 `agents/faq/orchestrator.faq.md`

## 自我迭代与沉淀机制

Harness 必须随执行持续演进。采用**双节奏机制**：

| 节奏 | 触发 | 范围 | 改动幅度 | 产出 |
|---|---|---|---|---|
| **小步快跑** | 每次用户交互 | 本轮交互问题 | 最小改动 | FAQ 条目、最佳实践条目、节点-skill 微调 |
| **大周期沉淀** | 每 N 轮（默认 10）或 `/harness-evolve` | 汇总小步快跑期间所有沉淀 | 重量级改动 | 模式固化、角色定义升级、工作流重构 |

**小步快跑**（每次交互必走，每个角色独立执行）：collect → analyze → sink → iterate。最小改动，不升级角色定义。

**大周期沉淀**（每 N 轮或 `/harness-evolve`，orchestrator 牵头）：collect（汇总沉淀）→ analyze（识别模式与短板）→ sink（提炼到 best-practices/patterns）→ iterate（升级角色定义、新增工作流、重构节点序列）。

**迭代触发**：
- 小步快跑级：节点-skill 映射微调、FAQ 条目追加、best-practices 条目追加
- 大周期级：角色定义迭代（FAQ 同类错误 ≥3 次）、工作流迭代（新工作类型）、模式固化（被引用 ≥3 次无调整）、最佳实践升华

**已初始化时的合并增强**：检测到已有配置不覆盖，按策略合并（harness.yaml diff 合并 / roles 追加能力 / workflows 追加或更新 / faq 追加条目 / best-practices 追加条目 / sync 追加条目 / evolution 追加历史）。**合并前必须向用户展示 diff**。

## 五条方法论映射

| 方法论 | 落地位置 | 承载角色 |
|---|---|---|
| 总经理谨慎经营（瀑布） | dual-phase-dev 工作流 wf_scope→wf_freeze | architect |
| 大将军指挥千军万马（Scrum） | dual-phase-dev 工作流 sprint_plan→sprint_retro + sprint_loop | orchestrator 调度 6 角色 |
| Martin Fowler 渐进式 | 角色武器库（演进点清单/最后责任时刻/strangler fig/YAGNI） | architect / retro-optimizer / executor |
| 小步快跑 | evolution.yaml small_step + best-practices/ 持续追加 | 全员 |
| TDD 保证质量 | executor/reviewer 武器库 + dual-phase-dev 的 sprint_implement task | executor / reviewer |

## 演进路径

### 阶段 1：初始化（一次性，本 skill 覆盖）

`/init-dev` → 扫描项目 → 大决策清单 → 生成全套配置 → 自洽性检查 → 自动 commit。**完成标志**：harness.yaml 存在 + 自洽性检查 10 项全通过 + git commit 成功。

### 阶段 2：首次使用（初始化后立即）

用户提出需求 → orchestrator triage → 路由到 standard-dev → executor 实施 → reviewer 审查 → devops 部署 → retro-optimizer 回顾 → 小步快跑记录 FAQ/best-practices。**完成标志**：首个工作流执行完成 + progress.yaml 更新 + FAQ/best-practices 各追加 ≥1 条。

### 阶段 3：持续演进（每次交互 + 大周期）

小步快跑（每次交互，角色自决）+ 大周期沉淀（每 N=10 轮或 `/harness-evolve`，orchestrator 牵头）。**完成标志**：evolution/ 目录持续追加 + 角色定义持续升级 + 工作流持续扩展。

### 阶段 4：dogfooding（可选，项目成熟后）

≥3 个大周期沉淀完成后，用户主动发起 `/harness-evolve dogfooding`（大决策）→ 用项目 harness 反向优化本 skill + 反哺 patterns/dual-phase-engineering/ + 固化角色武器库种子条目。

## 依赖 Skill 声明

init-dev-harness-agents 本身不依赖其他 skill 执行，但生成的 AGENTS.md 中会声明 Harness 工程所需的 skill 链。orchestrator 根据技术栈自动判断必需/可选，**不问用户**：

| Skill | 必需/可选 | 判断依据 |
|---|---|---|
| `brainstorming` | 必需 | 任何项目都需要需求澄清 |
| `writing-plans` | 必需 | 任何项目都需要实施计划 |
| `subagent-driven-development` | 必需 | 逐任务实施是 executor 核心 |
| `test-driven-development` | 必需 | TDD 是硬约束 |
| `systematic-debugging` | 必需 | 调试是 executor 分内 |
| `verification-before-completion` | 必需 | 完成前验证是硬约束 |
| `requesting-code-review` | 必需 | reviewer 核心 |
| `receiving-code-review` | 必需 | executor 接收审查反馈 |
| `codebase-design` | 可选 | 启用 architecture-first 或 dual-phase-dev 时必需 |
| `design-an-interface` | 可选 | 启用 architecture-first 或 dual-phase-dev 时必需 |
| `review` | 可选 | reviewer 标准化审查（与 requesting-code-review 互补） |
| `setup-pre-commit` | 可选 | devops 启用提交钩子时必需 |
| `git-guardrails-claude-code` | 可选 | devops 启用 git 安全防护时必需 |

**自决规则**：必需 skill 缺失 → AGENTS.md 标注"⚠️ 必需 skill 未安装"，不阻塞初始化；可选 skill 缺失 → 标注"（可选，启用 X 工作流时安装）"；启用 dual-phase-dev → codebase-design + design-an-interface 自动升级为必需。

**Skill 链**：

```
Harness 开发: brainstorming → writing-plans → subagent-driven-development → test-driven-development → verification-before-completion
多 Agent 协作: orchestrator (triage) → architect / executor / reviewer / devops / retro-optimizer
架构优先: architect (codebase-design / design-an-interface) → orchestrator (decompose) → executor → reviewer
代码审查: reviewer (requesting-code-review / review) → executor (receiving-code-review)
调试: executor (systematic-debugging)
验证: reviewer / devops (verification-before-completion)
DevOps: devops (setup-pre-commit / git-guardrails-claude-code)
双阶段融合开发: architect → orchestrator → executor → reviewer → devops → retro-optimizer
```

## 模板变量

| 变量 | 来源 | 示例 |
|---|---|---|
| `{项目名}` | 目录名或 package.json name | stock-analysis |
| `{技术栈}` | 扫描依赖文件 | Java 17 + Spring Boot + Vue3 + Vite |
| `{Agent 数量}` | 根据项目复杂度 | 6（architect + orchestrator + executor + reviewer + devops + retro-optimizer） |
| `{工作流数量}` | 根据项目需求 | 6 或 7（+ dual-phase-dev if enabled） |

## 附加文件

本 SKILL.md 自包含可执行。以下附加文件提供大段模板与角色详解，按需读取：

| 文件 | 内容 | 读取时机 |
|---|---|---|
| `references/templates.md` | harness.yaml / AGENTS.md / communication.yaml / evolution.yaml / sync 模板 | 生成对应文件时 |
| `references/roles.md` | 6 角色生成规范（六段结构 + capabilities 对齐 + 输入输出契约 + devops 自审视清单 + retro-optimizer 飞轮检查清单） | 生成角色文件时 |

## 核心原则

- 每个 Agent 角色职责单一、输入输出契约明确、通信协议标准化
- **各司其职**：每个角色有分内工作（必做）与分外工作（交回其他角色），不越权；每个角色有专属技能清单，优先调用 skill 不重复发明
- **大框架到小要求**：双阶段工作流通过 `main_flow`（主要业务流程，大框架）与 `tech_stack`（主要技术栈，小要求）inputs 实现层次传递
- 架构师把控全局，编排者按工作类型路由到对应角色
- 工作流先识别"这是什么工作"，再转发给匹配角色；无匹配则创建新角色/工作流后执行
- 每个角色配备 FAQ，避免重复犯错
- 每个工作节点必须标注"优先调用的 skill"，避免角色自行实现已有步骤
- **每次用户交互必走自我迭代流程**：collect → analyze → sink → iterate，每个角色都参与实践（非 orchestrator 独占）
- **已初始化不覆盖，合并增强**：检测到已有 Harness 配置时按合并策略增强，保留已有能力与历史沉淀
- **小步快跑，大周期沉淀**：交互级轻量迭代（小步快跑，每轮做最小改进）+ 周期级深度沉淀（大周期，定期回顾提炼最佳实践与模式）
- **每个角色积累最佳实践**：每个角色维护自身最佳实践库，区别于 FAQ（错误沉淀），最佳实践是正向经验沉淀
- **retro-optimizer 飞轮**：每个 Sprint 回顾后执行角色能力同步检查，动态积累各角色武器库（知识库自形成）
- **devops 自审视**：每个 Sprint 末主动评估是否需要启停脚本，不等 orchestrator 指令
- **协同进度透明化**：通过进度跟踪文件、交接记录、同步日志实现多角色工作状态可视化
- **经验可追溯**：通过经验总结文件、跨角色 lessons learned 实现知识持续积累与复用
- **决策可错性**：老板（用户）会犯错，大模型（角色）也会犯错。每个决策标注可证伪条件与推翻触发器，retro-optimizer 在 Sprint 回顾时检查决策是否被实践推翻，被推翻的决策触发回溯修正并记录到 lessons-learned——不盲从任何层级

## 注意事项

- **Harness 与 Skills 分工**：本 skill 只定义角色 + 工作流（工作节点序列），不复制 skill 的具体步骤；角色执行节点时优先调用对应 skill，避免重复发明
- **角色动态创建**：当 orchestrator triage 后发现无匹配角色的新工作类型时，按 `patterns/dual-phase-engineering/agents/roles/_template.md` 创建新角色文件，初始化武器库骨架，并在工作流中新增对应节点
- **知识库自形成（retro-optimizer 飞轮）**：角色文件的"方法论武器库"段落即知识库载体；初始为种子条目，每个 Sprint 回顾后由 retro-optimizer 执行"角色能力同步检查清单"动态积累（借鉴 `patterns/loop-enginerring` 飞轮机制）
- **自我迭代是硬性要求**：Harness 必须随执行演进，不能一次性生成后不再维护
- **双节奏迭代**：小步快跑（每次交互轻量迭代，最小改动）+ 大周期沉淀（每 N 轮深度回顾，重量级改动）；避免小步快跑做重量级改动，也避免大周期遗漏细节
- **每个角色都参与迭代**：collect/analyze/sink 由每个角色独立完成，非 orchestrator 独占；orchestrator 仅在大周期牵头汇总
- **每个角色积累最佳实践**：best-practices 是正向经验沉淀，区别于 FAQ（负向错误）；两者并行维护，不能只有 FAQ 没有 best-practices
- **沉淀先于迭代**：发现问题时先沉淀到 FAQ / best-practices / lessons-learned，达到阈值（≥3 次）再升级到角色定义或工作流变更
- **已初始化不覆盖，合并增强**：检测到已有 Harness 配置时按合并策略增强，保留已有能力与历史沉淀；合并前必须展示 diff
- 角色定义保持单一职责，避免一个角色承担过多能力
- 架构师角色必须先行于大规模实施，把控应用、模块拆分、接口与类职责
- DevOps 角色必须产出启停脚本与必要监控，不能只停留在文档
- 工作流定义优先覆盖核心流程（dev / architecture / review / debug / devops），后续按需扩展
- 通信配置中的超时和重试策略根据实际 Agent 响应时间调整
- FAQ 与 best-practices 必须随执行过程持续更新，不能一次性生成后不再维护
- 交互记录必须存放于 `docs/interaction-changes/`，并在 AGENTS.md 中说明位置
- 沉淀文件按日期 + 序号追加，不覆盖历史；角色与工作流变更同步提交 git

## 与 init-harness-agents 的区别

| 维度 | init-harness-agents（通用） | init-dev-harness-agents（开发） |
|------|--------------------------|-------------------------------|
| 目标 | 通用多 Agent 协作 | 代码开发多 Agent 协作 |
| 角色 | coordinator / analyst / creator / evaluator | architect / orchestrator / executor / reviewer / devops / retro-optimizer |
| 适用 | 产品、设计、研究等非代码领域 | 前后端代码开发 |
| 工作流 | 协作、辩论、调研 | 开发、架构、审查、调试、运维、双阶段融合 |
| 路由 | 任务分解后分配 | triage-route（先识别工作类型再路由）+ 角色动态创建 |
| 触发 | `/init` | `/init-dev` |
