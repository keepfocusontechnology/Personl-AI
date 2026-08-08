# Memory Architecture

> **Status**: Draft
>
> **Phase**: 2.1 — Memory Architecture
>
> **Date**: 2026-08-08
>
> **Author**: Architecture Analyst (AI)
>
> **Review Required**: Principal Architect
>
> **Related Documents**:
> - `docs/design/00-Architecture-Overview.md` — Phase 1.5 Frozen（§4 Memory / §8 State Recovery / §10 EventStream）
> - `docs/design/Personal-AI_Philosophy.md` — §3 Memory / §7.1 Memory Architecture Impact
> - `docs/decisions/ADR-001-state-recovery-model.md` — Accepted
> - `docs/decisions/ADR-002-evolution-boundary.md` — Accepted
> - `docs/decisions/ADR-003-eventstream-positioning.md` — Accepted
> - `docs/decisions/ADR-004-brain-execution-boundary.md` — Accepted
> - `docs/decisions/ADR-005-agency-autonomy-boundary.md` — Accepted
> - `docs/research/Landscape-Report.md` — §7 Mem0 / §8 Graphiti / §9 Hindsight / §13 横向对比

---

## 1. Purpose

本文档是 Phase 2.1 的输出，回答一个核心问题：

> **Personal-AI 的 Memory 到底是什么？保存什么、不保存什么，如何支撑 5-10 年 Digital Life？**

Phase 1.5 已冻结以下约束，本文档不修改这些约束，只在其框架内细化 Memory 的架构定义：

- Memory = cognitive/experiential memory（ADR-001）
- Memory ≠ Brain Persistence（Brain Persistence 范围大于 Memory）（ADR-001）
- Memory ≠ EventStream（两者分离但互补）（ADR-003）
- Memory 的原始经历记录是 ADD-only（ADR-002 D5）
- Memory 的整理结果属于 Autonomous Evolution（ADR-002 D4）
- Memory 不直接被 Agent 访问（ADR-004 D7）

本文档通过 12 个 Architecture Questions（Q1-Q12）展开分析，比较三种架构选项（Option A/B/C），提出推荐方案，并列出需要 Principal Architect 决策的 Open Questions。

**本文档不涉及**：数据库类型、表结构、JSON Schema、SQL、API Endpoint、Python Class、Embedding Model、Chunk 参数、Token 数、Recall Top-K、Agent Prompt、Agent Workflow、Reflection 算法、Identity 详细结构、Self Model 详细结构、Goal 系统、Agency Trigger 算法、Planning 算法、Runtime。这些属于后续 Phase 或本 Phase 的 Deferred Decisions。

---

## 2. Architecture Definition

### 2.1 Memory 的架构定义

**Memory 是 Personal-AI 的认知/经历记忆系统，记录"我经历过什么"，为 Self Model、Reflection、Goal、Agency、Planning 提供经历基础。**

Memory 不是：
- 不是"所有持久化数据的总称"（ADR-001 已冻结）
- 不是 Brain Persistence（Brain Persistence = Snapshot + EventStream + Memory + 其他 Durable State）
- 不是 EventStream（EventStream = 系统事实记录 + 通信总线，ADR-003）
- 不是 Runtime Context（Runtime Context 是 Execution 的临时状态，ADR-004）
- 不是 External Files（External Files 属于 Environment Layer）
- 不是 User Profile（User Profile 是 Identity 的组成部分）
- 不是 Identity（Identity 定义"我是谁"，Memory 记录"发生了什么"）
- 不是 Self Model（Self Model 是从 Memory 提炼的自我理解）

### 2.2 Memory 在 Brain 中的位置

```
Brain Layer
├── Identity          — 我是谁？（Protected Core + Guided Evolution）
├── Memory            — 我经历过什么？（本文档）
├── Self Model        — 我如何理解自己？（从 Memory 提炼）
├── Reflection        — 我如何从过去学习？（输入 Memory，输出更新）
├── Goal              — 我要帮助用户走向哪里？
├── Agency            — 我应该主动做什么？
└── Planning          — 如何执行？（内部认知能力，读取 Memory）
```

Memory 是 Brain 的**经历基础层**——其他 Brain 组件从 Memory 获取经历数据，但 Memory 不替其他组件做结论。

---

## 3. Scope

### 3.1 Memory 保存什么

| 类别 | 说明 | 来源 |
|------|------|------|
| Experience Record | 用户交互经历、外部事件经历、Agent 执行结果经历 | Philosophy §3 / Architecture Overview §10.5 |
| Episodic Memory | 带时间、地点、上下文的经历片段 | Philosophy §3 |
| Semantic Memory | 从经历中提取的知识 | Philosophy §3 |
| User Memory | 关于用户的记忆（用户偏好、用户习惯、用户历史） | Philosophy §3 |
| Project Memory | 关于项目的记忆（项目上下文、项目进展） | Philosophy §3 |
| Memory Consolidation Output | Dream/Consolidation 的整理结果（合并、强化、抽象后的记忆） | Mem0 Dream / Graphiti Community |

### 3.2 Memory 不保存什么

| 不保存的内容 | 归属 | 理由 |
|-------------|------|------|
| Identity 定义（人格、价值观、行为原则） | Identity | Memory 记录"发生了什么"，Identity 定义"我是谁"（Philosophy §3） |
| Self Model（能力评估、用户关系理解） | Self Model | Self Model 是从 Memory 提炼的结论，不是原始经历（Philosophy §3） |
| Goal（Vision / Long Term Goal / Project Goal / Task） | Goal | Goal 是方向性状态，不是经历记录 |
| Agency Decision Log | EventStream | Agency 决策是系统事件，不是经历（ADR-003） |
| Brain State Snapshot | Brain Persistence (Snapshot) | Snapshot 是 Brain 状态的完整快照，不是经历记录（ADR-001） |
| Execution Runtime State | Execution Layer | Execution 是 durable-state-free（ADR-004） |
| External File Content | Environment Layer | 外部文件属于环境，Memory 只记录"接触过这些文件"的经历 |
| System Configuration | Protected Core / Configuration | 配置不是经历 |
| EventStream 本身 | EventStream | EventStream 是通信总线 + 事实记录，不是 Memory（ADR-003） |

