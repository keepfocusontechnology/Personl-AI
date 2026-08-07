# ADR-000: Architecture Direction Correction

> **Status**: Accepted
>
> **Date**: 2026-08-07
>
> **Supersedes**: None (this is the foundational ADR)
>
> **Related Documents**:
> - `docs/research/Landscape-Report.md` (v1.0, original research)
> - `docs/research/Personal-AI Landscape Report v1.1.md` (revised research)
> - `docs/design/Personal-AI_Philosophy.md` (philosophy definition)

---

## Context

### 之前架构方向

Personal-AI 项目完成了 Landscape Research（v1.0），调研了 12 个项目，形成了以下架构方向：

```
Personal-AI = OpenHands(无状态Runtime+EventStream)
            + Mem0(ADD-only+Dream+多信号检索)
            + Graphiti(双时间模型+非有损演化)
            + Hindsight(Retain/Recall/Reflect)
            + SOUL.md(可演化Identity)
            + LangGraph(StateGraph+Checkpointer)
            + 自行设计(Goal + 行为Reflection + 长期Planning)
```

核心能力模型为 5 项：

```
Memory / Identity / Reflection / Goal / Planning / Runtime
```

这个方向的研究结论是有效的——12 个项目的调研结论仍然有价值。但这个方向的**范式定位**存在问题：它仍然在"Agent + Memory"范式内，没有跳出到"Digital Life"范式。

---

## Problem

### 缺少 Self Model

v1.0 的模型有 Identity（"我是谁"）和 Memory（"发生了什么"），但缺少两者之间的连接层。

没有 Self Model，Personal-AI 无法：
- 认知自己的能力边界
- 理解与用户的关系深度
- 总结自己的行为模式
- 评估自己的优势和不足

这导致 Personal-AI 的自我认知是空白的——它知道自己是谁（Identity），记得发生了什么（Memory），但不理解自己当前的状态和能力。

### 缺少 Agency

v1.0 的模型是纯被动的——用户请求 → Personal-AI 响应。没有"Personal-AI 什么时候应该主动行动"的能力。

没有 Agency，Personal-AI 无法：
- 主动追踪长期目标进度
- 主动反思经验
- 主动维护用户关系
- 主动响应环境变化

这导致 Personal-AI 退化为"有记忆的 Agent"——比普通 Agent 好一点，但不是 Digital Life。

### 缺少 Long-term Continuity 设计

v1.0 的模型关注"如何构建"，但没有充分关注"如何长期存在"。

缺少对以下问题的系统性回答：
- 5 年后 Memory 如何不腐化？
- 5 年后 Identity 如何保持连续？
- 5 年后 Goal 如何不消失？
- 5 年后 Personality 如何不重置？
- 5 年后 Relationship 如何不重新开始？

### 范式偏移的后果

如果沿用 v1.0 方向，Personal-AI 会退化为：

```
长期记忆 Agent = Agent + Memory + Identity + Reflection
```

这是一个更好的 Agent，但不是 Digital Life。它缺少自我认知（Self Model）和主动性（Agency），无法形成闭环自演化。

---

## Decision

### 确定：Personal-AI 采用 Digital Life Architecture

Personal-AI 的架构范式从 **Agent Architecture** 调整为 **Digital Life Architecture**。

### 核心能力模型升级

从 5 项升级为 7 项：

```
v1.0: Memory / Identity / Reflection / Goal / Planning / Runtime

v1.1: Identity / Memory / Self Model / Reflection / Goal / Agency / Execution
```

变更：
1. **新增 Self Model** — Identity 和 Memory 的连接层，回答"我如何理解自己"
2. **新增 Agency** — 从被动响应到主动存在，回答"我什么时候应该主动行动"
3. **Planning → Execution** — Planning 降级为 Execution 的子阶段
4. **Runtime 移除** — Runtime 从核心能力降级为基础设施

### 生命周期升级

从开环升级为闭环：

