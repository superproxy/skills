# Dual-Phase Engineering — 双阶段融合开发范例

融合五条方法论的完整可执行范例：**前期瀑布（总经理谨慎经营）→ 范围冻结门 → 后期 Scrum（大将军指挥千军万马）+ TDD 红绿重构 + Martin Fowler 渐进式架构**。

## 核心设计：角色动态创建 + 知识库自形成

本范例不预建完整角色内容，而是设计"角色动态创建 + 知识库自形成"机制：

- **角色文件即知识库载体**：每个角色文件的"方法论武器库"段落即其知识库
- **种子条目 + 待积累占位**：初始只含 2-3 条最核心原则，非完整内容
- **retro-optimizer 飞轮**：每个 Sprint 回顾后同步积累各角色武器库（借鉴 `patterns/loop-enginerring/agents/roles/optimizer.md` 的角色能力同步检查清单）
- **动态创建**：orchestrator triage 后无匹配角色时，按 `_template.md` 创建新角色文件并初始化武器库骨架

## 五条方法论融合映射

| 方法论 | 落地方式 | 承载角色 | 工作流节点 |
|---|---|---|---|
| 总经理谨慎经营 | 瀑布阶段：范围冻结/风险评估/DoD 把关 | architect 主导 | wf_scope → wf_milestone → wf_wbs → wf_risk → wf_freeze |
| 大将军指挥千军万马 | 多 Agent 协作，orchestrator 调度 6 角色 | orchestrator 主导 | sprint_plan → sprint_implement → sprint_review → sprint_devops → sprint_retro |
| Martin Fowler 渐进式 | 演进点清单/最后责任时刻/strangler fig/YAGNI | architect / retro-optimizer / executor 武器库 | 贯穿性约束（非独立节点） |
| 小步快跑 | Scrum 迭代循环 + 双节奏自我迭代 | 全员 | sprint_loop（loop）+ big_cycle_sink |
| TDD 保证质量 | 红-绿-重构内嵌于实施环节 | executor / reviewer | sprint_implement 的 task（非独立节点） |

## 三层解耦结构

```
patterns/dual-phase-engineering/
└── agents/
    ├── RULES.md                       # 执行规则（契约 + 动态创建 + 知识库自形成）
    ├── dual-phase-workflow.yaml       # 工作流定义（瀑布5步 + 阶段切换 + Scrum4步循环 + 大周期沉淀）
    └── roles/
        ├── _template.md               # 角色动态创建模板
        ├── architect.md               # 架构师（总经理视角）
        ├── orchestrator.md            # 编排者（大将军参谋部）
        ├── executor.md                # 执行者（大将军先锋营）
        ├── reviewer.md                # 评审者（大将军监军）
        ├── devops.md                  # DevOps（大将军后勤辎重）
        └── retro-optimizer.md         # 回顾优化者（大将军军师史官）
```

## 工作流阶段切换

```
瀑布阶段（architect 主导）
  wf_scope → wf_milestone → wf_wbs → wf_risk → wf_freeze(产出 freeze_gate)
                                                    │
                                          condition: GATE_OPEN
                                                    ▼
Scrum 迭代（orchestrator+executor+reviewer+devops+retro-optimizer）
  sprint_plan → sprint_implement(TDD内嵌) → sprint_review → sprint_devops → sprint_retro
       ▲                                                                              │
       └──────────────────── loop (max 6, exit: backlog_completed) ──────────────────┘
                                                    │
                                          backlog_completed
                                                    ▼
大周期沉淀（retro-optimizer）
  big_cycle_sink（模式提炼 + 角色定义升级 + strangler fig 总决策）
```

## 6 角色隐喻映射

| 角色 | 隐喻 | Scrum 角色 | 软件工程角色 | 方法论偏向 | 阶段 |
|---|---|---|---|---|---|
| architect | 总经理（谨慎经营） | Product Owner（兼） | 架构师 / 系统分析师 | 瀑布 + Martin Fowler 渐进式 | 瀑布主导 + Scrum 参与 plan |
| orchestrator | 大将军参谋部 | Scrum Master | 项目经理 / 敏捷教练 | Scrum 编排 + 阶段切换守门 | 全程 |
| executor | 大将军先锋营 | Development Team | 开发工程师 | TDD + 小步快跑 + YAGNI | Scrum 主导 |
| reviewer | 大将军监军 | Development Team | 测试工程师 / QA | TDD 验证 + DoD 验收 | Scrum 每 sprint |
| devops | 大将军后勤辎重 | Development Team（关键） | DevOps 工程师 / SRE | 演进式部署 + 监控 + 应急响应 | Scrum 每 sprint 末 |
| retro-optimizer | 大将军军师史官 | Scrum Master（回顾） | 过程改进工程师 | Sprint 回顾 + strangler fig + 角色武器库同步 | Scrum 每 sprint 末 + 大周期 |

## DevOps 角色的重要性（核心强化）