### 3.3 边界澄清：Experience 事件的双写

根据 ADR-003 和 Architecture Overview §10.3：

> 所有 Experience 事件同时进入 EventStream 和 Memory。

这意味着：
- **EventStream** 记录所有系统事件（包括 Experience Event + Brain State Event + Agency Event + Execution Event + Reflection Event）
- **Memory** 只记录 Experience Event（用户经历 + 系统经历）+ Consolidation Output

Memory 是 EventStream 中 Experience 类事件的**认知加工层**——EventStream 是原始事实流，Memory 是经过 Retain 处理后的经历记忆。

---

## 4. Taxonomy — Memory 分类体系

### 4.1 Philosophy 定义的五类型

Philosophy §3 定义了 Memory 的五种类型：

| 类型 | 说明 | 示例 |
|------|------|------|
| Event Memory | 原始事件流 | "用户在 10:32 发送了一条消息" |
| Episodic Memory | 情景记忆（带时间、地点、上下文） | "上周三下午，用户在讨论项目A时提到了对性能的担忧" |
| Semantic Memory | 语义记忆（从经历中提取的知识） | "用户偏好简洁的代码风格" |
| User Memory | 关于用户的记忆 | "用户的常用编程语言是 Python" |
| Project Memory | 关于项目的记忆 | "项目A使用 React + TypeScript 技术栈" |

### 4.2 类型之间的关系

```
Experience Event（原始事件）
    │
    ├──→ Event Memory（原始事件流记录，不可变）
    │
    └──→ Retain 处理
            │
            ├──→ Episodic Memory（带上下文的经历片段）
            │       │
            │       └──→ Reflection/Consolidation
            │               │
            │               ├──→ Semantic Memory（提取的知识）
            │               ├──→ User Memory（关于用户的认知）
            │               └──→ Project Memory（关于项目的认知）
            │
            └──→ Consolidation Output（整理后的记忆，可更新）
```

### 4.3 类型与 ADD-only 的关系

| 类型 | ADD-only？ | 说明 |
|------|-----------|------|
| Event Memory | 是 | 原始事件不可变 |
| Episodic Memory | 是 | 原始经历片段不可变 |
| Semantic Memory | 否（可更新） | 知识可被新经历修正，但旧知识不删除（标记失效） |
| User Memory | 否（可更新） | 用户认知可被新经历修正 |
| Project Memory | 否（可更新） | 项目认知可被新经历修正 |
| Consolidation Output | 否（可更新） | 整理结果可被新的整理覆盖，但原始记录不删除 |

**关键原则**：ADD-only 适用于**原始记录**（Event Memory + Episodic Memory）。从原始记录中提取的知识（Semantic / User / Project Memory）和整理结果（Consolidation Output）可以被修正，但修正不删除旧版本——旧版本标记为"在某个时间点之前有效"（非有损演化，参考 Graphiti）。

---

## 5. Experience vs Memory vs Knowledge

### 5.1 三者区分

Personal-AI 中存在三个容易混淆的概念：

| 概念 | 定义 | 不可变性 | 时间模型 | 归属 |
|------|------|---------|---------|------|
| Experience | 正在发生或刚刚发生的事件 | 不可变（发生后即固定） | 单时间（发生时间） | EventStream + Memory 双写 |
| Memory | 经过 Retain 处理的经历记录 | 原始记录 ADD-only，整理结果可更新 | 双时间（valid_time + transaction_time） | Memory |
| Knowledge | 从 Memory 中提取的结构化知识 | 可被修正（非有损演化） | 双时间 + 有效期 | Memory（Semantic Memory 子类型） |

### 5.2 数据流

```
Experience（发生）
    │
    ├──→ EventStream（事实记录，不可变，单时间）
    │
    └──→ Memory.Retain（经历记录）
            │
            ├──→ Event Memory（原始事件，ADD-only）
            ├──→ Episodic Memory（经历片段，ADD-only）
            │
            └──→ Reflection / Consolidation
                    │
                    ├──→ Semantic Memory / User Memory / Project Memory（知识，可更新）
                    └──→ Consolidation Output（整理结果，可更新）
```

### 5.3 Knowledge 的修正机制

当新经历与已有 Knowledge 冲突时：

1. **不删除旧 Knowledge**——标记为"在 valid_time X 之前有效"
2. **添加新 Knowledge**——标记为"从 valid_time Y 开始有效"
3. **记录修正原因**——关联触发修正的 Experience
4. **通知相关组件**——Self Model / Goal / Agency 可能需要重新评估

这是 Graphiti 的**非有损图演化**理念在 Personal-AI 中的应用。

---

## 6. Lifecycle — Retain / Recall / Reflect

### 6.1 三阶段模型

参考 Hindsight 的 Retain/Recall/Reflect 三阶段模型（Landscape Report P0-6），Memory 的生命周期分为三个阶段：

| 阶段 | 职责 | 触发 | 输出 |
|------|------|------|------|
| Retain | 经历的获取和存储 | Experience Event 发生 | Event Memory + Episodic Memory |
| Recall | 经历的检索和召回 | Brain 组件请求（Planning / Agency / 用户响应） | 相关记忆集合 |
| Reflect | 记忆的整理和知识提取 | 定时 / Memory 膨胀 / 冲突检测 | Semantic Memory + Consolidation Output |

### 6.2 Retain 阶段

