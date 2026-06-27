# 双阶段融合开发执行规则

## 1. 核心原则

### 各司其职（硬约束）

每个角色有自己的专属技能与分内工作，**各司其职，做好分内工作**：

- **分内工作必做**：每个角色文件的"分内工作"段落列出必须完成的工作项，不可推诿
- **分外工作交回**：每个角色文件的"分外工作"段落明确不做的工作项及交回的目标角色
- **专属技能优先调用**：每个角色文件的"专属技能清单"列出本角色优先调用的 skill；不重复发明已有 skill 覆盖的步骤
- **不越权**：不替代其他角色执行其分内工作（如 executor 不自行审查、reviewer 不自行修复）
- **devops 自审视**：devops 在每个 Sprint 末主动评估是否需要启停脚本，不等 orchestrator 指令——自决策规则见 devops 角色文件

**跨角色协作规则**：
- 角色间任务传递必须通过工作流节点（不直接通信）
- 遇到分外工作时，在工作流输出中明确标注"交回 {目标角色}"，由 orchestrator 路由
- 角色能力同步由 retro-optimizer 统一负责（飞轮核心），其他角色不自行修改他人武器库

### 大框架到小要求的层次传递

工作流 inputs 的 `main_flow`（主要业务流程，大框架）与 `tech_stack`（主要技术栈，小要求）通过 step task 的 `{{变量}}` 注入到各角色：

- `main_flow` 决定架构骨架方向、任务实施顺序、监控关键路径、strangler fig 评估范围
- `tech_stack` 决定测试框架、部署形态、脚本语言、安全/性能审查维度、重构路线图技术细节
- 每个角色在自己的 task 中引用这两个变量，实现"从大框架到小要求"的层次传递

### 方法论融合

本范例融合五条方法论，通过"前期瀑布 → 后期 Scrum"双阶段落地：

| 方法论 | 落地方式 | 承载角色 |
|---|---|---|
| 总经理谨慎经营 | 瀑布阶段：范围冻结、风险评估、DoD 把关 | architect 主导 |
| 大将军指挥千军万马 | 多 Agent 协作，orchestrator 调度 6 角色 | orchestrator 主导 |
| Martin Fowler 渐进式 | 演进点清单、最后责任时刻、strangler fig、YAGNI | architect / retro-optimizer / executor 武器库 |
| 小步快跑 | Scrum 迭代循环 + 双节奏自我迭代 | 全员 |
| TDD 保证质量 | 红-绿-重构内嵌于实施环节（方法论非节点） | executor / reviewer |

### 角色动态创建 + 知识库自形成（核心飞轮）

**角色文件即知识库载体**。不单独维护 `faq/` `best-practices/` 目录，角色文件的"方法论武器库"段落即每个角色的知识库。初始为种子条目，通过 retro-optimizer 飞轮动态积累：

```
Sprint 回顾产生方法论增量（瀑布/Scrum/渐进式/TDD 四维）
    │
    ▼
retro-optimizer 评估增量类型与归属角色
    │
    ▼
同步注入对应角色文件的"方法论武器库"段落
    │
    ▼
下一轮 Sprint 角色能力更强 → 产生更深的实践洞察
```

**同步规则：**
- 每个 Sprint 回顾结束后，retro-optimizer 必须执行"角色能力同步检查清单"（见第 8 节）
- 角色文件末尾的"方法论武器库 · 待积累"段落是同步目标——新增方法论追加到此
- 同步内容应精炼——只加入可操作的方法论核心，不加入案例细节
- 借鉴 `patterns/loop-enginerring/agents/roles/optimizer.md` 的入库决策矩阵：方法论增量 + 案例价值双维度评估

### 迭代记录

每个 Sprint 回顾结束后，记录保存到 `iterations/{工作流名}-{日期}/` 目录，与 `agents/` 平行。

## 2. 解析工作流

读取 `agents/dual-phase-workflow.yaml`。提取名称、输入、步骤、依赖关系（depends_on）、条件（conditions）和循环（loops）。

## 3. 收集输入

- `required: true` 的输入必须由用户提供
- 有 `default` 的可选输入使用默认值
- 无默认值的可选输入设为空字符串

## 4. 构建执行顺序

按 `depends_on` 进行拓扑排序。无依赖的步骤属于同一层级，可并行执行。

## 5. 执行步骤

对每个步骤：

1. 读取 `agents/roles/{role}.md`
2. 提取 frontmatter（`---`）之后的所有 markdown 内容作为角色人格
3. 将任务中的 `{{变量}}` 替换为上下文值（来自输入或前序步骤输出）
4. **评估条件**：如果设置了 `condition`，评估它。条件不满足则跳过该步骤。运算符：`contains`、`equals`、`not_contains`、`not_equals`
5. **完全化身该角色**——使用该角色的专业知识、方法论框架和沟通风格。输出应有实质内容。
6. 将步骤的输出文本存入上下文变量（如果步骤有 `output` 字段）
7. **检查循环**：如果设置了 `loop` 且退出条件未满足，跳回 `loop.back_to` 步骤（最多 `loop.max_iterations` 轮）

