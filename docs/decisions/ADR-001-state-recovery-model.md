# ADR-001: State Recovery Model

> **Status**: Proposed
>
> **Date**: 2026-08-07
>
> **Supersedes**: None
>
> **Related Documents**:
> - `docs/decisions/ADR-000-direction-correction.md`
> - `docs/design/00-Architecture-Overview.md` §8 State Recovery Model
> - `docs/decisions/ADR-003-eventstream-positioning.md`

---

## Context

### 为什么需要这个决策

Personal-AI 的核心设计原则之一是 **Long-lived**——以 5-10 年连续运行为基准。这意味着系统必然经历：

- 进程重启（部署升级、崩溃恢复）
- 硬件故障（磁盘损坏、内存丢失）
- 长时间休眠后唤醒（用户数月不使用后回归）
- 架构升级（数据格式迁移）

Architecture Overview §4.1 声明 Brain 持有 Digital Life 的权威长期状态，Brain 是认知状态的逻辑权威所有者（authoritative owner of cognitive state），但未定义恢复的具体机制和一致性保证。

### 未解决的核心问题

1. **Brain 是否是 source of truth？**——如果是，Brain 的状态权威来源是什么？
2. **Memory 是否保存全部状态？**——Memory 是"经历记录"，还是也承担"系统状态快照"？
3. **Self Model / Goal / Identity 如何恢复？**——这些组件的状态是独立持久化，还是从 Memory 重放得出？
4. **Event replay 是否存在？**——重启后是否需要重放事件流来恢复状态？
5. **Recovery 的边界是什么？**——哪些状态可恢复，哪些不可恢复？
6. **Bootstrap 悖论**——Memory 是 Brain 的子组件，但 Brain 启动需要从 Memory 恢复，如何解决？

---

## Decision

### D1: Brain 是认知状态的权威所有者，恢复依据是 Snapshot + Event Delta

Brain 是 Personal-AI 认知状态的逻辑权威所有者（authoritative owner of cognitive state）。Execution Layer 和 Environment Layer 不持有权威长期状态。

Durable Brain State = Snapshot + committed Event Delta。Snapshot 可以由 Memory Storage 承载，但完整恢复路径依赖 Snapshot + EventStream，不是 Memory 单独保存 Brain 的全部状态。

Memory 是认知/经历记忆（cognitive/experiential memory），不是"所有持久化东西的总称"。Brain Persistence 的范围大于 Memory——它包括 cognitive memory + Brain Snapshot（Identity 版本、Self Model 快照、Goal 状态、Agency Queue 状态）+ version history + Event Delta reference。

```
Brain（运行时认知状态）
    ↕ 持久化 / 恢复
Snapshot + Event Delta
    ↕ 承载
Memory Storage（cognitive memory）+ EventStream（system events）
```

### D2: Brain 状态分为三类，采用不同的恢复策略

| 状态类别 | 包含组件 | 恢复策略 |
|----------|----------|----------|
| **Event-Sourced State**（事件可重放状态） | Memory 的经历记录本身 | 直接从持久化存储加载，无需重放 |
| **Snapshot State**（快照状态） | Identity 版本、Self Model 版本、Goal 状态、Agency Initiative Queue | 从最近的 Snapshot 加载 + 重放 Snapshot 之后的增量事件 |
| **Derived State**（可重建状态） | Reflection 的中间结果、Self Model 的缓存索引 | 不持久化，重启后按需重建 |

### D3: 采用 Snapshot + Event Delta 恢复模型

Brain 的可恢复状态（Snapshot State）采用 **Snapshot + Event Delta** 模型：

```
恢复流程：
1. 加载最近的 Snapshot（Identity v2.3, Self Model v1.8, Goal 状态, Agency Queue 状态）
2. 重放 Snapshot 之后的所有 Brain 内部事件（Event Delta）
3. Brain 恢复到崩溃前的最终一致状态
```

Snapshot 是 Brain 状态在某个时间点的完整快照。Event Delta 是 Snapshot 之后发生的增量变化。

Snapshot 的创建频率由 Reflection 的 Dream/Consolidation 周期决定——每次 Consolidation 完成后创建新 Snapshot。

### D4: Memory 的 Bootstrap 优先级

解决 Bootstrap 悖论：Memory 是 Brain 的子组件，但 Brain 启动需要从 Memory 恢复。

**分层启动顺序**：

```
1. 加载 Memory 存储层（底层持久化，不依赖 Brain 运行时）
2. 从 Memory 存储层加载最近的 Snapshot
3. 从 Memory 存储层加载 Event Delta（Snapshot 之后的经历记录）
4. 重放 Event Delta，恢复 Identity / Self Model / Goal / Agency 状态
5. Brain 启动完成，进入正常运转
```