**职责**：决定什么值得记住，如何存储。

**输入**：Experience Event（用户消息、外部事件、Agent 执行结果）

**处理**：
1. 判断是否值得 Retain（不是所有事件都需要进入 Memory）
2. 提取上下文（时间、地点、参与者、关联事件）
3. 存储 Event Memory（原始事件）+ Episodic Memory（经历片段）
4. 双时间标记（valid_time + transaction_time）

**Retain 策略**（Deferred——具体策略在后续 Phase 定义）：
- 什么值得 Retain？（用户交互一定 Retain，系统事件选择性 Retain）
- Retain 的粒度？（完整事件 vs 摘要 vs 两者）
- Retain 的时机？（实时 vs 批量）

### 6.3 Recall 阶段

**职责**：根据请求检索相关记忆。

**输入**：Recall 请求（来自 Planning / Agency / 用户响应流 / Reflection）

**处理**：
1. 解析 Recall 请求的意图和关键词
2. 多信号检索（参考 Mem0：向量 + 图 + 关键词，P1-2）
3. 时间过滤（双时间模型支持时间维度查询）
4. 相关性排序
5. 返回记忆集合

**Recall 策略**（Deferred——具体策略在后续 Phase 定义）：
- 检索信号权重
- Top-K 数量
- 时间衰减因子
- 上下文窗口大小

### 6.4 Reflect 阶段

**职责**：整理记忆，提取知识，解决冲突。

**输入**：Memory 中的原始经历记录

**处理**：
1. Dream / Consolidation（参考 Mem0 Dream，P0-2）——整理、合并、强化记忆
2. 知识提取——从经历中提取 Semantic Memory / User Memory / Project Memory
3. 冲突检测——检测新旧 Knowledge 的冲突，执行非有损演化
4. 记忆衰减——降低低价值记忆的检索权重（不删除原始记录）

**Reflect 与 Reflection 的关系**：

Memory 的 Reflect 阶段是 **Memory Reflection**（Philosophy §3），属于 Reflection 核心能力的一部分。Reflection 核心能力还包括 **Behavior Reflection**（Phase 2.4）。

| | Memory Reflection（Reflect） | Behavior Reflection |
|---|---|---|
| 输入 | 原始经历记录 | 自身行为记录 + 结果反馈 |
| 输出 | 整理后的记忆 + 提取的知识 | 行为评估 + 策略调整建议 |
| 归属 | Memory 生命周期的一部分 | Reflection 核心能力（Phase 2.4） |
| 触发 | 定时 / Memory 膨胀 / 冲突检测 | 行为完成后 / Agency 触发 |

**关键约束**：Memory 的 Reflect 只整理记忆和提取知识，不评估行为、不调整策略。行为评估和策略调整是 Behavior Reflection 的职责（Phase 2.4）。

---

## 7. Time & Provenance — 双时间模型与溯源

### 7.1 双时间模型

参考 Graphiti 的双时间模型（Landscape Report P0-3），Memory 采用双时间标记：

| 时间维度 | 定义 | 示例 |
|---------|------|------|
| valid_time | 事实实际发生的时间 | "2024年3月用户住在上海" |
| transaction_time | 系统记录该事实的时间 | "2024年4月系统才知道用户住在上海" |

**为什么需要双时间**：
- 事实可能被延迟记录（用户今天提到上周的事情）
- 事实本身有时间范围（用户从1月到3月住在上海）
- 单一时间戳无法区分"什么时候发生的"和"什么时候知道的"

### 7.2 双时间模型的应用范围

| Memory 类型 | 双时间？ | 说明 |
|------------|---------|------|
| Event Memory | 是 | valid_time = 事件发生时间，transaction_time = 记录时间 |
| Episodic Memory | 是 | valid_time = 经历发生时间，transaction_time = 记录时间 |
| Semantic Memory | 是 | valid_time = 知识有效起始时间，transaction_time = 记录时间 |
| User Memory | 是 | valid_time = 用户特征有效起始时间，transaction_time = 记录时间 |
| Project Memory | 是 | valid_time = 项目特征有效起始时间，transaction_time = 记录时间 |
| Consolidation Output | 是 | valid_time = 整理结果有效起始时间，transaction_time = 整理时间 |

**架构级原则**：双时间模型是 Memory 的架构级原则，所有 Memory 类型都采用双时间标记。

### 7.3 Provenance — 溯源

每条 Memory 记录必须可溯源：

| 溯源字段 | 说明 |
|---------|------|
| source_experience | 来源 Experience Event 的引用 |
| retain_method | Retain 方式（实时 / 批量 / 用户编辑） |
| consolidation_history | 整理历史（如果经过 Reflect 处理） |
| conflict_resolution | 冲突解决记录（如果涉及非有损演化） |

**架构级原则**：每条 Memory 记录必须可追溯到来源 Experience Event。

---

## 8. Mutation / Correction / Forgetting

### 8.1 ADD-only 的精确定义

ADD-only 需要区分四种情况：

| 操作 | 允许？ | 说明 | 归属 |
|------|--------|------|------|
| Historical Immutability | 强制 | 原始记录（Event Memory + Episodic Memory）创建后不可修改 | Memory 架构级原则 |
| Logical Invalidation | 允许 | Knowledge 可被标记为"在某个时间点之前有效"，但不删除 | 非有损演化 |
| User Deletion | 允许 | 用户可以显式删除特定记忆（隐私权） | 用户控制（§12） |
| Physical Deletion | 允许（受限） | 过期记忆的物理清理（保留溯源元数据） | Forgetting 机制 |

**关键区分**：
- Historical Immutability 是架构级约束——AI 不可修改原始记录
- Logical Invalidation 是 Knowledge 的正常演化——旧知识不删除，标记失效
- User Deletion 是用户的隐私权——用户可以删除任何记忆
- Physical Deletion 是系统维护——在保留溯源的前提下清理过期数据

