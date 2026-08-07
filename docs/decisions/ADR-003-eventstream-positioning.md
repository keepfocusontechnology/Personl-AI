# ADR-003: EventStream Positioning

> **Status**: Accepted
>
> **Date**: 2026-08-07
>
> **Supersedes**: None
>
> **Related Documents**:
> - `docs/decisions/ADR-001-state-recovery-model.md`
> - `docs/design/00-Architecture-Overview.md` §10 EventStream Positioning
> - `docs/design/00-Architecture-Overview.md` §7.2 Event-driven Principle

---

## Context

### 为什么需要这个决策

Architecture Overview §7.2 声明"所有组件之间通过 EventStream 通信"，§7.5 声明"EventStream 中的所有事件持久化"。但 EventStream 的架构定位不明确：

- 它是"消息总线"（通信通道，状态由各组件自行管理）？
- 还是"系统事实记录"（Event Sourcing 的 Source of Truth，状态可从事件重放得出）？
- 还是两者兼有？

这个定位直接影响：
- ADR-001 的 State Recovery Model——如果 EventStream 是 Source of Truth，Recovery = Event Replay
- Memory 的架构定位——Memory 与 EventStream 是统一还是分离
- Brain 内部通信——Brain 内部组件之间是否也必须事件化

### 未解决的核心问题

1. **EventStream 是消息总线还是 Source of Truth？**
2. **EventStream 是否支持 replay？**
3. **EventStream 是否承担状态恢复？**
4. **Brain 内部是否也必须事件化？**
5. **EventStream 与 Memory 的关系——统一还是分离？**

---

## Decision

### D1: EventStream 是"系统事实记录 + 通信总线"的统一体

EventStream 不是普通消息队列。它同时承担两个职责：

```
┌──────────────────────────────────────────────┐
│  EventStream                                  │
│                                               │
│  职责 1: 系统事实记录（System Fact Log）       │
│    - 记录所有已发生的事件                      │
│    - 事件不可变                                │
│    - 支持按时间、类型、来源检索                │
│    - 支持重放（Replay）                        │
│                                               │
│  职责 2: 通信总线（Communication Bus）         │
│    - 组件之间通过事件通信                      │
│    - 发布-订阅模式                             │
│    - 解耦组件依赖                              │
└──────────────────────────────────────────────┘
```

这两个职责不是分离的——同一条事件既是"事实记录"也是"通信消息"。

### D2: EventStream 支持 Replay，但不承担完整状态恢复

EventStream 支持按时间范围重放事件。但 **EventStream 不是 Brain 状态的唯一 Source of Truth**。

根据 ADR-001，Brain 状态恢复采用 **Snapshot + Event Delta** 模型：
- Snapshot 是 Brain 状态的权威快照
- EventStream 中的事件用于重放 Snapshot 之后的增量变化

```
恢复时 EventStream 的角色：
1. 加载 Snapshot（来自 Memory 存储层）
2. 从 EventStream 重放 Snapshot 之后的 Brain 内部事件（Event Delta）
3. Brain 恢复到最终一致状态
```

EventStream 承担"增量恢复"职责，不承担"完整状态恢复"职责。完整状态由 Snapshot + EventStream 共同保证。

### D3: EventStream 与 Memory 的关系——分离但互补

**EventStream ≠ Memory。** 两者是不同的东西，但有交集。

| 维度 | EventStream | Memory |
|------|-------------|--------|
| 记录内容 | 系统事件（已发生的所有事件） | 经历记录（用户经历 + 系统经历） |
| 不可变性 | 事件不可变 | ADD-only（原始记录不可变，整理结果可更新） |
| 用途 | 通信 + 事实记录 + 增量恢复 | 经历积累 + 知识提取 + 状态持久化 |
| 时间模型 | 单时间（事件发生时间） | 双时间（valid_time + transaction_time） |
| 结构 | 事件序列 | 结构化记忆（情景/语义/程序/偏好） |

**关系**：

```
Experience 事件 ──→ EventStream（作为系统事件记录）
                    ↓
                  Memory（作为经历记录，Retain 到 Memory 存储层）

Brain 内部事件（如 Self Model 更新、Goal 变更）
    ──→ EventStream（作为系统事件记录）
    ──→ 不一定进入 Memory（取决于事件类型）
```

- **所有 Experience 事件同时进入 EventStream 和 Memory**——EventStream 记录"发生了这个事件"，Memory 记录"这个事件的经历内容"
- **Brain 内部事件只进入 EventStream**——如 Self Model 版本更新、Goal 状态变更，这些是系统事件，不是用户经历
- **Memory 的 Dream/Consolidation 输出进入 EventStream**——整理结果作为系统事件记录，同时更新 Memory 的结构化记忆