DevOps 不是"附属角色"，而是双阶段融合开发的**关键支柱**——贯穿部署、安全、应急、健康巡检全生命周期。融合业界最佳实践（发布控制闸门、分级应急响应、密钥防护、健康巡检），devops 承担以下关键职责：

### DevOps 关键实践（武器库种子）

#### 发布控制闸门（前置 4 问）

每次部署前必须回答 4 个问题，全通过才进发布流程：
1. 主干与生产环境的差异是什么？
2. 是否有真实发布需求（非调试性发布）？
3. 回滚方案是否已准备？
4. 监控告警是否已配置？

任一为否 → 提供替代路径，避免不必要发布。

#### 分级应急响应（P0/P1/P2）

| 级别 | 场景 | 响应流程 |
|---|---|---|
| P0 | 完全不可用 | **强制先回滚再诊断**；回滚后补诊断报告 |
| P1 | 部分功能受损 | 跳过完整需求阶段，直接修复；24 小时内补齐文档与测试 |
| P2 | 体验问题 | 走正常 Sprint 流程 |

#### 密钥安全防护

- 任务中出现敏感信息时强制走环境变量配置
- 提交前执行密钥扫描（三条命令）
- 密钥泄露后立即启动撤销与轮换应急步骤

#### 项目健康巡检（bit rot 检查）

长时间搁置项目后恢复时，必须执行：
- 依赖是否有破坏性更新
- CI 是否还能运行
- 配置是否过期

#### 演进式部署原则

- 灰度优先：先小流量验证，再逐步放量
- feature flag 优先：新功能默认关闭，验证后开启
- 不一次性全量：避免大爆炸式部署的风险

#### 可回滚硬约束

- 每次部署必须验证可回滚
- 回滚方案必须预先准备（不临时想）
- 部署失败自动回滚 + 通知 orchestrator

#### 启动脚本自审视（每 Sprint 末主动评估）

devops 不等 orchestrator 指令，主动评估：
| 检查项 | 是/否 | 行动 |
|---|---|---|
| 本 Sprint 是否新增/修改服务入口？ | 是→ | 产出/更新启停脚本 |
| 本 Sprint 是否新增依赖服务(DB/缓存/MQ)？ | 是→ | 产出依赖服务启停脚本+健康检查 |
| 是否需要本地开发启停脚本？ | 是→ | 产出 `dev-up.sh`/`dev-down.sh` |
| 是否需要生产部署脚本？ | 是→ | 产出 `deploy.sh`+回滚脚本 |
| 现有脚本是否因本 Sprint 变更失效？ | 是→ | 更新脚本并验证 |

**自决策规则**：任一项为"是"，主动产出脚本，不等待指令。

## 与其他范例对比

| 维度 | harness-engineering | loop-enginerring | dual-phase-engineering |
|---|---|---|---|
| 定位 | 模式总览（说明书式） | 求真辨伪完整示例 | 双阶段融合开发完整示例 |
| 方法论 | 多 Agent 协作骨架 | 求真辨伪辩论 | 瀑布+Scrum+渐进式+TDD+小步快跑 |
| 角色 | 引用 init 产物 | 5 角色（collector/challenger/reasoner/judge/optimizer） | 6 角色（含 retro-optimizer 飞轮） |
| 工作流 | 模板片段 | 7 步辩论 + loop 循环 | 12 步双阶段 + loop 循环 + condition 守门 |
| 知识库机制 | 无 | optimizer 飞轮同步角色武器库 | retro-optimizer 飞轮 + 动态创建角色 |
| 执行方式 | 引用 init 生成 | `/loop` 引擎执行 | `/loop` 引擎执行 |

## 执行方式

将本范例的 `agents/` 复制到项目根目录，触发 `/loop`：

```bash
cp -r patterns/dual-phase-engineering/agents /path/to/your-project/
cd /path/to/your-project
# 触发 /loop，引擎读取 agents/dual-phase-workflow.yaml 执行
```

输出保存到 `iterations/{工作流名}-{日期}/`。

## 依赖 Skill 声明

本范例假设以下外部 skill 已安装（仓库内未收录，仅声明依赖）：

| Skill | 用途 | 对应工作流节点 |
|---|---|---|
| `brainstorming` | 需求澄清 | wf_scope 前置 |
| `writing-plans` | 实施计划 | wf_wbs |
| `subagent-driven-development` | 逐任务实施 | sprint_implement |
| `test-driven-development` | TDD 红绿重构 | sprint_implement（内嵌于 task） |
| `systematic-debugging` | 系统化调试 | sprint_review 后修复 |
| `verification-before-completion` | 完成前验证 | sprint_review |

这些 skill 不在本范例范围内，由项目按需安装。

## 后续演进

- **dogfooding**：后续可为本仓库自身初始化 Harness，以 dual-phase-engineering 范例运行（本次不在范围）
- **skill 补建**：6 个外部 skill 可按需补建为完整 SKILL.md
- **工作流扩展**：通过 orchestrator triage-route 动态创建新角色与工作流节点