### 8.2 Correction — 知识修正

当新经历表明已有 Knowledge 错误时：

```
新 Experience 进入
    ↓
Reflect 检测到与现有 Knowledge 冲突
    ↓
旧 Knowledge 标记为 invalid_at = 新 valid_time
    ↓
新 Knowledge 创建，valid_time = 新 valid_time
    ↓
记录冲突解决原因
    ↓
通知相关组件（Self Model / Goal / Agency）
```

**不删除旧 Knowledge**——它仍然是"在某个时间点之前有效"的历史记录。

### 8.3 Forgetting — 遗忘机制

Memory 需要遗忘机制来防止无限膨胀（参考 Mem0 Dream，P0-2）：

| 遗忘操作 | 说明 | 是否删除原始记录 |
|---------|------|----------------|
| Weight Decay | 降低低价值记忆的检索权重 | 否 |
| Consolidation Merge | 多条相似记忆合并为一条整理结果 | 否（原始记录保留） |
| Archival | 低频访问的记忆移入归档存储 | 否 |
| Physical Purge | 过期且无溯源价值的记忆物理删除 | 是（保留溯源元数据） |

**架构级原则**：Forgetting 不破坏 Historical Immutability——Weight Decay 和 Consolidation Merge 不删除原始记录。Physical Purge 只在严格条件下执行（如用户授权 + 过期 + 无溯源价值），且保留溯源元数据。

---

## 9. Recall — 检索架构

### 9.1 多信号检索

参考 Mem0 的多信号检索（Landscape Report P1-2），Memory 的 Recall 采用多信号融合：

| 检索信号 | 说明 | 参考来源 |
|---------|------|---------|
| Semantic Search | 向量相似度检索 | Mem0 |
| Graph Traversal | 记忆关系图遍历 | Graphiti |
| Keyword Match | 关键词精确匹配 | Mem0 |
| Time Filter | 双时间维度过滤 | Graphiti |
| Context Filter | 上下文关联过滤 | 自行设计 |

**多信号融合策略**（Deferred——具体权重和融合算法在后续 Phase 定义）。

### 9.2 Recall 的请求方

| 请求方 | Recall 目的 | 延迟要求 |
|--------|-----------|---------|
| Planning | 获取历史经验，辅助 Plan 生成 | 中 |
| Agency | 检测 Memory 变化、冲突 | 低 |
| 用户响应流 | 获取相关记忆组装上下文 | 低 |
| Reflection | 获取原始经历进行整理 | 中 |
| Self Model | 获取行为数据更新自我认知 | 中 |

### 9.3 Recall 的约束

- Agent **不直接**访问 Memory（ADR-004 D7）——Agent 需要的记忆通过 Brain → EventStream → Execution 路径传输
- Recall 返回的是记忆集合，不是结论——Memory 不替 Self Model 做结论
- Recall 结果受用户授权边界约束（ADR-005）——不返回用户未授权的数据

---

## 10. Consolidation Boundary — 整理边界

### 10.1 问题：Consolidation 归属哪里？

Consolidation（Dream / 记忆整理）的归属存在歧义：

- 它是 Memory 的生命周期阶段（Reflect）？
- 它是 Reflection 核心能力的输出？
- 它是独立的第三类组件？

### 10.2 分析

| 选项 | 说明 | 问题 |
|------|------|------|
| Consolidation = Memory 内部能力 | Memory 自己整理自己 | Memory 变成自包含系统，与 Reflection 职责重叠 |
| Consolidation = Reflection 输出 | Reflection 核心能力包含 Memory Reflection | 职责清晰，但 Memory 生命周期不完整 |
| Consolidation = 独立组件 | 第三类 Brain 组件 | 增加架构复杂度，违反七能力模型 |

### 10.3 推荐

**Consolidation 是 Reflection 核心能力的输出，作用于 Memory。**

理由：
1. Philosophy §3 明确定义 Reflection 包含 Memory Reflection（整理经历、提取知识）
2. Memory 的 Reflect 阶段是 Memory Reflection 的执行——Memory 提供"被整理的对象"，Reflection 提供"整理的能力"
3. Consolidation 的结果写回 Memory（Semantic Memory / Consolidation Output），不独立存在
4. 这与 ADR-002 D4 一致——Memory 整理结果属于 Autonomous Evolution，Reflection 自动执行

**边界**：
- Memory 负责：存储原始记录、存储整理结果、提供 Recall 接口
- Reflection 负责：执行整理逻辑、提取知识、检测冲突、输出整理结果
- Memory 不执行整理逻辑，Reflection 不持有整理结果

---

## 11. Conflict Model — 记忆冲突

### 11.1 冲突类型

| 冲突类型 | 说明 | 示例 |
|---------|------|------|
| Knowledge Conflict | 新 Knowledge 与旧 Knowledge 矛盾 | "用户偏好Python" vs "用户偏好TypeScript" |
| Episodic Conflict | 新经历与旧经历矛盾 | "用户说喜欢A" vs "用户后来说讨厌A" |
| Temporal Conflict | 时间维度矛盾 | "用户1月在上海" vs "用户1月在北京" |

### 11.2 冲突处理原则

1. **不删除**——冲突的旧记忆保留，标记冲突状态
2. **时间优先**——较新的 Knowledge 在 valid_time 上优先
3. **记录冲突**——冲突原因、触发事件、解决方式都记录
4. **通知**——冲突可能触发 Agency（ADR-005 Level 1 自主观察）或 Reflection

### 11.3 冲突与 Agency 的关系

Memory 冲突检测是 Agency 的 Trigger 源之一（Architecture Overview §4.1 Agency）：
- Memory 冲突 → Agency Level 1 自主观察 → 可能触发 Reflection
- Memory 冲突不直接产生行动——冲突解决是 Reflection 的职责

