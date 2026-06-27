# Roles 生成指引 — 6 角色文件生成规范

> 本文件为 SKILL.md Step 3 引用。Claude 在生成项目 `agents/roles/*.md` 时读取本文件，按规范产出角色文件。
>
> **与 patterns 的关系**：角色定义的权威来源是 `patterns/dual-phase-engineering/agents/roles/{role}.md`。本文件不重复角色内容，只提供**生成规范** + **skill 独有字段**（输入输出契约、Scrum 映射、devops 自审视清单、retro-optimizer 飞轮检查清单）。

## 角色文件来源策略

生成 `agents/roles/{role}.md` 时：

1. **启用 dual-phase-dev**：直接引用 `patterns/dual-phase-engineering/agents/roles/{role}.md`（symlink 或复制），并按下方"附加字段"补充输入输出契约
2. **未启用 dual-phase-dev**（默认）：从 patterns 角色文件复制六段结构内容，裁剪 dual-phase-dev 专属的工作流节点引用，保留通用角色定义
3. **新角色动态创建**：按下方"六段结构规范"从零生成，使用 `patterns/dual-phase-engineering/agents/roles/_template.md` 作为骨架

## 六段结构规范（每个角色文件必须包含）

```
1. frontmatter（name + description + emoji + color + capabilities）
2. 核心立场（2-3 条原则 + 职责边界一句话）
3. 分内工作 + 专属技能
   ├── 分内工作（必须做）
   ├── 分外工作（不做，交回其他角色）
   └── 专属技能清单（优先调用，skill 缺失时标注"假设已安装"）
4. 方法论武器库
   ├── 种子条目（2-3 条最核心可操作清单，初始即有）
   └── 待积累（retro-optimizer 飞轮同步，初始为占位）
5. 输出格式（markdown 模板）
6. 行为准则（2-3 条硬约束 + 各司其职 + 协作边界）
```

## 6 角色清单与权威来源

| 角色 | 隐喻 | Scrum 角色 | 权威来源（patterns） |
|---|---|---|---|
| architect | 总经理（谨慎经营） | Product Owner 兼任 | `patterns/dual-phase-engineering/agents/roles/architect.md` |
| orchestrator | 大将军参谋部 | Scrum Master | `patterns/dual-phase-engineering/agents/roles/orchestrator.md` |
| executor | 大将军先锋营 | Development Team | `patterns/dual-phase-engineering/agents/roles/executor.md` |
| reviewer | 大将军监军 | Development Team | `patterns/dual-phase-engineering/agents/roles/reviewer.md` |
| devops | 大将军后勤辎重 | Development Team | `patterns/dual-phase-engineering/agents/roles/devops.md` |
| retro-optimizer | 大将军军师史官 | Development Team（回顾牵头） | `patterns/dual-phase-engineering/agents/roles/retro-optimizer.md` |

## capabilities 对齐表（生成时必须一致）

| 角色 | capabilities |
|---|---|
| architect | architecture-design, module-split, interface-definition, tech-decision, risk-assessment, evolution-point-identification |
| orchestrator | task-decomposition, work-triage, context-management, agent-routing, backlog-management, phase-gatekeeping |
| executor | code-generation, file-operation, command-execution, tdd-red-green-refactor, yagni-check |
| reviewer | code-review, security-scan, quality-check, dod-verification, tdd-coverage-check |
| devops | start-stop-scripts, monitoring-setup, deployment, infra-automation, evolution-deployment, rollback-verification |
| retro-optimizer | sprint-retrospective, strangler-fig-evaluation, role-knowledge-sync, pattern-extraction, big-cycle-sink |

## 附加字段（skill 独有，补充到角色文件末尾）

以下内容不重复 patterns，是 init-dev-harness-agents 生成的项目 Harness 角色文件必须额外包含的字段：

### 角色输入输出契约（architect/orchestrator 自决定义，不问用户）

| 角色 | 输入 | 输出 |
|---|---|---|
| architect | 用户需求（requirement + main_flow + tech_stack） | 架构决策记录 → orchestrator；演进点清单 → retro-optimizer |
| orchestrator | 架构决策 + 任务 | WBS 任务清单 → executor；阶段切换信号 → 全员 |
| executor | WBS 任务 | 代码 + 测试报告 → reviewer；阻塞上报 → orchestrator |
| reviewer | 代码 + 测试报告 | 评审报告 → executor；DoD 达标信号 → orchestrator |
| devops | 部署信号 + 代码 | 部署状态 → orchestrator；启停脚本 → 全员 |
| retro-optimizer | Sprint 完成信号 + 各角色产出 | 武器库同步 → 各角色文件；大周期沉淀 → evolution/ |

### DevOps 启动脚本自审视清单（每 Sprint 末必填）

DevOps 在每个 Sprint 末主动评估，不等 orchestrator 指令：

| 检查项 | 是/否 | 行动 |
|---|---|---|
| 本 Sprint 是否新增/修改了服务入口？ | 是→ | 产出/更新启停脚本 |
| 本 Sprint 是否新增了依赖服务（DB/缓存/MQ）？ | 是→ | 产出依赖服务启停脚本 + 健康检查 |
| 是否需要本地开发启停脚本（一键拉起全栈）？ | 是→ | 产出 `dev-up.sh` / `dev-down.sh` |
| 是否需要生产部署脚本？ | 是→ | 产出 `deploy.sh` + 回滚脚本 |
| 现有脚本是否因本 Sprint 变更而失效？ | 是→ | 更新脚本并验证 |

**自决策规则**：任一项为"是"，主动产出脚本，不等待 orchestrator 指令。脚本产出后回写到部署报告。

### Retro-Optimizer 角色能力同步检查清单（飞轮核心）

每个 Sprint 回顾结束后必须检查（借鉴 `patterns/loop-enginerring` Optimizer 飞轮）：

| 检查项 | 目标角色 | 问题 |
|---|---|---|
| 瀑布方法论增量 | architect | 是否有新的范围冻结/风险评估/DoD 经验加入武器库？ |
| Scrum 编排增量 | orchestrator | 是否有新的阶段切换/backlog 管理经验加入武器库？ |
| 渐进式架构增量 | architect / retro-optimizer | 是否有新的演进点/strangler fig 触发模式加入武器库？ |
| TDD 实践增量 | executor / reviewer | 是否有新的红绿重构/测试覆盖经验加入武器库？ |
| 角色方法论修正 | 相关角色 | 本 Sprint 是否暴露了某个角色方法的不足？ |
| 新工作类型 | orchestrator | 是否发现无匹配角色的新工作类型？（触发动态创建） |

**同步规则**：
- 如需同步，立即更新对应角色文件的"方法论武器库 · 待积累"段落
- 同步内容精炼——只加入方法论核心，不加入案例细节
- 有方法论增量 + 有案例价值才入库；宁缺毋滥

## 生成注意事项

- 角色文件生成后，capabilities 必须与 `harness.yaml` 中 `agents[].capabilities` 严格一致（自洽性检查项）
- 专属技能清单中的 skill 标注"假设已安装"，不重复发明已有 skill 覆盖的步骤
- `{{main_flow}}` 与 `{{tech_stack}}` 变量由 workflow inputs 传入，角色文件中据此适配决策
- 未启用 dual-phase-dev 时，裁剪角色文件中对 `wf_*` / `sprint_*` 节点的引用，保留通用角色定义