```
v1.0: 用户请求 → 推理 → 执行 → 结束（开环）

v1.1: 经历 → 记忆 → 自我理解 → 反思 → 目标演化 → 主动性 → 行动 → 新经历（闭环）
```

### 已有研究的定位调整

v1.0 的 12 个项目调研结论从"Personal-AI 的定义"降级为"Personal-AI 能力组件的设计输入"。

| 项目 | 新定位 |
|------|--------|
| OpenHands | Execution 层参考 |
| Mem0 | Memory 层参考 |
| Graphiti | Memory 层参考 |
| Hindsight | Memory 层 + Memory Reflection 参考 |
| SOUL.md | Identity 层 + Self Model 参考 |
| LangGraph | Execution 层参考 |
| 其他 | 各能力层的组件级参考 |

---

## Consequence

### 对 Memory Design 的影响

- Memory 必须支持 Self Model 更新（提供行为数据）
- Memory 必须支持 Agency 触发（Memory 变化可触发 Agency）
- Memory 的 Dream/Consolidation 需要同时服务于 Memory Reflection 和 Behavior Reflection

### 对 Identity Design 的影响

- Identity 必须与 Self Model 协调（Self Model 更新时做一致性检查）
- Identity 必须约束 Agency（Agency 的决策必须符合 Identity 的行为原则）
- Identity 的演化规则需要考虑 Agency 和 Self Model 的反馈

### 对 Reflection Design 的影响

- Reflection 必须区分 Memory Reflection 和 Behavior Reflection
- Behavior Reflection 的输出需要更新 Self Model
- Reflection 的输出可以触发 Agency

### 对 Goal Design 的影响

- Goal 不只是用户输入，还可以来自 Reflection 和 Self Model
- Goal 变化可以触发 Agency
- Goal 需要支持长期追踪（多年）

### 对 Planning Design 的影响

- Planning 不再是独立核心能力，而是 Execution 的子阶段
- Planning 的输入不只是 Goal，还包括 Self Model 的能力评估
- Planning 需要考虑 Agency 的 Initiative Queue

### 对 Runtime Design 的影响

- Runtime 从核心能力降级为基础设施
- Runtime 的设计由 Execution 层驱动
- Runtime 保持无状态 + EventStream 架构

### 新增设计领域

| 新领域 | 需要设计的内容 |
|--------|----------------|
| Self Model Design | 能力认知模型、用户关系理解、行为总结机制、Self Model 更新流程 |
| Agency Design | Trigger System、Decision Engine、Initiative Queue、边界控制 |
| Long-term Continuity Design | 5 年 Memory 不腐化策略、Identity 版本演化、Goal 持久化、关系积累机制 |

---

## Rationale

### 为什么不是在 v1.0 基础上加模块

v1.0 的范式是"Agent + Memory"。在这个范式上加 Self Model 和 Agency，相当于在 Agent 基础上加插件——本质还是 Agent。

Digital Life Architecture 不是 Agent 加插件，而是从起点就不同的范式：
- Agent 的起点是"任务"，Digital Life 的起点是"存在"
- Agent 是开环，Digital Life 是闭环
- Agent 是被动的，Digital Life 有 Agency

因此必须做范式调整，而不是增量改良。

### 为什么保留 v1.0 研究

v1.0 的 12 个项目调研结论是有效的技术参考。问题不在研究结论本身，而在如何定位这些结论：

- v1.0 定位：这些项目定义了 Personal-AI → 错误
- v1.1 定位：这些项目是 Personal-AI 能力组件的参考 → 正确

研究结论不变，定位调整。

---

> **ADR-000 决策完成**
>
> Personal-AI 从 Agent Architecture 调整为 Digital Life Architecture。
>
> 核心能力从 5 项升级为 7 项（新增 Self Model + Agency）。
>
> 生命周期从开环升级为闭环。
>
> 已有研究结论保留，定位从"定义"调整为"组件参考"。