---

## 12. User Control — 用户对 Memory 的控制

### 12.1 用户权利

| 权利 | 说明 | 约束 |
|------|------|------|
| 查看 | 用户可以查看自己的所有记忆 | 受授权边界约束 |
| 删除 | 用户可以删除特定记忆 | 删除后保留溯源元数据 |
| 修正 | 用户可以修正错误记忆 | 修正不删除旧版本（非有损演化） |
| 导出 | 用户可以导出自己的记忆 | 隐私主权（Philosophy §6） |
| 遗忘 | 用户可以要求 AI 遗忘特定经历 | Physical Deletion（保留溯源元数据） |

### 12.2 用户控制与 ADD-only 的关系

用户控制**优先于** ADD-only：
- ADD-only 约束 AI 不可自主修改原始记录
- 用户可以显式删除或修正任何记忆——这是用户的隐私权
- 用户删除后，系统保留溯源元数据（记录"用户在 X 时间删除了 Y 记忆"），但不保留记忆内容

### 12.3 Memory 与 Protected Core 的关系

Memory **不能修改** Protected Core（ADR-002 Layer A）：
- Memory 不存储核心价值约束、用户授权边界、安全规则——这些属于 Protected Core
- Memory 的经历记录可以**触发** Protected Core 相关建议（如 Reflection 检测到需要调整安全规则），但只能向用户提议，不能自动修改

---

## 13. Options Considered

### Option A: Minimal Memory（最小化记忆）

**方案**：Memory 只保存原始 Experience Event，不提取 Knowledge，不整理。

**结构**：
```
Memory
└── Event Log（原始事件流，ADD-only）
```

**优点**：
- 架构最简单
- 无冲突问题
- 无 Consolidation 复杂度

**缺点**：
- 无法支撑 5-10 年 Digital Life——记忆无限膨胀
- 无 Knowledge 提取——Self Model / Goal / Agency 无法获取结构化认知
- 无时间推理——无法回答"上次什么时候讨论过这个话题"
- 违反 Philosophy §3——Philosophy 明确要求 Episodic / Semantic / User / Project Memory
- 违反 Philosophy §7.1——Memory 必须支持闭环生命周期、双时间模型、ADD-only + Dream

**为什么不推荐**：不满足 Personal-AI 的基本需求，退化为简单日志系统。

### Option B: Layered Memory with Full Automation（全自动化分层记忆）

**方案**：Memory 保存所有类型（Event / Episodic / Semantic / User / Project），Consolidation 完全自动化，无用户干预。

**结构**：
```
Memory
├── Event Memory（原始事件，ADD-only）
├── Episodic Memory（经历片段，ADD-only）
├── Semantic Memory（知识，自动提取，自动修正）
├── User Memory（用户认知，自动提取，自动修正）
├── Project Memory（项目认知，自动提取，自动修正）
└── Consolidation Output（自动整理，自动覆盖）
```

**优点**：
- 完整的 Memory 分类
- 全自动化，用户无负担
- 支持长期 Digital Life

**缺点**：
- 用户无控制——违反 Human-centric 原则
- Knowledge 修正完全自动——用户无法纠正错误认知
- 无用户删除机制——违反隐私主权
- Consolidation 完全自动——可能产生错误的知识提取，且无法纠正
- 违反 ADR-002——Memory 整理结果属于 Autonomous Evolution，但用户应有知情权和干预权

**为什么不推荐**：虽然技术上完整，但违反 Human-centric 原则和用户隐私主权。

### Option C: Layered Memory with User Control（分层记忆 + 用户控制）

**方案**：Memory 保存所有类型，Consolidation 自动化但用户可干预，用户对 Memory 有完整控制权。

**结构**：
```
Memory
├── Raw Layer（原始层，ADD-only）
│   ├── Event Memory（原始事件）
│   └── Episodic Memory（经历片段）
├── Derived Layer（派生层，可更新，非有损演化）
│   ├── Semantic Memory（知识）
│   ├── User Memory（用户认知）
│   ├── Project Memory（项目认知）
│   └── Consolidation Output（整理结果）
└── User Control Layer（用户控制层）
    ├── Deletion Log（删除记录）
    ├── Correction Log（修正记录）
    └── Provenance Metadata（溯源元数据）
```

**优点**：
- 完整的 Memory 分类
- 原始记录 ADD-only，保证可追溯
- 派生层非有损演化，知识可修正但不丢失历史
- 用户完整控制——查看 / 删除 / 修正 / 导出 / 遗忘
- Consolidation 自动化但用户可干预
- 符合 Philosophy §3 / §7.1 / §6（隐私主权）
- 符合 ADR-002（Autonomous Evolution + 用户知情权）
- 符合 ADR-005（用户授权边界）

**缺点**：
- 架构复杂度较高
- 用户控制层需要额外设计
- 三层结构增加维护成本

**为什么推荐**：在完整性和用户控制之间取得平衡，符合 Personal-AI 的所有冻结约束和设计原则。

---

## 14. Proposed Architecture

### 14.1 推荐方案：Option C — Layered Memory with User Control

基于以上分析，推荐 Option C 作为 Memory Architecture 的 Proposed 方案。

### 14.2 架构总览

