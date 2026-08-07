# ADR-004: Brain Execution Boundary

> **Status**: Accepted
>
> **Date**: 2026-08-07
>
> **Supersedes**: None
>
> **Related Documents**:
> - `docs/decisions/ADR-000-direction-correction.md`
> - `docs/decisions/ADR-001-state-recovery-model.md`
> - `docs/decisions/ADR-003-eventstream-positioning.md`
> - `docs/design/00-Architecture-Overview.md` §4 Layer Responsibility
> - `docs/design/Personal-AI_Philosophy.md` §5 Brain vs Agent Model

---

## Context

### 为什么需要这个决策

Architecture Overview §3.2 定义了三层架构的层间关系："Brain 与 Execution 之间只通过 EventStream 通信——不直接调用"。§4.2 声明"Execution 是无状态的——重启后从 Brain 的 Memory 恢复上下文"。

但 Brain 和 Execution 的边界不够明确：

- Brain 的"智能"边界到哪里？Planning 在 Brain 还是 Execution？
- Execution 的"行动"边界到哪里？Execution 能否自主决策？
- Execution 能否持久化任何状态？
- Agent 是 Execution 的一部分还是独立实体？
- Brain 能否直接调用 Tool（绕过 Execution）？

这些边界不冻结，Detailed Design 中会出现职责重叠和状态泄漏。

### 未解决的核心问题

1. **Brain 负责什么？**——智能、状态、身份的边界
2. **Execution 负责什么？**——行动、运行时的边界
3. **Planning 归属**——Planning 在 Brain 还是 Execution？
4. **Execution 能否持久化状态？**
5. **Brain 能否直接调用 Tool？**
6. **Agent 的定位**——Execution 的子组件还是独立实体？

---

## Decision

### D1: Brain = Identity / State / Intelligence

Brain 负责 Personal-AI 的**存在本质**和**智能决策**。

| Brain 职责 | 说明 |
|------------|------|
| Identity 维护 | 人格、价值观、行为原则的持久化和演化 |
| Memory 管理 | 经历记录的 Retain / Recall / Reflect |
| Self Model 维护 | 动态自我认知的更新 |
| Reflection 执行 | Memory Reflection + Behavior Reflection |
| Goal 管理 | Vision → Long Term Goal → Project Goal → Task 的管理 |
| Agency 决策 | Trigger 识别、Decision 判断、Initiative 管理 |
| Planning | 从 Goal 生成执行 Plan（Plan 是 Brain 的智能输出，不是 Execution 的行为） |
| Context 组装 | 为 Execution 提供 Agent 执行所需的上下文（从 Memory 提取） |

**关键约束**：Brain 不直接执行外部操作。Brain 的输出是"决策"和"Plan"，不是"Action"。

### D2: Execution = Action / Runtime

Execution 负责 Personal-AI 的**行动执行**和**运行时管理**。

| Execution 职责 | 说明 |
|------------|------|
| Plan 接收 | 从 Brain 接收 Plan（通过 EventStream） |
| Agent 调度 | 根据 Plan 创建、分配、监控 Agent |
| Tool 执行 | Agent 通过 Tool（MCP/API）执行具体操作 |
| Result 收集 | 收集 Agent 执行结果，通过 EventStream 返回 Brain |
| Runtime 管理 | Agent 运行环境、资源隔离、生命周期管理 |
| 执行监控 | 监控 Agent 执行状态，异常时通知 Brain |

**关键约束**：Execution 不做任何"智能决策"。Execution 的所有行为由 Brain 的 Plan 驱动。

### D3: Planning 归属 Brain，不是 Execution

**Planning 是 Brain 的智能输出，不是 Execution 的行为。**

```
Brain                          Execution
─────                          ─────────
Goal                           Plan 接收
  ↓                              ↓
Planning（考虑 Self Model、      Agent 调度
  Memory、资源约束）               ↓
  ↓                            Tool 执行
Plan（结构化执行方案）             ↓
  ↓                            Result 返回
通过 EventStream 发送 →          （通过 EventStream 返回 Brain）
```

Planning 需要读取 Self Model（能力评估）、Memory（历史经验）、Goal（优先级），这些都是 Brain 的内部状态。如果 Planning 在 Execution 中，Execution 需要访问 Brain 内部状态，违反边界。

### D4: Execution 禁止持久化任何 Brain 状态

| Execution 可以做 | Execution 禁止做 |
|------------------|-------------------|
| 临时缓存 Agent 执行上下文（进程内，重启丢失） | 持久化 Identity |
| 临时记录 Agent 执行日志（通过 EventStream 发送给 Brain） | 持久化 Memory |
| 临时管理 Agent 生命周期状态（进程内） | 持久化 Self Model |
| 临时缓存 Tool 连接信息 | 持久化 Goal |
| | 持久化 Agency Initiative Queue |
| | 持久化任何 Brain 组件的状态 |