Memory 的"存储层"（持久化介质）与"运行时"（Brain 内的 Memory 组件）分离。存储层是基础设施，不依赖 Brain 运行时；运行时是 Brain 组件，从存储层加载。

### D5: Recovery 边界

| 可恢复 | 不可恢复 |
|--------|----------|
| Identity 版本历史 | Runtime 内存中的临时计算中间态 |
| Memory 全部经历记录 | Agent 执行过程中的临时上下文 |
| Self Model 版本历史 | 未完成 Reflection 的中间推理过程 |
| Goal 状态和版本历史 | 未发送的 Initiative 草稿（已提交到 Queue 的可恢复） |
| Agency Initiative Queue 状态 | Execution Layer 的 Agent 运行时状态 |
| EventStream 已持久化的事件 | Environment Layer 的外部系统临时连接状态 |

**不可恢复的状态都是"可重建"或"可丢弃"的**——Runtime 临时态可丢弃，Agent 状态可重建，外部连接可重连。没有任何"丢失后导致 Brain 不一致"的状态。

### D6: Execution Layer 的恢复行为

Execution Layer 不持有 Digital Life 的权威长期状态（durable-state-free），重启后：

1. 不需要恢复权威长期状态——运行期临时状态（Agent 执行上下文、retry 计数、Tool 连接缓存等）可丢失、可重建
2. 从 Brain 的 Goal 和 Agency Initiative Queue 获取待执行任务
3. 被中断的任务标记为"中断"状态，由 Brain 决定是否重试

Execution Layer 的 Agent 运行时临时状态不持久化。被中断的 Agent 执行结果不恢复，由 Brain 的 Planning 重新分派。

---

## Consequences

### 正面影响

- **Long-lived 保障**：Brain 状态可通过 Snapshot + Event Delta 恢复，保证 5-10 年连续性
- **Bootstrap 悖论解决**：Memory 存储层与运行时分离，启动顺序明确
- **恢复效率**：Snapshot 避免了每次重启都从头重放全部事件
- **审计能力**：Snapshot + Event Delta 模型天然支持状态历史追溯
- **Execution 简化**：Execution 不持有权威长期状态，重启后无需复杂恢复逻辑

### 负面影响

- **Snapshot 管理复杂度**：需要管理 Snapshot 的创建、版本、清理
- **Event Delta 一致性**：Snapshot 与 Event Delta 的边界需要精确定义，否则可能丢失或重复
- **存储成本**：Snapshot + Event Delta + 原始 Memory 记录，存储开销较大
- **恢复延迟**：大型 Brain 状态的 Snapshot 加载 + Event Delta 重放可能需要数秒到数分钟

---

## Alternatives Considered

### Alt-1: Pure Event Sourcing（纯事件溯源）

**方案**：Brain 的全部状态从 EventStream 重放得出，不使用 Snapshot。

**为什么不选**：
- 5 年的 EventStream 重放可能需要数小时，不可接受
- 每次重启都从头重放，恢复时间随运行时间线性增长
- EventStream 的体积无上限增长，存储和重放成本不可控

**保留**：Event Delta 部分借鉴了 Event Sourcing 的思想，但用 Snapshot 限制了重放范围。

### Alt-2: Independent Persistence（各组件独立持久化）

**方案**：Identity、Memory、Self Model、Goal、Agency 各自独立持久化，不统一管理。

**为什么不选**：
- 跨组件一致性无法保证——一个组件恢复成功，另一个失败，Brain 状态不一致
- 恢复顺序复杂——组件间有依赖关系，需要精确定序
- 缺少统一的审计视图——无法从单一来源追溯 Brain 的完整状态历史

**保留**：各组件可以在 Snapshot 内部有独立的持久化结构，但恢复由统一的 Snapshot + Event Delta 机制管理。

### Alt-3: External Database as Source of Truth

**方案**：使用外部数据库（如 PostgreSQL）作为 Brain 状态的 source of truth，Brain 运行时从数据库加载。

**为什么不选**：
- 违反 Phase 1 约束——不设计具体数据库
- 将 Brain 的连续性绑定到外部基础设施，降低可移植性
- 数据库的 schema 演化问题与 Brain 的演化问题叠加，复杂度倍增

**保留**：Memory 存储层的具体实现可以在 Phase 2 决定是否使用外部数据库。

---

> **ADR-001 决策完成**
>
> Brain = authoritative owner of cognitive state。Recovery authority = latest valid Snapshot + committed Event Delta。
>
> Durable Brain State = Snapshot + committed Event Delta。
>
> Bootstrap：Memory 存储层优先启动，然后恢复 Brain 运行时。
>
> Execution 不持有权威长期状态，重启后从 Brain 获取待执行任务。