```
┌─────────────────────────────────────────────────────────┐
│                    Memory Architecture                    │
│                                                           │
│  ┌─────────────────────────────────────────────────┐     │
│  │              Raw Layer（原始层）                  │     │
│  │  ADD-only · 双时间模型 · 不可变                   │     │
│  │                                                   │     │
│  │  ┌──────────────┐  ┌──────────────────┐          │     │
│  │  │ Event Memory  │  │ Episodic Memory   │          │     │
│  │  │ 原始事件       │  │ 经历片段           │          │     │
│  │  └──────────────┘  └──────────────────┘          │     │
│  └─────────────────────────────────────────────────┘     │
│                         │                                 │
│                    Reflection                             │
│                    (Memory Reflection)                    │
│                         │                                 │
│  ┌─────────────────────────────────────────────────┐     │
│  │            Derived Layer（派生层）                │     │
│  │  可更新 · 非有损演化 · 双时间模型 + 有效期        │     │
│  │                                                   │     │
│  │  ┌──────────────┐  ┌──────────┐  ┌────────────┐  │     │
│  │  │ Semantic Mem  │  │ User Mem  │  │ Project Mem│  │     │
│  │  │ 知识           │  │ 用户认知   │  │ 项目认知    │  │     │
│  │  └──────────────┘  └──────────┘  └────────────┘  │     │
│  │  ┌──────────────────────────────────────────┐    │     │
│  │  │ Consolidation Output（整理结果）            │    │     │
│  │  └──────────────────────────────────────────┘    │     │
│  └─────────────────────────────────────────────────┘     │
│                                                           │
│  ┌─────────────────────────────────────────────────┐     │
│  │          User Control Layer（用户控制层）         │     │
│  │                                                   │     │
│  │  ┌──────────────┐  ┌──────────────┐              │     │
│  │  │ Deletion Log  │  │ Correction   │              │     │
│  │  │ 删除记录       │  │ Log 修正记录  │              │     │
│  │  └──────────────┘  └──────────────┘              │     │
│  │  ┌──────────────────────────────────────────┐    │     │
│  │  │ Provenance Metadata（溯源元数据）           │    │     │
│  │  └──────────────────────────────────────────┘    │     │
│  └─────────────────────────────────────────────────┘     │
│                                                           │
│  ┌─────────────────────────────────────────────────┐     │
│  │              Lifecycle（生命周期）                │     │
│  │                                                   │     │
│  │  Retain ──→ Recall ──→ Reflect                   │     │
│  │  (存储)     (检索)     (整理·由Reflection执行)     │     │
│  └─────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────┘
```

### 14.3 三层结构说明

#### Raw Layer（原始层）

| 属性 | 值 |
|------|-----|
| 不可变性 | ADD-only（Historical Immutability） |
| 时间模型 | 双时间（valid_time + transaction_time） |
| 内容 | Event Memory + Episodic Memory |
| 修改者 | 无（AI 不可修改，用户可删除） |
| 溯源 | 每条记录关联 source Experience Event |

#### Derived Layer（派生层）

| 属性 | 值 |
|------|-----|
| 不可变性 | 非有损演化（可标记失效，不删除） |
| 时间模型 | 双时间 + 有效期（valid_from / valid_to / transaction_time） |
| 内容 | Semantic Memory + User Memory + Project Memory + Consolidation Output |
| 修改者 | Reflection（自动）+ 用户（显式修正） |
| 溯源 | 每条记录关联 source Raw Layer 记录 + consolidation_history |

#### User Control Layer（用户控制层）

| 属性 | 值 |
|------|-----|
| 不可变性 | ADD-only（操作记录不可变） |
| 内容 | Deletion Log + Correction Log + Provenance Metadata |
| 作用 | 记录用户对 Memory 的所有控制操作 |
| 约束 | 用户删除后保留溯源元数据，不保留记忆内容 |

### 14.4 生命周期与三层结构的关系

```
Experience Event
    │
    ↓ Retain
Raw Layer（Event Memory + Episodic Memory）
    │
    ↓ Reflection（Memory Reflection）
Derived Layer（Semantic + User + Project + Consolidation Output）
    │
    ↓ Recall（多信号检索）
Brain 组件（Planning / Agency / 用户响应 / Self Model）
    │
    ↓ User Control
User Control Layer（Deletion Log + Correction Log + Provenance）
```

### 14.5 Memory 与其他 Brain 组件的接口

| 接口 | 方向 | 说明 |
|------|------|------|
| Experience → Memory | 写入 | Experience Event 通过 Retain 进入 Raw Layer |
| Memory → Self Model | 读取 | Self Model 从 Memory 获取行为数据（Memory 不做结论） |
| Memory → Reflection | 读取 | Reflection 从 Memory 获取原始经历进行整理 |
| Reflection → Memory | 写入 | Reflection 的整理结果写入 Derived Layer |
| Memory → Planning | 读取 | Planning 从 Memory 获取历史经验 |
| Memory → Agency | 读取/触发 | Agency 监控 Memory 变化（Level 1 自主观察） |
| Memory → 用户响应流 | 读取 | 用户响应时 Recall 相关记忆组装上下文 |
| User → Memory | 控制 | 用户通过 User Control Layer 操作 Memory |

**关键约束**：
- Memory 只提供数据，不做结论——Memory 不替 Self Model 评估能力，不替 Goal 设定方向，不替 Agency 做决策
- Memory 不直接被 Agent 访问（ADR-004 D7）
- Memory 的所有写入（Retain / Reflection 输出）通过 EventStream 记录（ADR-003）

---

## 15. Invariants — 架构不变量

以下不变量是 Memory Architecture 的架构级约束，任何实现都必须满足：