**原则**：Execution 重启后，不恢复任何自身状态。所有待执行任务从 Brain 的 Goal 和 Initiative Queue 重新获取。

### D5: Brain 禁止直接调用 Tool

| Brain 可以做 | Brain 禁止做 |
|--------------|--------------|
| 通过 Memory 检索信息 | 直接调用 MCP Tool |
| 通过 Reflection 分析经验 | 直接调用外部 API |
| 通过 Agency 产生 Initiative | 直接执行文件操作 |
| 通过 Planning 生成 Plan | 直接与 Environment Layer 交互 |
| 通过 EventStream 与 Execution 通信 | 绕过 Execution 直接行动 |

**原则**：Brain 是"大脑"，不是"手脚"。Brain 的所有外部操作必须通过 Execution → Environment 路径。

**例外**：Memory 的 Dream/Consolidation 是 Brain 内部操作，不涉及外部世界，不需要通过 Execution。

### D6: Agent 是 Execution 的子组件，不是独立实体

Agent 是 Execution Layer 内部的执行单元，不是独立于 Brain 和 Execution 之外的第三类实体。

```
Brain
  ↓ Plan（通过 EventStream）
Execution
  ├── Agent Orchestration（调度）
  │     ├── Agent A（执行 Task 1）
  │     ├── Agent B（执行 Task 2）
  │     └── Agent C（执行 Task 3）
  ├── Tools（MCP/API）
  └── Runtime（执行环境）
```

Agent 的特征：
- **无状态**——Agent 不持有任何跨任务的状态
- **可替换**——Agent 可以被创建、销毁、替换，不影响 Brain
- **生命周期短**——Agent 随 Task 创建，随 Task 完成而销毁
- **通过 EventStream 与 Brain 交互**——Agent 不直接访问 Brain 内部状态

### D7: Brain ↔ Execution 的通信规则

| 通信方向 | 内容 | 机制 |
|----------|------|------|
| Brain → Execution | Plan、Task 分派、Initiative 执行指令 | EventStream 事件 |
| Execution → Brain | Agent 执行结果、执行状态、异常通知 | EventStream 事件 |
| Brain → Execution | Agent 执行所需的上下文 | 随 Plan 一起发送（通过 EventStream） |
| Execution → Brain | Agent 执行过程中的中间状态 | EventStream 事件（Brain 决定是否需要） |

**禁止**：
- Execution 直接读取 Brain 内部状态
- Brain 直接调用 Execution 内部方法
- Agent 直接访问 Memory

---

## Consequences

### 正面影响

- **状态安全**：Brain 状态不会泄漏到 Execution，Execution 重启不影响 Brain
- **Execution 可替换**：Execution 可以整体替换（换 Runtime、换 Agent 框架），不影响 Brain 连续性
- **职责清晰**：Brain 做决策，Execution 做执行，无重叠
- **Recovery 简化**：Execution 无状态，Recovery 只需恢复 Brain（ADR-001）
- **审计清晰**：所有 Brain → Execution 的指令通过 EventStream 记录

### 负面影响

- **延迟**：Brain → EventStream → Execution → EventStream → Brain 的链路比直接调用有更高延迟
- **上下文传输**：Agent 执行所需上下文必须通过 EventStream 传输，可能较大
- **Execution 自主性受限**：Execution 不能自主决策，所有异常必须上报 Brain 等待指令

---

## Alternatives Considered

### Alt-1: Planning in Execution

**方案**：Planning 放在 Execution Layer，Brain 只输出 Goal，Execution 自行规划。

**为什么不选**：
- Planning 需要读取 Self Model（能力评估）、Memory（历史经验），这些是 Brain 内部状态
- 如果 Execution 读取 Brain 内部状态，违反边界分离
- Planning 是智能决策，不是行动执行——放在 Execution 中模糊了"智能"与"行动"的边界

### Alt-2: Shared State Between Brain and Execution

**方案**：Brain 和 Execution 共享部分状态（如 Memory），Execution 可直接读取 Memory。

**为什么不选**：
- Execution 变为有状态，重启后需要恢复——违反"Execution 无状态"原则
- 状态一致性复杂——Brain 和 Execution 同时修改 Memory 时冲突
- Execution 与 Brain 耦合，不可独立替换

### Alt-3: Agent as Independent Entity

**方案**：Agent 是独立于 Brain 和 Execution 的第三类实体，有自己的状态和生命周期。

**为什么不选**：
- 引入第三类实体增加架构复杂度
- Agent 有状态意味着需要 Agent 级别的 Recovery 机制
- 违反"Brain = 状态，Execution = 行动"的清晰划分
- Agent 有状态会导致"哪个 Agent 做的"成为审计问题

---

> **ADR-004 决策完成**
>
> Brain = Identity / State / Intelligence（含 Planning）。
>
> Execution = Action / Runtime（无状态，可替换）。
>
> Execution 禁止持久化 Brain 状态。Brain 禁止直接调用 Tool。
>
> Agent 是 Execution 的无状态子组件。