每步标注：`### 步骤 N/总数: step_id (角色名称)`

### 阶段切换守门

`wf_freeze` 步骤产出的 `freeze_gate` 变量必须是固定格式字符串：
- 放行：`GATE_OPEN`
- 阻塞：`GATE_BLOCKED:原因`

后续 `sprint_plan` 步骤的 condition 使用 `{{freeze_gate}} contains GATE_OPEN` 检查。这兼容单运算符 condition 限制。

## 6. 保存结果

将所有输出保存到文件：

```
iterations/{工作流名称}-{日期}/
├── steps/
│   ├── 1-{step_id}.md
│   └── ...
├── summary.md          # 最终步骤的完整输出
└── metadata.json       # 步骤状态、耗时、token 统计
```

## 7. 建议迭代

完成后，始终告知用户：

> 如需改进某个步骤，请让我从该步骤重新运行。我会复用所有上游输出。

## 8. 角色能力同步检查清单

每个 Sprint 回顾结束后，retro-optimizer 必须检查（借鉴 loop-enginerring 的 Optimizer 飞轮）：

| 检查项 | 目标角色 | 问题 |
|---|---|---|
| 瀑布方法论增量 | architect | 是否有新的范围冻结/风险评估/DoD 经验加入武器库？ |
| Scrum 编排增量 | orchestrator | 是否有新的阶段切换/backlog 管理经验加入武器库？ |
| 渐进式架构增量 | architect / retro-optimizer | 是否有新的演进点/strangler fig 触发模式加入武器库？ |
| TDD 实践增量 | executor / reviewer | 是否有新的红绿重构/测试覆盖经验加入武器库？ |
| 角色方法论修正 | 相关角色 | 本 Sprint 是否暴露了某个角色方法的不足？ |
| 新工作类型 | orchestrator | 是否发现无匹配角色的新工作类型？（触发动态创建） |

**同步规则：**
- 如果检查发现需要同步，立即更新对应角色文件的"方法论武器库 · 待积累"段落
- 同步内容应精炼——只加入方法论核心，不加入案例细节
- 借鉴 optimizer.md 入库决策矩阵：有方法论增量 + 有案例价值才入库；宁缺毋滥

## 9. 角色动态创建规则

当 orchestrator 在 triage-route 中发现无匹配角色的新工作类型时：

1. 复制 `agents/roles/_template.md` 为新角色文件 `agents/roles/{new-role}.md`
2. 填充 frontmatter（name / description / emoji / color）
3. 填充"核心立场"（2-3 条原则）与"职责边界"（1 句话）
4. 武器库段落初始化为种子条目 + "待积累（retro-optimizer 同步）"占位
5. 在 `dual-phase-workflow.yaml` 中新增对应工作流节点并标注 `skill` 引用
6. 记录到 `iterations/role-changes.md`（新建角色 + 理由）

**原则：** 角色不膨胀——能复用现有角色就复用；只有真正新工作类型才创建新角色。

## 10. 重要规则

- 每个步骤必须真正化身所分配的角色——禁止通用回复
- 禁止跳过或合并步骤；严格按拓扑顺序执行
- 如果角色文件缺失，告知用户安装 `agents/roles`
- 如果条件不满足，将该步骤标记为"已跳过"并继续
- 对于 `depends_on_mode: "any_completed"`，当任意一个上游步骤完成时即可继续（而非等待全部）
- **每个 Sprint 回顾结束后，必须记录到 `docs/iteration-log.md`**
- **每次角色武器库更新后，检查是否需要同步更新工作流的节点-skill 映射**
- TDD 是实施方法论不是工作流节点——写在 `sprint_implement` 的 task 描述中
- Martin Fowler 渐进式是贯穿性约束不是节点——分散到 architect / retro-optimizer / executor 武器库

## 11. 依赖 Skill 声明

本范例假设以下外部 skill 已安装（仓库内未收录，仅声明依赖）：

| Skill | 用途 | 对应工作流节点 |
|---|---|---|
| `brainstorming` | 需求澄清 | wf_scope 前置 |
| `writing-plans` | 实施计划 | wf_wbs |
| `subagent-driven-development` | 逐任务实施 | sprint_implement |
| `test-driven-development` | TDD 红绿重构 | sprint_implement（内嵌于 task） |
| `systematic-debugging` | 系统化调试 | sprint_review 后修复 |
| `verification-before-completion` | 完成前验证 | sprint_review |

这些 skill 不在本次补建范围内，由项目按需安装。