### D4: Brain 内部组件之间必须通过 EventStream 通信

Brain 内部的 Identity、Memory、Self Model、Reflection、Goal、Agency 之间的所有跨组件交互必须通过 EventStream。

**不允许**：
- Identity 直接调用 Agency 的方法
- Reflection 直接写入 Self Model
- Goal 直接通知 Execution

**必须**：
- Identity 发布"Identity 更新事件" → Agency 订阅
- Reflection 发布"Self Model 更新提议事件" → Self Model 订阅
- Goal 发布"Goal 变更事件" → Agency 订阅

**例外**：同步用户响应流（§6.3）中，为了低延迟，允许 Brain 内部组件之间的直接读取（不是写入）。例如 Self Model 可以直接读取 Memory 的检索结果。但状态变更必须通过事件。

### D5: EventStream 的事件分类

| 事件类别 | 来源 | 示例 | 进入 Memory？ |
|----------|------|------|---------------|
| Experience Event | 用户消息、外部事件、Agent 执行结果 | 用户发来消息、日历事件触发 | 是（作为经历记录） |
| Brain State Event | Brain 内部组件的状态变更 | Identity 版本更新、Self Model 更新、Goal 变更 | 否（系统事件） |
| Agency Event | Agency 的决策和行动 | Trigger 触发、Decision 产出、Initiative 创建 | 否（系统事件） |
| Execution Event | Execution Layer 的执行过程 | Agent 启动、Agent 完成、Tool 调用 | 部分（执行结果进入 Memory） |
| Reflection Event | Reflection 的过程和输出 | Reflection 开始、Reflection 完成、策略调整 | 部分（策略调整进入 Self Model） |

### D6: EventStream 的持久化与清理

- **持久化**：所有事件持久化，不可删除
- **清理**：EventStream 不清理历史事件。但 Replay 时可以指定时间范围，不需要重放全部历史
- **Snapshot 关联**：每个 Snapshot 记录其对应的 EventStream 位置（offset/timestamp），Recovery 时从该位置开始重放

---

## Consequences

### 正面影响

- **统一通信**：所有组件间通信通过 EventStream，无隐式依赖
- **可审计**：所有事件持久化，可追溯
- **可重放**：支持按时间范围重放，用于恢复、调试、审计
- **解耦**：组件之间通过事件解耦，可独立演化
- **与 Recovery 对齐**：EventStream 的 Replay 能力直接支持 ADR-001 的 Event Delta 恢复

### 负面影响

- **延迟**：事件驱动的通信比直接调用有更高延迟（同步流通过"直接读取"例外缓解）
- **事件体积**：5 年的 EventStream 体积可能很大，需要高效的存储和检索方案（Phase 2 设计）
- **事件 schema 管理**：不同类别的事件需要不同的 schema，schema 演化是长期挑战
- **调试复杂度**：事件驱动的系统调试比直接调用更复杂，需要事件追踪工具

---

## Alternatives Considered

### Alt-1: EventStream as Pure Message Bus（纯消息总线）

**方案**：EventStream 只是消息通道，不持久化事件，不支持 Replay。各组件自行管理状态。

**为什么不选**：
- 无法支持 ADR-001 的 Event Delta 恢复
- 违反 Auditable 原则——事件不持久化，无法追溯
- 失去"系统事实记录"能力，Long-lived 保障减弱

### Alt-2: EventStream as Sole Source of Truth（纯事件溯源）

**方案**：Brain 的全部状态从 EventStream 重放得出，不使用 Snapshot，Memory 是 EventStream 的子集。

**为什么不选**：
- 5 年的 EventStream 重放不可行（ADR-001 Alt-1 已否决）
- Memory 的双时间模型和结构化记忆无法简单从事件重放得出
- Memory 和 EventStream 的语义不同——Memory 是"经历"，EventStream 是"事件"

### Alt-3: Brain Internal Direct Calls（Brain 内部直接调用）

**方案**：Brain 内部组件之间直接调用，只有 Brain ↔ Execution 通过 EventStream。

**为什么不选**：
- Brain 内部产生隐式依赖，组件难以独立演化
- 内部状态变更不可追溯，违反 Auditable 原则
- Recovery 时无法重放 Brain 内部事件来恢复状态

---

> **ADR-003 决策完成**
>
> EventStream = 系统事实记录 + 通信总线。
>
> 支持 Replay，承担增量恢复（Snapshot 之后的 Event Delta）。
>
> EventStream ≠ Memory——分离但互补。
>
> Brain 内部必须事件化（状态变更必须通过事件，读取可例外）。