| # | 不变量 | 说明 | 来源 |
|---|--------|------|------|
| INV-1 | Raw Layer ADD-only | 原始记录创建后不可修改（AI 不可修改，用户可删除） | ADR-002 D5 / Philosophy §3 |
| INV-2 | 双时间模型 | 所有 Memory 记录采用 valid_time + transaction_time | Philosophy §7.1 / Graphiti P0-3 |
| INV-3 | 非有损演化 | Derived Layer 的 Knowledge 修正不删除旧版本 | Graphiti P0-4 / Philosophy §3 |
| INV-4 | 溯源完整 | 每条 Memory 记录可追溯到 source Experience Event | 架构级原则 |
| INV-5 | Memory ≠ EventStream | Memory 是经历记忆，EventStream 是事实记录 + 通信总线 | ADR-003 |
| INV-6 | Memory ≠ Brain Persistence | Brain Persistence 范围大于 Memory | ADR-001 |
| INV-7 | Memory 不做结论 | Memory 只提供数据，不替其他组件做结论 | Philosophy §3 |
| INV-8 | Memory 不修改 Protected Core | Memory 不存储也不修改核心价值/授权/安全规则 | ADR-002 Layer A |
| INV-9 | Agent 不直接访问 Memory | Agent 通过 Brain → EventStream → Execution 路径获取记忆 | ADR-004 D7 |
| INV-10 | 用户控制优先 | 用户可以删除/修正任何记忆，优先于 ADD-only | Human-centric / 隐私主权 |
| INV-11 | Consolidation 由 Reflection 执行 | Memory 不执行整理逻辑，Reflection 执行整理并写入 Memory | Philosophy §3 / §10.3 |
| INV-12 | Memory 变更通过 EventStream | Memory 的所有写入通过 EventStream 记录 | ADR-003 |

---

## 16. Deferred Decisions

以下决策属于后续 Phase 或更细粒度设计，本 Phase 不做决策：

| # | Deferred Decision | 归属 Phase | 说明 |
|---|-------------------|-----------|------|
| D-1 | 数据库类型 / 存储引擎 | Phase 3 | Memory 使用什么数据库（关系型 / 图 / 向量 / 混合） |
| D-2 | 表结构 / JSON Schema | Phase 3 | Memory 记录的具体数据结构 |
| D-3 | Embedding Model / Chunk 参数 | Phase 3 | 向量检索的具体参数 |
| D-4 | Recall Top-K / 检索权重 | Phase 3 | 多信号检索的具体参数 |
| D-5 | Retain 策略细节 | Phase 3 | 什么值得 Retain、Retain 粒度、Retain 时机 |
| D-6 | Consolidation 算法 | Phase 2.4 | Dream / Consolidation 的具体算法属于 Reflection 设计 |
| D-7 | Forgetting 策略参数 | Phase 3 | Weight Decay 因子、Archival 阈值、Physical Purge 条件 |
| D-8 | Conflict Resolution 算法 | Phase 2.4 | 冲突检测和解决的具体算法 |
| D-9 | Memory 与 Self Model 的数据接口 | Phase 2.3 | Self Model 如何从 Memory 获取行为数据 |
| D-10 | Memory 与 Reflection 的执行接口 | Phase 2.4 | Reflection 如何读取 Memory、如何写入整理结果 |
| D-11 | Memory 物理存储与 Snapshot 的关系 | Phase 3 | Memory Storage 是否承载 Brain Snapshot（ADR-001 提到 Snapshot 可以由 Memory Storage 承载） |
| D-12 | Memory 导出格式 | Phase 3 | 用户导出 Memory 的数据格式 |
| D-13 | Memory 跨设备同步 | Phase 3 | 多设备场景下 Memory 的同步机制 |
| D-14 | Memory 加密 / 安全 | Phase 3 | Memory 数据的加密和访问控制 |

---

## 17. Open Questions for Principal Architect

以下问题需要 Principal Architect 决策，本 Phase 不做决策：

### OQ-1: Memory 是否需要三层子图（Episode → Entity → Community）？

**背景**：Graphiti 提出了三层子图模型（Landscape Report P1-1），Episode（事件）→ Entity（语义）→ Community（抽象）。本 Phase 的 Raw Layer + Derived Layer 是否对应 Episode + Entity？Community 层是否需要？

**问题**：Community 层（抽象聚类）是 Memory 的职责，还是 Reflection 的输出？

**影响**：如果 Community 属于 Memory，Memory 需要增加第三层；如果属于 Reflection，Memory 保持两层。

**AI 建议**：Community 属于 Reflection 的输出，写入 Derived Layer 的 Consolidation Output。Memory 保持两层（Raw + Derived）。

### OQ-2: 4 层记忆模型（感官/短期/工作/长期）是否采用？

**背景**：SOUL.md 的 soulclaw 实现了 4 层记忆（Landscape Report P1-3）：感官记忆、短期记忆、工作记忆、长期记忆。

**问题**：Personal-AI 的 Memory 是否需要感官/短期/工作记忆层，还是只保留长期记忆？

**影响**：感官/短期/工作记忆更接近 Runtime Context（ADR-004 中的 Execution 临时状态），可能不属于 Memory 核心能力。

**AI 建议**：感官/短期/工作记忆属于 Execution Layer 的 Runtime Context（ADR-004），不属于 Memory。Memory 只负责长期记忆（Raw Layer + Derived Layer）。

### OQ-3: Memory 的 Condensers（上下文压缩）是否采用？

**背景**：OpenHands 的 Condensers 机制（Landscape Report P1-4）用于长对话/长记忆的压缩。

**问题**：Condensers 是 Memory 的 Recall 阶段的一部分，还是 Execution Layer 的上下文管理？

**影响**：如果属于 Memory，Recall 需要返回压缩后的记忆；如果属于 Execution，Memory 返回原始记忆，Execution 自行压缩。

**AI 建议**：Condensers 属于 Execution Layer 的上下文管理。Memory 的 Recall 返回相关记忆集合，压缩由 Execution 负责。

### OQ-4: Memory 是否需要独立的事件系统（Episode 层）？

**背景**：Graphiti 的 Episode 层本质上是一个事件系统（Landscape Report §8.9）。Personal-AI 已有 EventStream（ADR-003）。

**问题**：Memory 的 Raw Layer（Event Memory）是否就是 EventStream 的子集，还是 Memory 有自己的事件存储？

