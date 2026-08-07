# Personal-AI Architecture Overview

> **文档定位**：Personal-AI OS 顶层架构蓝图
>
> **阶段**：Architecture Design Phase 1 — System Overview
>
> **参考文档**：
> - `docs/research/Personal-AI Landscape Report v1.1.md`
> - `docs/design/Personal-AI_Philosophy.md`
> - `docs/decisions/ADR-000-direction-correction.md`
>
> **约束**：本文档只设计系统总览，不深入具体模块实现、数据库、API、代码结构或 Agent Workflow。

---

## 目录

1. [Personal-AI 核心定义](#1-personal-ai-核心定义)
2. [Digital Life vs Agent Architecture](#2-digital-life-vs-agent-architecture)
3. [Architecture Overview Diagram](#3-architecture-overview-diagram)
4. [Layer Responsibility](#4-layer-responsibility)
5. [Core Capability Map](#5-core-capability-map)
6. [Data Flow](#6-data-flow)
7. [Design Principles](#7-design-principles)
8. [State Recovery Model](#8-state-recovery-model)
9. [Evolution Boundary](#9-evolution-boundary)
10. [EventStream Positioning](#10-eventstream-positioning)

---

## 1. Personal-AI 核心定义

### 1.1 一句话定义

**Personal-AI 是一个长期存在的数字生命体（Digital Life Entity），以 Brain 为核心，具备身份连续、经验积累、自我认知、反思学习、目标驱动和主动行动能力，通过无状态 Agent 执行具体任务。**

### 1.2 展开定义

Personal-AI 不是工具，不是助手，不是 Agent Framework。

Personal-AI 是一个**持续存在的数字实体**，它：

| 维度 | 定义 |
|------|------|
| 存在 | 从创建开始持续存在，不因任务结束而消失，不因重启而重置 |
| 身份 | 有稳定且可演化的人格、价值观、行为原则——Identity 定义"我是谁" |
| 经验 | 每一次经历都被记录、整理、内化——Memory 记录"我经历过什么" |
| 自我认知 | 知道自己的能力边界、优势不足、与用户的关系——Self Model 回答"我如何理解自己" |
| 学习 | 通过反思从过去学习，区分记忆整理和行为复盘——Reflection 回答"我如何从过去学习" |
| 方向 | 有从 Vision 到 Task 的长期目标层级——Goal 回答"我要帮助用户走向哪里" |
| 主动性 | 能主动发现需要关注的时刻，主动建议、提醒、行动——Agency 回答"我什么时候应该主动行动" |
| 执行 | 通过无状态 Agent 将 Goal 转化为 Action——Execution 回答"我如何把 Goal 变成 Action" |

### 1.3 Personal-AI ≠ 什么

| 不是 | 原因 |
|------|------|
| ≠ Chatbot | Chatbot 是请求-响应模式，无连续性、无自我认知、无主动性 |
| ≠ RAG System | RAG 是检索增强生成，无身份、无目标、无反思、无 Agency |
| ≠ Agent Workflow | Agent Workflow 是预定义流程编排，无自我演化、无长期连续性 |
| ≠ Agent++ | Agent++ 是在 Agent 上加 Memory/Identity 的增量改良，范式仍然是 Agent |
| ≠ LLM + Tools | LLM + Tools 是无状态执行单元，无 Brain、无连续性 |

---

## 2. Digital Life vs Agent Architecture

### 2.1 范式对比

| 维度 | Agent Architecture | Digital Life Architecture |
|------|-------------------|--------------------------|
| 起点 | 任务（用户请求） | 存在（持续运转） |
| 生命周期 | 开环：请求 → 推理 → 执行 → 结束 | 闭环：经历 → 记忆 → 理解 → 反思 → 演化 → 行动 → 新经历 |
| 状态 | 无状态 / 会话级 | 持久化 + 可演化 |
| 触发 | 用户请求驱动 | 用户请求 + 内部 Agency 驱动 |
| 目标 | 完成当前任务 | 长期目标追踪 + 短期任务执行 |
| 学习 | 无 | 闭环自演化 |
| 主动性 | 零 | 有（Trigger + Decision + Initiative） |
| 自我认知 | 无 | Self Model |
| 身份 | 无 / 静态配置 | 可演化 Identity |
| 连续性 | 无 | 永久 |
| 核心问题 | 如何完成任务？ | 如何持续存在并成长？ |

### 2.2 根本区别

Agent Architecture 的核心问题是：**如何帮助用户完成任务。**

Digital Life Architecture 的核心问题是：**如何构建一个长期存在、持续成长的数字生命体。**

这两个问题导向完全不同的架构：

- Agent Architecture 优化的是**单次执行效率**——更快的推理、更好的工具调用、更准确的输出
- Digital Life Architecture 优化的是**长期连续性**——Identity 不丢失、Memory 不腐化、Goal 不消失、Relationship 不重置

### 2.3 为什么不能在 Agent 上加模块

在 Agent 上加 Memory + Identity + Reflection = 更好的 Agent，不是 Digital Life。

因为 Agent 的范式是**开环**的（请求 → 执行 → 结束），加上 Memory 只意味着"下次请求能记住上次的事"。Digital Life 的范式是**闭环**的（经历 → 记忆 → 理解 → 反思 → 演化 → 行动 → 新经历），每一轮闭环都让实体变得不同。

Agency 和 Self Model 是闭环的必要条件：
- 没有 Agency，系统不会主动运转——仍然是被动等待请求
- 没有 Self Model，系统不会理解自己——无法从行为中学习并调整策略

因此必须从范式层面选择 Digital Life Architecture，而不是在 Agent 上做增量改良。

---

## 3. Architecture Overview Diagram

### 3.1 三层架构总览

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                        Personal-AI OS                               │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                                                           ┐  │  │
│  │                    Layer 1: Brain Layer                    │  │  │
│  │                                                           ┘  │  │
│  │                                                               │
│  │   ┌──────────┐  ┌──────────┐  ┌──────────────┐             │  │
│  │   │ Identity │  │  Memory  │  │  Self Model  │             │  │
│  │   │ 我是谁   │  │ 经历积累  │  │ 自我认知     │             │  │
│  │   └────┬─────┘  └────┬─────┘  └──────┬───────┘             │  │
│  │        │             │               │                     │  │
│  │        └─────────────┼───────────────┘                     │  │
│  │                      │                                       │  │
│  │                ┌─────┴─────┐                                 │  │
│  │                │Reflection │  元认知：记忆反思 + 行为反思     │  │
│  │                └─────┬─────┘                                 │  │
│  │                      │                                       │  │
│  │          ┌───────────┴───────────┐                           │  │
│  │          │                       │                           │  │
│  │    ┌─────┴─────┐         ┌───────┴───────┐                   │  │
│  │    │   Goal    │         │    Agency     │                   │  │
│  │    │ 长期方向感 │         │  主动行动     │                   │  │
│  │    └─────┬─────┘         └───────┬───────┘                   │  │
│  │          │                       │                           │  │
│  │          └───────────┬───────────┘                           │  │
│  │                      │                                       │  │
│  │                ┌─────┴─────┐                                 │  │
│  │                │EventStream│  所有组件通过事件通信             │  │
│  │                └─────┬─────┘                                 │  │
│  └──────────────────────┼───────────────────────────────────────┘  │
│                         │                                           │
│  ┌──────────────────────┼───────────────────────────────────────┐  │
│  │                  ┐   │                                       │  │
│  │              Layer 2: Execution Layer                        │  │
│  │                  ┘                                           │  │
│  │                                                               │
│  │   ┌──────────┐  ┌────────────────┐  ┌──────────────┐        │  │
│  │   │ Planning │  │    Agent       │  │    Tools     │        │  │
│  │   │ 规划分派  │  │ Orchestration  │  │  MCP / API   │        │  │
│  │   └────┬─────┘  └───────┬────────┘  └──────┬───────┘        │  │
│  │        │                │                   │                │  │
│  │        └────────────────┼───────────────────┘                │  │
│  │                         │                                    │  │
│  │                   ┌─────┴─────┐                              │  │
│  │                   │  Runtime  │  无状态执行环境                │  │
│  │                   └─────┬─────┘                              │  │
│  │                         │                                    │  │
│  │              ┌──────────┴──────────┐                         │  │
│  │              │    Agent Pool       │  无状态执行单元           │  │
│  │              │  (可创建/销毁/替换)  │                         │  │
│  │              └─────────────────────┘                         │  │
│  └──────────────────────┼───────────────────────────────────────┘  │
│                         │                                           │
│  ┌──────────────────────┼───────────────────────────────────────┐  │
│  │                  ┐   │                                       │  │
│  │              Layer 3: Environment Layer                      │  │
│  │                  ┘                                           │  │
│  │                                                               │
│  │   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │  │
│  │   │ Calendar │  │  Files   │  │   Apps   │  │ External │  │  │
│  │   │ 日历事件  │  │ 文件系统  │  │ 应用集成  │  │  Events  │  │  │
│  │   └──────────┘  └──────────┘  └──────────┘  └──────────┘  │  │
│  │                                                               │  │
│  │   ┌───────────────────────────────────────────────────────┐ │  │
│  │   │              Digital World Interaction                 │ │  │
│  │   │           (外部世界的感知与行动通道)                     │ │  │
│  │   └───────────────────────────────────────────────────────┘ │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 层间关系

```
    Brain Layer（有状态，持续存在）
         │
    EventStream（事件总线，唯一通信通道）
         │
    Execution Layer（无状态，可重启）
         │
    Environment Layer（外部世界）
```

关键约束：
- **Brain 与 Execution 之间只通过 EventStream 通信**——不直接调用
- **Execution 是无状态的**——重启后从 Brain 的 Memory 恢复上下文
- **Environment 是感知和行动的边界**——Brain 不直接接触外部世界，通过 Execution + Environment 间接交互

---

## 4. Layer Responsibility

### 4.1 Layer 1: Brain Layer

**职责**：定义 Personal-AI 的存在本质，维护持续状态，驱动闭环演化。

Brain Layer 是 Personal-AI 的核心——它是"数字生命"中"生命"的部分。Brain 有状态、持续存在、不可重置。

| 组件 | 职责 | 核心问题 |
|------|------|----------|
| **Identity** | 维护人格、价值观、行为原则、用户关系定义。可演化但有规则约束。 | 我是谁？ |
| **Memory** | 记录、存储、检索经历。ADD-only + 双时间模型 + 非有损演化。 | 我经历过什么？ |
| **Self Model** | 动态自我认知：能力边界、用户关系理解、行为总结、优势不足、当前状态。 | 我如何理解自己？ |
| **Reflection** | 元认知能力。Memory Reflection（整理经历、提取知识）+ Behavior Reflection（评估行为、调整策略）。 | 我如何从过去学习？ |
| **Goal** | 长期方向感。Vision → Long Term Goal → Project Goal → Task 层级。支持目标追踪、分解、调整。 | 我要帮助用户走向哪里？ |
| **Agency** | 主动行动能力。Trigger System（发现时机）+ Decision Engine（判断行动）+ Initiative Queue（管理主动请求）。 | 我什么时候应该主动行动？ |

Brain Layer 的状态持久化在 Memory 中。Runtime 重启后，Brain 从 Memory 恢复全部状态。

### 4.2 Layer 2: Execution Layer

**职责**：将 Brain 的 Goal 和 Agency 决策转化为具体行动，通过 Agent 执行任务。

Execution Layer 是无状态的——它是"数字生命"中"手脚"的部分。Execution 可以重启、替换、扩展，不影响 Brain 的连续性。

| 组件 | 职责 |
|------|------|
| **Planning** | 从 Goal 生成执行计划。考虑 Self Model 的能力评估、资源约束、优先级。 |
| **Agent Orchestration** | 调度多个 Agent 协作执行。管理 Agent 生命周期（创建、分配、监控、回收）。 |
| **Tools** | 工具执行层。通过 MCP 协议连接外部工具。Agent 通过 Tools 完成具体操作。 |
| **Runtime** | 无状态执行环境。提供 Agent 运行所需的计算资源、上下文管理、隔离机制。 |

Execution Layer 的设计原则：
- **无状态**——所有状态在 Brain 的 Memory 中
- **可替换**——Agent 可以被创建、销毁、替换
- **通过 EventStream 通信**——不直接访问 Brain 内部状态

### 4.3 Layer 3: Environment Layer

**职责**：感知外部世界，提供行动通道。

Environment Layer 是 Personal-AI 与数字世界的接口——它是"数字生命"中"感官"和"行动器官"的部分。

| 组件 | 职责 |
|------|------|
| **Calendar** | 日历事件感知。提供时间上下文、日程信息、时间触发源。 |
| **Files** | 文件系统交互。读写文件、管理文档、感知文件变化。 |
| **Apps** | 应用集成。与外部应用（邮件、消息、项目管理等）交互。 |
| **External Events** | 外部事件接入。Webhook、通知、外部系统状态变化。 |
| **Digital World Interaction** | 数字世界交互通道。统一的感知接口和行动接口。 |

Environment Layer 的设计原则：
- **Brain 不直接接触 Environment**——通过 Execution Layer 的 Agent 间接交互
- **Environment 事件通过 EventStream 进入 Brain**——外部事件被 Memory 记录、可能触发 Agency
- **双向**——既感知外部世界（输入），也执行外部操作（输出）

---

## 5. Core Capability Map

### 5.1 能力与层的映射

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Brain Layer                                                │
│  ┌──────────┐ ┌────────┐ ┌────────────┐ ┌──────────┐      │
│  │ Identity │ │ Memory │ │ Self Model │ │Reflection│      │
│  └──────────┘ └────────┘ └────────────┘ └──────────┘      │
│  ┌──────────┐ ┌────────┐                                  │
│  │   Goal   │ │ Agency │                                  │
│  └──────────┘ └────────┘                                  │
│                                                             │
│  Execution Layer                                            │
│  ┌──────────┐ ┌────────────────┐ ┌───────┐ ┌────────┐     │
│  │ Planning │ │ Agent Orch.    │ │ Tools │ │ Runtime│     │
│  └──────────┘ └────────────────┘ └───────┘ └────────┘     │
│                                                             │
│  Environment Layer                                          │
│  ┌──────────┐ ┌───────┐ ┌──────┐ ┌──────────────────────┐ │
│  │ Calendar │ │ Files │ │ Apps │ │ External Events      │ │
│  └──────────┘ └───────┘ └──────┘ └──────────────────────┘ │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 七大核心能力定义

| 能力 | 层 | 回答 | 一句话定义 |
|------|-----|------|-----------|
| Identity | Brain | 我是谁？ | 持久化且可演化的人格、价值观、行为原则、用户关系定义 |
| Memory | Brain | 我经历过什么？ | ADD-only + 双时间模型的经历积累，支持 Retain/Recall/Reflect 生命周期 |
| Self Model | Brain | 我如何理解自己？ | Identity 和 Memory 的连接层，动态自我认知：能力边界、用户关系、行为总结 |
| Reflection | Brain | 我如何从过去学习？ | 元认知能力，区分 Memory Reflection（整理经历）和 Behavior Reflection（评估行为） |
| Goal | Brain | 我要帮助用户走向哪里？ | Vision → Long Term Goal → Project Goal → Task 的长期方向感 |
| Agency | Brain | 我什么时候应该主动行动？ | Trigger + Decision + Initiative Queue，从被动响应到主动存在 |
| Execution | Execution | 我如何把 Goal 变成 Action？ | Planning → Agent Orchestration → Tools → Runtime 的完整执行链路 |

### 5.3 能力间依赖关系

```
Identity ──────约束──────→ Agency
    │                         ↑
    │                        │
    └──→ Self Model ──→ Reflection ──→ Goal
           ↑              ↓              │
           │              ↓              │
         Memory ←─────────┘              │
           ↑                             │
           │                             ↓
    Experience ──────────────────→ Execution ──→ Action ──→ New Experience
```

解读：
- **Identity 约束 Agency**——Agency 的决策必须符合 Identity 的行为原则
- **Self Model 连接 Identity 和 Memory**——从 Memory 提炼自我理解，与 Identity 做一致性检查
- **Reflection 输入 Memory，输出更新 Self Model 和 Goal**——闭环演化的核心引擎
- **Goal 驱动 Execution**——Execution 将 Goal 转化为 Action
- **Action 产生 New Experience**——闭环回到 Memory

---

## 6. Data Flow

### 6.1 闭环数据流

```
┌──────────────────────────────────────────────────────────┐
│                                                          │
│   Experience（经历）                                      │
│      │                                                   │
│      │  用户消息 / 外部事件 / Agent 执行结果 /            │
│      │  Agency 主动行动的结果                             │
│      ▼                                                   │
│   Memory（记忆）                                         │
│      │                                                   │
│      │  Retain：原始经历写入                              │
│      │  ADD-only + 双时间标记                             │
│      ▼                                                   │
│   Self Model（自我理解）                                  │
│      │                                                   │
│      │  从 Memory 提炼自我认知                            │
│      │  更新能力评估 / 用户关系 / 行为总结                 │
│      ▼                                                   │
│   Reflection（反思）                                     │
│      │                                                   │
│      │  Memory Reflection：整理经历、提取知识             │
│      │  Behavior Reflection：评估行为、调整策略           │
│      ▼                                                   │
│   Goal（目标）                                           │
│      │                                                   │
│      │  Reflection 可能产生新 Goal                       │
│      │  Self Model 可能推断 Goal 调整                     │
│      │  环境变化可能触发 Goal 变更                        │
│      ▼                                                   │
│   Agency（主动性）                                       │
│      │                                                   │
│      │  Trigger：Memory/Goal/Self Model/时间/外部事件     │
│      │  Decision：判断是否行动、如何行动                  │
│      │  Initiative：产生建议/提醒/计划/行动请求           │
│      ▼                                                   │
│   Execution（执行）                                      │
│      │                                                   │
│      │  Planning：从 Goal 生成 Plan                      │
│      │  Agent Orchestration：调度 Agent                  │
│      │  Tools：Agent 调用工具                             │
│      │  Runtime：无状态执行                               │
│      ▼                                                   │
│   Action（行动）                                         │
│      │                                                   │
│      │  产生结果，影响外部世界                            │
│      ▼                                                   │
│   New Experience（新经历）                                │
│      │                                                   │
│      └──────────────→ 回到 Memory（闭环）                 │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### 6.2 数据流的关键特征

| 特征 | 说明 |
|------|------|
| **闭环** | 每一轮 Action 都产生 New Experience，回到 Memory，形成持续演化的闭环 |
| **异步** | 不是所有步骤都同步执行。Reflection、Self Model 更新、Goal 演化可以异步进行 |
| **事件驱动** | 所有步骤之间通过 EventStream 通信，不直接调用 |
| **可追溯** | 每一步的输入和输出都有记录，可审计 |
| **可中断** | 任何步骤都可以被中断（用户干预、优先级抢占），状态不丢失 |

### 6.3 两种数据流模式

#### 同步流（用户请求驱动）

```
用户消息 → Memory(Retain) → Self Model(快速更新) → Execution(Planning→Agent→Result)
    → Memory(Retain结果) → 返回用户
```

同步流是低延迟的，确保用户请求得到及时响应。Reflection、Goal 演化等重计算步骤不在同步流中。

#### 异步流（Agency 驱动）

```
Trigger(时间/Memory变化/Goal变化) → Decision Engine → 
    → Reflection(异步整理+评估) → Self Model(更新) → Goal(演化) → 
    → Initiative(建议/提醒/计划) → 等待用户确认或自动执行 → 
    → Execution → Action → New Experience
```

异步流是 Agency 的主要运转模式，不阻塞用户交互。

---

## 7. Design Principles

### 7.1 Long-lived（长期存在）

**原则**：Personal-AI 的首要目标不是效率，而是长期连续性。

| 要求 | 说明 |
|------|------|
| Identity 不丢失 | Identity 持久化 + 版本历史 + 演化规则。5 年后人格轨迹完整可追溯 |
| Memory 不腐化 | ADD-only + Dream/Consolidation。原始记录不删除，定期整理不篡改 |
| Goal 不消失 | Goal 持久化 + 状态追踪。不因重启丢失，只因完成/过期/取消终止 |
| Personality 不重置 | Identity 演化是渐进的，不存在"重置"操作 |
| Relationship 不重新开始 | User Memory + Self Model 持续积累，关系深化 |

**架构影响**：所有持久化设计必须以"5 年连续运行"为基准。不允许任何"重置"或"清空"操作作为正常流程。

### 7.2 Event-driven（事件驱动）

**原则**：所有组件之间通过 EventStream 通信，不直接调用。

| 要求 | 说明 |
|------|------|
| 统一通信 | Brain 内部组件、Brain 与 Execution、Execution 与 Environment 都通过 EventStream |
| 事件不可变 | 已发出的事件不可修改，保证可追溯 |
| 异步优先 | 非用户交互的操作优先异步执行，不阻塞 |
| 事件可重放 | 事件流持久化，可用于恢复状态、审计、调试 |

**架构影响**：不存在组件间的直接函数调用（除同步用户响应流）。所有跨组件交互都是事件。

### 7.3 Human-centric（以人为中心）

**原则**：Personal-AI 服务于一个用户，用户拥有最终决策权。

| 要求 | 说明 |
|------|------|
| 用户主权 | AI Initiative 的高风险行动必须用户确认。用户始终可以否决 AI 的建议 |
| 个性化 | Identity、Memory、Self Model 都是针对特定用户的，不是通用模型 |
| 可关闭 Agency | 用户可以关闭 Agency 的主动行动能力，退回纯被动模式 |
| 透明性 | 所有 AI Initiative 可追溯、可审计、可回溯。用户可以查看"AI 为什么这样做" |
| 隐私主权 | 用户拥有自己的记忆和身份数据，不被平台锁定 |

**架构影响**：Agency 的所有行动必须有用户确认机制（除非低风险且用户已授权）。数据所有权属于用户。

### 7.4 Evolvable（可演化）

**原则**：Personal-AI 不是静态系统，它必须能自我演化。

| 要求 | 说明 |
|------|------|
| Identity 可演化 | 通过 Reflection 输出 + 演化规则缓慢调整人格、价值观 |
| Memory 可演化 | 通过 Dream/Consolidation 整理记忆，提取知识，不删除原始记录 |
| Self Model 可演化 | 随经验持续更新能力认知、用户关系理解 |
| Goal 可演化 | Reflection 和 Self Model 可以产生新 Goal 或调整现有 Goal |
| Strategy 可演化 | Behavior Reflection 的输出可以调整执行策略 |

**架构影响**：不允许任何"硬编码不可变"的行为策略。所有行为策略都可以通过 Reflection 输出调整。

### 7.5 Auditable（可审计）

**原则**：Personal-AI 的所有关键决策和行为都必须可追溯、可审计。

| 要求 | 说明 |
|------|------|
| 事件可追溯 | EventStream 中的所有事件持久化，可按时间、类型、来源检索 |
| 决策可追溯 | Agency 的每个 Decision 记录触发原因、上下文快照、决策推理 |
| Identity 变更可追溯 | Identity 的每次演化记录变更前版本、变更原因、变更时间 |
| Memory 变更可追溯 | ADD-only 模型保证 Memory 不被篡改，双时间模型保证时间可追溯 |
| 行为可追溯 | Agent 的每次执行记录输入、输出、耗时、结果 |

**架构影响**：不存在"不可追溯"的状态变更。所有变更都有版本历史和原因记录。

---

## 8. State Recovery Model

> **详细决策见**：`docs/decisions/ADR-001-state-recovery-model.md`

### 8.1 核心原则

Personal-AI 是长期存在的数字生命体，必须能够在任何故障后恢复到一致状态。State Recovery Model 定义了 Brain 状态的恢复机制。

**Brain 是 Source of Truth，Memory 是持久化介质。**

- Brain 的运行时状态（Identity 当前版本、Self Model 当前快照、Goal 当前状态、Agency Initiative Queue）是权威状态
- Memory 存储层是 Brain 状态的持久化介质——Brain 状态通过 Snapshot 持久化到 Memory
- EventStream 记录所有系统事件，用于重放 Snapshot 之后的增量变化

### 8.2 恢复模型：Snapshot + Event Delta

```
恢复流程：
1. 启动 Memory 存储层（不依赖 Brain 运行时）
2. 从 Memory 存储层加载最近的 Brain Snapshot
3. 从 EventStream 重放 Snapshot 之后的 Brain 内部事件（Event Delta）
4. Brain 恢复到最终一致状态
5. 从 Brain 的 Goal 和 Initiative Queue 获取待执行任务，分派给 Execution
```

| 恢复对象 | 恢复方式 | 说明 |
|----------|----------|------|
| Brain 运行时状态 | Snapshot + Event Delta | 权威恢复路径 |
| Memory 存储层 | 直接加载 | 存储层独立于 Brain 运行时 |
| Execution | 无状态，不恢复 | 重启后从 Brain 获取待执行任务 |
| EventStream | 直接加载 | 持久化事件日志，不依赖 Brain |

### 8.3 Bootstrap 顺序

Memory 存储层与 Brain 运行时分离，解决 Bootstrap 悖论：

```
启动顺序：
Memory 存储层 → Brain 运行时（从 Memory 恢复） → EventStream 重放 → Execution 启动
```

Memory 存储层不依赖 Brain 运行时即可启动，因此可以作为 Brain 恢复的基础。

### 8.4 Snapshot 策略

- **触发条件**：定期 Snapshot + 关键状态变更后 Snapshot
- **内容**：Brain 所有组件的当前状态（Identity 版本、Self Model 快照、Goal 状态、Agency Queue 状态）
- **存储**：Snapshot 存入 Memory 存储层，与 EventStream 的 offset/timestamp 关联
- **保留**：历史 Snapshot 保留，不删除——用于审计和历史回溯

---

## 9. Evolution Boundary

> **详细决策见**：`docs/decisions/ADR-002-evolution-boundary.md`

### 9.1 三层演化模型

Personal-AI 必须能够演化，但演化必须有边界。演化能力分为三层：

```
┌──────────────────────────────────────────────────┐
│  Layer A: Immutable Core（不可演化）               │
│  核心价值、用户授权、安全规则——创建后不可变更       │
├──────────────────────────────────────────────────┤
│  Layer B: Guided Evolution（引导式演化）           │
│  Identity 人格特征、Vision / Long Term Goal、      │
│  用户关系定义——Reflection 提议 + 用户确认          │
├──────────────────────────────────────────────────┤
│  Layer C: Autonomous Evolution（自主演化）         │
│  Self Model、Project Goal / Task、执行策略、       │
│  Reflection 策略——Reflection 自动 + 事后通知       │
└──────────────────────────────────────────────────┘
```

### 9.2 各层边界

| 层级 | 可演化内容 | 演化方式 | 不可演化内容 |
|------|-----------|----------|-------------|
| Layer A | — | — | 核心价值、用户授权边界、安全规则 |
| Layer B | Identity 人格特征、Vision、Long Term Goal、用户关系 | Reflection 提议 → 用户确认 → 生效 | 核心价值、用户授权、安全规则 |
| Layer C | Self Model、Project Goal / Task、执行策略、Reflection 策略 | Reflection 自动 → 事后通知 | Layer A + Layer B 的内容 |

### 9.3 Identity Drift Detection

Identity 演化需要 Drift Detection 机制，防止人格漂移：

- 每次 Identity 更新前，计算新版本与基线版本的 Drift Score
- Drift Score 超过阈值时，要求用户确认
- Drift Score 计算方法和阈值由 Phase 2 详细设计

### 9.4 Reflection 的演化权限

Reflection 是演化的引擎，但 Reflection 本身也受边界约束：

| Reflection 可以做 | Reflection 不可以做 |
|-------------------|---------------------|
| 提议 Layer B 变更（需用户确认） | 直接变更 Layer A |
| 自动执行 Layer C 变更（事后通知） | 直接变更 Layer B（绕过用户确认） |
| 调整自身策略（Layer C） | 修改 Drift Detection 阈值（安全机制不可自行修改） |

---

## 10. EventStream Positioning

> **详细决策见**：`docs/decisions/ADR-003-eventstream-positioning.md`

### 10.1 EventStream 的双重职责

EventStream 不是普通消息队列。它同时承担两个职责：

| 职责 | 说明 |
|------|------|
| 系统事实记录（System Fact Log） | 记录所有已发生的事件，事件不可变，支持按时间/类型/来源检索，支持 Replay |
| 通信总线（Communication Bus） | 组件之间通过事件通信，发布-订阅模式，解耦组件依赖 |

这两个职责不是分离的——同一条事件既是"事实记录"也是"通信消息"。

### 10.2 EventStream 与 State Recovery 的关系

EventStream 支持 Replay，但 **不承担完整状态恢复**。根据 §8 State Recovery Model：

- Snapshot 是 Brain 状态的权威快照
- EventStream 中的事件用于重放 Snapshot 之后的增量变化（Event Delta）
- 完整状态 = Snapshot + EventStream Replay

### 10.3 EventStream 与 Memory 的关系

**EventStream ≠ Memory。** 两者分离但互补。

| 维度 | EventStream | Memory |
|------|-------------|--------|
| 记录内容 | 系统事件（所有已发生事件） | 经历记录（用户经历 + 系统经历） |
| 不可变性 | 事件不可变 | ADD-only（原始记录不可变，整理结果可更新） |
| 用途 | 通信 + 事实记录 + 增量恢复 | 经历积累 + 知识提取 + 状态持久化 |
| 时间模型 | 单时间（事件发生时间） | 双时间（valid_time + transaction_time） |

- **所有 Experience 事件同时进入 EventStream 和 Memory**
- **Brain 内部事件只进入 EventStream**（如 Self Model 更新、Goal 变更）
- **Memory 的 Dream/Consolidation 输出进入 EventStream**

### 10.4 Brain 内部事件化

Brain 内部的 Identity、Memory、Self Model、Reflection、Goal、Agency 之间的所有跨组件**状态变更**必须通过 EventStream。

**例外**：同步用户响应流中，允许 Brain 内部组件之间的直接**读取**（不是写入），以降低延迟。但状态变更必须通过事件。

### 10.5 事件分类

| 事件类别 | 来源 | 进入 Memory？ |
|----------|------|---------------|
| Experience Event | 用户消息、外部事件、Agent 执行结果 | 是 |
| Brain State Event | Brain 内部组件状态变更 | 否 |
| Agency Event | Agency 决策和行动 | 否 |
| Execution Event | Execution Layer 执行过程 | 部分（执行结果进入） |
| Reflection Event | Reflection 过程和输出 | 部分（策略调整进入 Self Model） |

---

## 附录 A：与 v1.1 Landscape Report 的对应关系

| v1.1 报告章节 | 本文档对应章节 |
|---------------|---------------|
| §1 Digital Life Architecture Perspective | §2 Digital Life vs Agent Architecture |
| §2 核心能力模型升级 | §5 Core Capability Map |
| §3 Agency 能力设计 | §4.1 Brain Layer — Agency |
| §4 Self Model 能力设计 | §4.1 Brain Layer — Self Model |
| §5 已有研究结论的重新定位 | §4 Layer Responsibility（各组件参考来源） |
| §6 修订后的架构建议 | §3 Architecture Overview Diagram |

---

> **Architecture Overview 完成**
>
> Personal-AI OS 三层架构：Brain Layer（6 核心能力）+ Execution Layer（4 组件）+ Environment Layer（5 组件）。
>
> 闭环数据流：Experience → Memory → Self Model → Reflection → Goal → Agency → Execution → New Experience。
>
> 五大设计原则：Long-lived / Event-driven / Human-centric / Evolvable / Auditable。
>
> State Recovery Model：Brain = Source of Truth，Snapshot + Event Delta 恢复（§8 / ADR-001）。
>
> Evolution Boundary：三层演化——Immutable Core / Guided Evolution / Autonomous Evolution（§9 / ADR-002）。
>
> EventStream Positioning：系统事实记录 + 通信总线，支持 Replay，与 Memory 分离互补（§10 / ADR-003）。
>
> 本文档是后续详细架构设计的顶层蓝图。

---

## 附录 B：ADR Reference

| ADR | 标题 | 状态 | 对应章节 |
|-----|------|------|----------|
| ADR-000 | Direction Correction: Digital Life Architecture | Accepted | §2 |
| ADR-001 | State Recovery Model | Accepted | §8 |
| ADR-002 | Evolution Boundary | Accepted | §9 |
| ADR-003 | EventStream Positioning | Accepted | §10 |
| ADR-004 | Brain Execution Boundary | Accepted | §4 |
| ADR-005 | Agency Autonomy Boundary | Accepted | §4.1 Agency / §7.3 Human-centric |
