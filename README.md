# Skills 仓库说明

本仓库收录一组可复用的 AI Agent 技能（Skills），覆盖项目初始化、多 Agent 协作框架、设计稿转代码、任务排期、技能发现等场景。每个 Skill 以 `SKILL.md` 为主入口，遵循 frontmatter + 正文的结构。

## 仓库结构

```
skills/
├── init-dev-agents/              # 项目开发流程初始化（生成 AGENTS.md）
├── init-dev-harness-agents/      # 代码开发多 Agent 协作框架初始化
├── init-harness-agents/          # 通用多 Agent 协作框架初始化
├── init-loop-agents/             # 多角色工作流执行引擎（Loop Engineering）
├── mastergo-magic-skill/         # MasterGo 设计稿 ⇄ 代码（C2D / D2C）
├── role-skills/                  # 技能发现与推荐
├── task-plan-skill/              # PRD 拆解为任务计划与排期
└── patterns/                     # 模式范例
    ├── harness-engineering/      # Harness 模式总览
    └── loop-enginerring/         # 求真辨伪辩论工作流示例
```

## Skill 清单

### 一、初始化类（4 个）

| Skill | 命令 | 用途 | 角色 |
|---|---|---|---|
| `init-dev-agents` | `/init` | 为项目生成 `AGENTS.md`，定义开发流程、声明 skill 依赖、建立需求收集机制 | — |
| `init-harness-agents` | `/init` | 通用多 Agent 协作框架（Harness），适用于产品、设计、研究等非代码领域 | coordinator / analyst / creator / evaluator |
| `init-dev-harness-agents` | `/init-dev` | 代码开发多 Agent 协作框架，含架构师先行、triage-route 路由、双节奏自我迭代 | architect / orchestrator / executor / reviewer / devops |
| `init-loop-agents` | `/loop` | 通用多角色工作流执行引擎，解析 `debate-workflow.yaml` 后按拓扑顺序执行 | 角色由项目自定义 |

**三种初始化模式区别**：

- `init-dev-agents`：只生成 `AGENTS.md` + 需求流程，不涉及多 Agent 集群
- `init-harness-agents`：生成 `harness.yaml + roles/ + workflows/` 通用角色集群
- `init-dev-harness-agents`：在通用 Harness 基础上加入代码开发专用角色、triage-route 路由、双节奏自我迭代（小步快跑 + 大周期沉淀）、FAQ + 最佳实践双向沉淀
- `init-loop-agents`：不绑定领域，只做通用工作流引擎，角色与流程由项目自定义

### 二、设计 / 前端类（1 个）

| Skill | 用途 | 触发词 |
|---|---|---|
| `mastergo-magic-skill` | MasterGo 设计稿转代码（D2C）/ 原型生成（C2D） | MasterGo、设计稿转代码、C2D、D2C、fileId、layer_id |

- **C2D**：页面需求 → HTML → 同步到 MasterGo 设计稿（`mcp__C2d`）
- **D2C**：MasterGo 设计稿 → 代码 + 资源落盘（`mcp__getD2c`）
- 默认产出中保真后台页面，桌面端宽度 1440，主色 `#1677FF`
- D2C 默认 Vue 3 + rem 适配，按模块组件化拆分

### 三、任务管理类（1 个）

| Skill | 用途 | 触发词 |
|---|---|---|
| `task-plan-skill` | PRD / 需求文档 → 里程碑 + WBS + 关键路径 + 风险清单 + 验收标准 | 任务计划、研发排期、WBS、里程碑 |

输出结构：计划概览 → 里程碑 → 任务分解（WBS）→ 关键路径 → 风险与应对 → 验收与发布清单。单任务工作量控制在 0.5~3 人天，DoD 必须可验证。

### 四、元技能类（1 个）

| Skill | 用途 | 触发词 |
|---|---|---|
| `role-skills` | 帮助用户发现、查找和安装仓库中的 AI 技能 | "有没有能做 X 的技能"、"有哪些技能可以用" |

按前端 / 后端 / 设计 / 产品 / 通用四类维护技能清单，支持关键词、语义、角色、组合四种匹配策略。

## Patterns 范例

### harness-engineering

`patterns/harness-engineering/harness-agents.md` 是 Harness 模式总览，对比三种 Harness 模式：

| 模式 | 命令 | Skill | 适用场景 |
|---|---|---|---|
| 通用 Harness | `/init` | `init-harness-agents` | 产品、设计、研究 |
| 开发 Harness | `/init-dev` | `init-dev-harness-agents` | 代码开发 |
| 辩论 Harness | `/loop` | `init-loop-agents` | 求真辨伪、方案辩论 |

### loop-enginerring

`patterns/loop-enginerring/` 提供一个完整的求真辨伪辩论工作流示例：

- **工作流**：`agents/debate-workflow.yaml`（Collector → Challenger ⇄ Reasoner 循环 → Judge → Optimizer）
- **规则**：`agents/RULES.md`（9 节，含角色能力同步、循环控制、结果保存、问询记录等）
- **角色**：`agents/roles/`

| 角色 | 职责 |
|---|---|
| Collector | 搜集争议话题，八维度打分筛选 |
| Challenger | 质疑者：找漏洞、红旗信号、异常聚类 |
| Reasoner | 推理者：建立最强解释、证据分级 |
| Judge | 裁判者：比较证据强度、分层结论 |
| Optimizer | 优化者：沉淀方法论、同步角色能力 |

工作流支持 `depends_on`（依赖）、`condition`（条件分支）、`loop`（循环 + 退出条件）、`output`（变量传递），输出保存到 `debates/{工作流名}-{日期}/`。

## 核心设计理念

1. **Harness 是骨架，Skills 是肌肉**：Harness 只定义角色 + 工作流节点序列，节点上的具体执行步骤由对应 skill 提供；角色不重复发明已有步骤
2. **triage-route 路由**：orchestrator 先识别工作类型，再路由到匹配角色；无匹配则创建新角色/工作流
3. **双节奏自我迭代**：小步快跑（每次交互轻量迭代，最小改动）+ 大周期沉淀（每 N 轮深度回顾，提炼模式、升级角色定义）
4. **双向沉淀**：FAQ（负向错误沉淀）+ best-practices（正向经验沉淀）并行维护
5. **已初始化不覆盖，合并增强**：检测到已有配置时按合并策略增强，保留已有能力与历史沉淀，合并前展示 diff
6. **交互记录强制归档**：用户询问与变更记录存放于 `docs/interaction-changes/`，并在 AGENTS.md 中说明位置

## 依赖外部 Skill 链

初始化类 skill 生成的 `AGENTS.md` 中会声明项目开发所需的 skill 链：

```
日常开发：brainstorming → openspec-explore → openspec-propose → writing-plans → subagent-driven-development
调试：systematic-debugging
验证：verification-before-completion
代码审查：requesting-code-review → receiving-code-review
架构优先：codebase-design → writing-plans → subagent-driven-development → requesting-code-review
```

## 使用方式

1. **新项目接入**：在项目根目录执行 `/init`（通用）或 `/init-dev`（开发），生成 `AGENTS.md` 与协作框架
2. **运行工作流**：执行 `/loop` 按 `debate-workflow.yaml` 自动跑多角色流程
3. **设计协作**：触发 `mastergo-magic-skill` 完成 MasterGo 设计稿与代码互转
4. **任务排期**：触发 `task-plan-skill` 将 PRD 拆解为可执行的 WBS
5. **发现技能**：询问"有哪些技能可以用"触发 `role-skills` 获取推荐