**影响**：如果 Memory 的 Event Memory 就是 EventStream 的 Experience Event 子集，Memory 不需要独立的事件存储；如果需要独立存储，存在数据冗余。

**AI 建议**：Memory 的 Event Memory 是 EventStream 中 Experience Event 的认知加工副本。EventStream 是事实记录（通信总线），Memory 的 Event Memory 是经历记录（带上下文、双时间标记）。两者分离但关联——Memory 的 Event Memory 引用 EventStream 的对应事件 ID。

### OQ-5: Memory 的物理存储是否承载 Brain Snapshot？

**背景**：ADR-001 提到"Snapshot 可以由 Memory Storage 承载"。

**问题**：Memory Storage 是否就是 Brain Snapshot 的物理存储，还是 Snapshot 有独立的存储？

**影响**：如果 Memory Storage 承载 Snapshot，Memory 的物理存储需要支持 Snapshot 的读写；如果独立，Memory 只负责经历记忆。

**AI 建议**：Deferred to Phase 3。本 Phase 只确认 Memory 的逻辑范围（经历记忆），物理存储与 Snapshot 的关系是 Phase 3 的决策。

### OQ-6: Memory 是否需要版本化（Versioning）？

**背景**：Memory 的 Derived Layer 采用非有损演化，Knowledge 会不断修正。

**问题**：Memory 是否需要像 Identity 一样有显式的版本号，还是通过双时间模型 + 有效期隐式管理版本？

**影响**：显式版本化增加管理复杂度，但便于回滚；隐式管理更简洁，但回滚需要时间查询。

**AI 建议**：通过双时间模型 + 有效期隐式管理版本，不引入显式版本号。Memory 的版本管理通过时间查询实现。

### OQ-7: Memory 与 Identity 的边界——User Memory vs User Profile

**背景**：Memory 有 User Memory（关于用户的记忆），Identity 有用户关系定义。

**问题**：User Memory（Memory）和 User Profile / User Relationship Definition（Identity）的边界在哪里？

**影响**：如果边界不清晰，User Memory 可能逐渐膨胀为 User Profile，违反 Memory ≠ Identity。

**AI 建议**：User Memory 记录"用户做过什么、说过什么"（经历），Identity 的用户关系定义记录"我如何理解与用户的关系"（结论）。Memory 不做结论，Identity 从 Memory 提炼结论。

### OQ-8: Memory 的初始状态（Bootstrap）

**背景**：Personal-AI 首次启动时，Memory 为空。

**问题**：Memory 是否需要预置任何初始记忆，还是完全从零开始积累？

**影响**：预置初始记忆可能影响 Identity 的初始状态；完全从零开始可能导致早期用户体验不佳。

**AI 建议**：Memory 完全从零开始。初始 Identity 由用户配置（Protected Core + 初始人格特征），Memory 通过经历积累逐步丰富。这与 Philosophy §6 的生命周期一致（"新生儿期：Memory 为空"）。

---

## 18. Summary

### 18.1 核心决策（Proposed）

| # | 决策 | 说明 |
|---|------|------|
| D-1 | Memory = cognitive/experiential memory | 不是所有持久化数据的总称（ADR-001 冻结） |
| D-2 | Memory 采用三层结构 | Raw Layer（ADD-only）+ Derived Layer（非有损演化）+ User Control Layer |
| D-3 | 双时间模型是架构级原则 | 所有 Memory 记录采用 valid_time + transaction_time |
| D-4 | ADD-only 精确定义 | Historical Immutability + Logical Invalidation + User Deletion + Physical Deletion |
| D-5 | Consolidation 由 Reflection 执行 | Memory 不执行整理逻辑，Reflection 执行并写入 Memory |
| D-6 | Memory 生命周期 = Retain / Recall / Reflect | 参考 Hindsight 三阶段模型 |
| D-7 | 多信号检索 | 向量 + 图 + 关键词 + 时间 + 上下文（参考 Mem0） |
| D-8 | 非有损演化 | Knowledge 修正不删除旧版本，标记有效期（参考 Graphiti） |
| D-9 | 用户控制优先 | 用户可以删除/修正任何记忆，优先于 ADD-only |
| D-10 | Memory 不做结论 | Memory 只提供数据，不替 Self Model / Goal / Agency 做结论 |

### 18.2 与冻结架构的一致性

| 冻结约束 | 本文档一致性 |
|---------|-------------|
| ADR-001: Memory = cognitive/experiential memory | §2.1 一致 |
| ADR-001: Memory ≠ Brain Persistence | §2.1 / §3.2 一致 |
| ADR-002: Memory 原始记录 ADD-only | §8.1 / §14.3 Raw Layer 一致 |
| ADR-002: Memory 整理结果属 Autonomous Evolution | §10.3 / §14.3 Derived Layer 一致 |
| ADR-003: Memory ≠ EventStream | §2.1 / §3.3 / §5.1 一致 |
| ADR-003: Experience 事件双写 | §3.3 一致 |
| ADR-004: Agent 不直接访问 Memory | §9.3 / INV-9 一致 |
| ADR-005: Memory 变化可触发 Agency | §11.3 一致 |
| ADR-005: 用户授权边界约束 | §9.3 / §12 一致 |
| Philosophy §3: Memory 五类型 | §4 一致 |
| Philosophy §7.1: Memory 架构影响 | 全文一致 |

---

> **文档状态：Draft**
>
> 本文档是 Phase 2.1 Memory Architecture 的输出，包含 12 个 Architecture Questions 的分析、三种架构选项的比较、推荐方案（Option C）、12 个架构不变量、14 个 Deferred Decisions、8 个 Open Questions。
>
> **需要 Principal Architect Review**：特别是 OQ-1 到 OQ-8 的 8 个 Open Questions。
>
> **Status**: Phase 2.1 Memory Architecture — Ready for Principal Architect Review
