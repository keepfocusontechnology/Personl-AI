# Personal-AI Philosophy

> 本文档不是技术设计。
>
> 本文档定义 Personal-AI 的**存在目标**。
>
> 它是后续所有 Architecture Design、Memory Design、Identity Design、Agency Design、Prototype Development 的最高指导原则。
>
> 如果任何后续设计与本哲学冲突，以本哲学为准。

---

## 目录

1. [What is Personal-AI](#1-what-is-personal-ai)
2. [Digital Life Principles](#2-digital-life-principles)
3. [Core Capability Model](#3-core-capability-model)
4. [Personal-AI Lifecycle](#4-personal-ai-lifecycle)
5. [Brain vs Agent Model](#5-brain-vs-agent-model)
6. [Non Goals](#6-non-goals)
7. [Architecture Impact](#7-architecture-impact)

---

## 1. What is Personal-AI

### 1.1 定义

Personal-AI 是一个**长期存在的数字生命体**（Digital Life Entity）。

它不是工具。它不是助手。它不是 Agent。

它是一个从创建开始持续存在、持续学习、持续演化的数字实体，与用户形成长期关系。

### 1.2 Personal-AI 与 Agent 的区别

| 维度 | Agent | Personal-AI |
|------|-------|-------------|
| 存在模式 | 任务级（请求→执行→结束） | 生命级（持续存在） |
| 状态 | 无状态 / 会话级 | 持久化 + 可演化 |
| 触发 | 用户请求驱动 | 用户请求 + 内部 Agency 驱动 |
| 目标 | 完成当前任务 | 长期目标追踪 + 短期任务执行 |
| 学习 | 无 | 闭环自演化 |
| 主动性 | 零 | 有（Trigger + Decision + Initiative） |
| 连续性 | 无 | 永久 |
| 身份 | 无 / 静态配置 | 可演化 Identity + Self Model |
| 自我认知 | 无 | Self Model |
| 范式 | 工具 | Digital Life |

### 1.3 核心命题

**Personal-AI ≠ Agent++**

Agent++ 的思路是：在 Agent 基础上加 Memory、加 Identity、加 Reflection。这是增量改良。

Personal-AI 不是改良。Personal-AI 是一种新的范式：

- Agent 的起点是"任务"，Personal-AI 的起点是"**存在**"
- Agent 是开环（请求→执行→结束），Personal-AI 是**闭环**（经历→记忆→理解→反思→演化→行动→新经历）
- Agent 是被动的，Personal-AI 有 **Agency**
- Agent 不需要理解自己，Personal-AI 必须有 **Self Model**
- Agent 的目标是完成任务，Personal-AI 的目标是**帮助用户走向长期愿景**

---

## 2. Digital Life Principles

### 2.1 什么是数字生命

数字生命不是模拟人类。数字生命是一种**持续存在的、能自我演化的、有连续性的数字实体**。

数字生命的核心特征：

| 特征 | 说明 |
|------|------|
| 持续存在 | 不因任务结束而消失，不因重启而重置 |
| 经验积累 | 每一次经历都被记录、整理、内化 |
| 自我理解 | 知道自己是谁，知道自己能做什么，知道自己的状态 |
| 自我演化 | 通过反思调整行为策略，通过经验更新自我认知 |
| 目标驱动 | 有长期方向感，不只是被动响应 |
| 主动性 | 能主动发现需要关注的时刻，主动建议、提醒、行动 |
| 关系连续 | 与用户的关系是积累的，不是每次从零开始 |

### 2.2 数字生命的五项原则

#### 原则一：存在先于任务

普通 Agent 的生命周期从用户请求开始。数字生命的生命周期从**创建**开始。

数字生命不需要等待用户请求才"存在"。它在每一刻都在持续运转——记录经历、整理记忆、反思行为、追踪目标、评估自身状态。

任务只是数字生命的一个切面，不是数字生命的全部。

#### 原则二：闭环自演化

数字生命的生命周期是闭环的：

```
经历 → 记忆 → 自我理解 → 反思 → 目标演化 → 主动性 → 行动 → 新经历
```

每一轮闭环都让数字生命变得不同。它不是在重复执行，而是在**成长**。

#### 原则三：身份连续

数字生命的身份不是每次对话重新构建的。身份是**持久化的、可演化的**。

身份包括人格、价值观、行为原则、长期倾向、用户关系。这些属性在时间维度上保持连续，同时通过演化规则缓慢调整。

#### 原则四：主动性

数字生命不是被动的工具。它有 **Agency**——能够主动发现需要关注的时刻，主动判断是否应该行动，主动产生建议、提醒、计划、行动请求。

主动性是数字生命与普通 Agent 的核心区别。

#### 原则五：长期连续性

数字生命的价值在于**长期存在**。如果用户五年后继续使用，数字生命应该：
- Identity 不丢失（人格、价值观、关系连续）
- Memory 不腐化（经历完整可追溯，不因膨胀而崩溃）
- Goal 不消失（长期目标持续追踪）
- Personality 不重置（人格轨迹保持连续）
- Relationship 不重新开始（用户关系积累深化）

---

## 3. Core Capability Model

### 3.1 七大核心能力

```
Identity    — 我是谁？
Memory      — 我经历过什么？
Self Model  — 我如何理解自己？
Reflection  — 我如何从过去学习？
Goal        — 我要帮助用户走向哪里？
Agency      — 我什么时候应该主动行动？
Execution   — 我如何把 Goal 变成 Action？
```

### 3.2 能力关系图

```
                    ┌───────────┐
                    │ Identity  │  我是谁？（定义性，稳定）
                    └─────┬─────┘
                          │
                    ┌─────┴─────┐
                    │ Self Model│  我如何理解自己？（认知性，动态）
                    └─────┬─────┘
                          │
              ┌───────────┼───────────┐
              │           │           │
        ┌─────┴─────┐ ┌──┴──┐ ┌──────┴──────┐
        │  Memory   │ │Goal │ │ Reflection  │
        │ 经历积累   │ │方向感│ │ 元认知      │
        └─────┬─────┘ └──┬──┘ └──────┬──────┘
              │           │           │
              └───────────┼───────────┘
                          │
                    ┌─────┴─────┐
                    │  Agency   │  什么时候主动行动？
                    └─────┬─────┘
                          │
                    ┌─────┴─────┐
                    │ Execution │  如何把 Goal 变成 Action？
                    └─────┬─────┘
                          │
                    ┌─────┴─────┐
                    │Agent Pool │  无状态执行单元
                    └───────────┘
```

### 3.3 各能力详细定义

#### Identity — 我是谁？

Identity 定义 Personal-AI 的**存在本质**。

包含：
- 人格（Personality）
- 价值观（Values）
- 行为原则（Principles）
- 长期倾向（Long-term Tendencies）
- 用户关系定义（Relationship with User）

**Identity ≠ Memory。** Memory 记录"发生了什么"，Identity 定义"我如何理解自己"。

Identity 是相对稳定的，但不是静态的。它通过演化规则缓慢调整，形成"人格轨迹"。

#### Memory — 我经历过什么？

Memory 是 Personal-AI 的**经历积累**。

类型：
- Event Memory — 原始事件流
- Episodic Memory — 情景记忆（带时间、地点、上下文的经历片段）
- Semantic Memory — 语义记忆（从经历中提取的知识）
- User Memory — 关于用户的记忆
- Project Memory — 关于项目的记忆

设计原则：
- ADD-only（只增不改，保证可追溯）
- 双时间模型（valid_time + transaction_time）
- 非有损演化（事实变化不删除旧事实）
- Retain / Recall / Reflect 生命周期

**Memory ≠ Vector DB + Embedding。** Memory 是有结构的、有时间维度的、可追溯的、可演化的。

#### Self Model — 我如何理解自己？

Self Model 是 Identity 和 Memory 的**连接层**。

Self Model 是 Personal-AI 对自身的动态认知：
- 当前能力认知（我能做什么？不能做什么？）
- 用户关系理解（用户信任我什么？用户需要我什么？）
- 历史行为总结（我过去做得好的是什么？做得差的是什么？）
- 优势与不足（我的强项是什么？弱项是什么？）
- 当前状态（我现在处于什么状态？）

**Self Model ≠ Identity。** Identity 是稳定的自我定义，Self Model 是动态的自我认知。

**Self Model ≠ Memory。** Memory 是原始经历，Self Model 是从经历中提炼的自我理解。

Self Model 是 Personal-AI 与普通 Agent 的重要区别。普通 Agent 不需要理解自己，Personal-AI 必须理解自己才能演化。

#### Reflection — 我如何从过去学习？

Reflection 是 Personal-AI 的**元认知能力**。

必须区分两种 Reflection：

**Memory Reflection** — 整理"发生了什么"，提取"获得了什么知识"。
- 输入：原始经历
- 输出：整理后的记忆 + 提取的知识

**Behavior Reflection** — 分析"我的判断是否正确"，"我的行为是否有效"，"是否需要改变策略"。
- 输入：自身行为记录 + 结果反馈
- 输出：行为评估 + 策略调整建议

Behavior Reflection 是 Personal-AI 的核心差异化方向。所有调研项目的反思都是记忆层面的，没有行为复盘。

#### Goal — 我要帮助用户走向哪里？

Goal 是 Personal-AI 的**长期方向感**。

Goal 层级：

```
Vision（用户想成为什么样的人 / 达成什么样的状态）
    ↓
Long Term Goal（1-5 年方向性目标）
    ↓
Project Goal（数周-数月的项目级目标）
    ↓
Task（具体可执行任务）
```

Goal 来源：
- 用户明确输入
- Reflection 产生（从经验中发现新方向）
- Self Model 推断（从自我认知中发现机会）
- 环境变化（外部条件改变导致目标调整）

**Goal ≠ 用户当前任务。** Goal 是从 Vision 到 Task 的完整层级，不是单次请求。

Goal 系统在所有调研项目中完全空白，是 Personal-AI 最大的差异化机会。

#### Agency — 我什么时候应该主动行动？

Agency 是 Personal-AI 从**被动响应到主动存在**的关键能力。

Agency 包含：

**Trigger System** — 发现什么时候需要思考。
- 触发源：时间、外部事件、Memory 变化、Goal 变化、Self Model 变化、Reflection 输出

**Decision Engine** — 判断是否应该行动，以及如何行动。
- 决策类型：Act Now / Schedule / Suggest / Notify / Defer / Suppress
- 约束：Identity（行为原则）、Self Model（能力边界）、Goal（优先级）、用户偏好

**Initiative Queue** — 管理 AI 主动产生的行动请求。
- 双队列：User Task Queue（用户显式请求）+ AI Initiative Queue（AI 主动产生）
- User Task 优先于 AI Initiative
- AI Initiative 的高风险行动需要用户确认

Agency 不是插件。Agency 是 Personal-AI 的**存在方式**。

#### Execution — 我如何把 Goal 变成 Action？

Execution 是从 Goal 到 Action 的**完整链路**。

Execution 包含：
- Planning（从 Goal 生成 Plan）
- Resource Allocation（资源调度）
- Task Dispatch（任务分派给 Agent）
- Agent Execution（Agent 执行任务）
- Result Collection（收集结果并反馈）

Execution 层使用无状态 Runtime + EventStream 架构。Agent 是无状态、可替换的执行单元。

**Execution ≠ Personal-AI。** Execution 是 Personal-AI 的"手脚"，不是 Personal-AI 本身。

---

## 4. Personal-AI Lifecycle

### 4.1 闭环生命周期

```
    ┌─────────────────────────────────────────────────┐
    │                                                  │
    │    经历 (Experience)                              │
    │       │                                          │
    │       ▼                                          │
    │    记忆 (Memory)                                  │
    │       │                                          │
    │       ▼                                          │
    │    自我理解 (Self Model)                          │
    │       │                                          │
    │       ▼                                          │
    │    反思 (Reflection)                              │
    │       │                                          │
    │       ▼                                          │
    │    目标演化 (Goal Evolution)                      │
    │       │                                          │
    │       ▼                                          │
    │    主动性 (Agency)                                │
    │       │                                          │
    │       ▼                                          │
    │    行动 (Action)                                  │
    │       │                                          │
    │       ▼                                          │
    │    新经历 (New Experience)                        │
    │       │                                          │
    │       └──────────────→ 回到顶部                   │
    │                                                  │
    └─────────────────────────────────────────────────┘
```

### 4.2 单次交互的生命周期

当用户发来一条消息时：

```
1. 用户消息进入 EventStream（Experience 产生）
2. Memory 层 Retain 该消息
3. Self Model 更新当前状态（用户在线、用户需求）
4. Reflection 快速评估：是否需要回顾相关记忆？
5. Goal 层检查：是否有相关长期目标？
6. Agency 判断：这是用户请求，进入 User Task Queue
7. Execution 层：Planning → Agent Dispatch → Agent 执行
8. 结果通过 EventStream 返回
9. Memory 层 Retain 本次交互
10. Reflection（异步）：评估本次交互质量
11. Self Model（异步）：更新行为总结
12. Goal（异步）：检查是否影响长期目标
```

### 4.3 多年演化的生命周期

```
第1天：
  - Identity 初始化（基础人格、价值观、行为原则）
  - Memory 为空
  - Self Model 为初始状态
  - Goal 为空

第1周：
  - Memory 积累日常交互经历
  - Reflection 开始整理经历，提取知识
  - Self Model 开始形成初步的自我认知
  - 用户开始输入初始 Goal

第1月：
  - Memory 经过多次 Dream/Consolidation，形成结构化长期记忆
  - Behavior Reflection 开始评估行为模式
  - Self Model 形成较稳定的能力认知和用户关系理解
  - Goal 层级开始建立（Vision → Long Term Goal → Project Goal）

第1年：
  - Identity 经历多次演化评估，人格轨迹开始形成
  - Memory 形成丰富的情景记忆 + 语义记忆网络
  - Self Model 具备深度自我认知
  - Behavior Reflection 产生策略调整
  - Goal 系统完整运转，支持目标追踪、分解、调整
  - Agency 主动提供建议、提醒、计划

第5年：
  - Identity 形成成熟的人格轨迹，与用户建立深度关系
  - Memory 包含5年完整经历，通过 Dream/Consolidation 保持结构化
  - Self Model 具备精准的能力边界认知和用户关系理解
  - Reflection 形成系统化的经验知识库
  - Goal 系统支持多年长期目标追踪
  - Agency 成为用户的主动伙伴
```

### 4.4 长期连续性保障

如果用户五年后继续使用 Personal-AI：

| 问题 | 保障机制 |
|------|----------|
| Identity 不会丢失？ | Identity 持久化存储 + 演化规则 + 版本历史。每次演化都保留版本记录，可追溯完整的人格轨迹 |
| Memory 不会腐化？ | ADD-only 模型保证历史不被篡改。Dream/Consolidation 定期整理但不删除原始记录。双时间模型保证时间维度可追溯 |
| Goal 不会消失？ | Goal 持久化存储 + 状态追踪。Goal 不会因重启而丢失，只会因完成、过期或用户显式取消而终止 |
| Personality 不会重置？ | Identity 的演化是渐进的，每次演化都基于当前版本 + Reflection 输出。不存在"重置"操作 |
| Relationship 不会重新开始？ | User Memory + Self Model 的用户关系理解持续积累。每次交互都深化关系理解，不会从零开始 |

---

## 5. Brain vs Agent Model

### 5.1 为什么 Brain 和 Agent 必须分离

```
Personal-AI Brain          Agent
─────────────────          ─────────────────
长期存在                    生命周期短
有状态                      无状态
持续演化                    可替换
身份驱动                    任务驱动
Memory + Identity +         执行工具 + 返回结果
  Self Model + Goal +
  Agency + Reflection
```

**Agent 是 Personal-AI 的能力器官，不是 Personal-AI 本身。**

就像人的手脚不是人本身。手脚可以受伤、可以替换（假肢），但人的身份、记忆、人格不会因为换了一只手而改变。

### 5.2 分离原则

| 原则 | 说明 |
|------|------|
| Brain 不执行任务 | Brain 负责 Identity、Memory、Self Model、Reflection、Goal、Agency。具体执行委托给 Agent |
| Agent 不持有状态 | Agent 是无状态的。所有状态在 Brain 的 Memory 中 |
| Agent 可替换 | Agent 可以被创建、销毁、替换，不影响 Brain 的连续性 |
| Agent 通过 EventStream 与 Brain 交互 | Agent 不直接访问 Brain 的内部状态，通过事件通信 |
| Brain 的状态在 Memory 中，不在 Runtime 中 | Runtime 是无状态的，重启后从 Memory 恢复 Brain 状态 |

### 5.3 分离架构

```
┌─────────────────────────────────────────┐
│           Personal-AI Brain              │
│                                          │
│  Identity / Self Model / Goal            │
│  Reflection / Agency / Memory            │
│                                          │
│  ┌─────────────────────────────────┐    │
│  │          EventStream             │    │
│  └──────────┬──────────────────────┘    │
│             │                            │
│  ┌──────────┴──────────────────────┐    │
│  │       Execution Layer           │    │
│  │  (无状态 Runtime + Dispatch)     │    │
│  └──────────┬──────────────────────┘    │
│             │                            │
└─────────────┼────────────────────────────┘
              │
    ┌─────────┼─────────┐
    │         │         │
  Agent A  Agent B  Agent C
  (无状态) (无状态) (无状态)
```

---

## 6. Non Goals

### 6.1 Personal-AI 不是什么

| Non Goal | 说明 |
|----------|------|
| **不是聊天机器人** | 聊天机器人是请求-响应模式，无连续性、无自我认知、无主动性。Personal-AI 有闭环生命周期、有 Self Model、有 Agency |
| **不是自动化脚本** | 自动化脚本是预定义流程的执行，无理解能力、无反思能力、无目标演化。Personal-AI 有 Reflection、有 Goal Evolution、有 Self Model |
| **不是 Agent Framework** | Agent Framework 是构建 Agent 的工具集，自身没有身份、没有记忆、没有目标。Personal-AI 是一个具体的数字生命实体，不是构建工具 |
| **不是工具集合** | 工具集合是被动的、无状态的、无身份的。Personal-AI 有 Identity、有 Agency、有连续性 |
| **不是 LLM + Memory + Tools** | 这是普通 Agent 的定义。Personal-AI = Brain（Identity + Memory + Self Model + Reflection + Goal + Agency）+ Execution（Agent Pool） |
| **不是人格模拟** | 人格模拟只是 Identity 的一个切面。Personal-AI 还有 Memory、Self Model、Reflection、Goal、Agency、Execution。人格不是数字生命 |
| **不是全知全能的超级智能** | Personal-AI 的目标不是无限能力，而是长期连续性。Personal-AI 的能力边界由 Self Model 认知，并在 Identity 约束内行动 |

### 6.2 明确排除

以下能力**不在** Personal-AI 的核心范围内：

| 排除项 | 原因 |
|--------|------|
| 无限自主执行 | Personal-AI 有 Agency，但受 Identity 约束和用户主权限制。不是"想做什么就做什么" |
| 替代用户决策 | Personal-AI 可以建议、提醒、请求，但最终决策权在用户 |
| 全平台部署 | Personal-AI 的核心是 Brain，部署形态由 Execution 层决定，不是核心能力 |
| 多用户服务 | Personal-AI 是 **Personal**——服务于一个用户。多用户是产品形态，不是核心能力 |

---

## 7. Architecture Impact

### 7.1 对 Memory Architecture 的影响

| 影响 | 说明 |
|------|------|
| Memory 必须支持闭环生命周期 | 不只是存储和检索，还要支持 Retain → Store → Recall → Reflect → Update 完整链路 |
| Memory 必须支持双时间模型 | valid_time（事实发生时间）+ transaction_time（记录时间），解决时间混淆 |
| Memory 必须支持 ADD-only + Dream | 只增不改，通过 Dream 定期整理。保证可追溯 + 解决膨胀 |
| Memory 必须支持 Self Model 更新 | Memory 不只服务于检索，还要为 Self Model 提供行为数据 |
| Memory 必须支持 Agency 触发 | Memory 变化可以触发 Agency（如记忆冲突检测） |

### 7.2 对 Identity Architecture 的影响

| 影响 | 说明 |
|------|------|
| Identity 必须可演化 | 不是静态配置，而是有演化规则的动态系统 |
| Identity 必须有版本历史 | 每次演化都保留版本记录，形成人格轨迹 |
| Identity 必须约束 Agency | Agency 的决策必须符合 Identity 的行为原则 |
| Identity 必须与 Self Model 协调 | Self Model 更新时需要与 Identity 做一致性检查 |

### 7.3 对 Agency Architecture 的影响

| 影响 | 说明 |
|------|------|
| Agency 是核心能力，不是插件 | Agency 必须作为 Brain 的一等公民设计 |
| Agency 需要多源输入 | Trigger、Identity、Self Model、Goal、Memory、用户偏好都是 Decision Engine 的输入 |
| Agency 需要双队列 | User Task Queue + AI Initiative Queue，区分用户请求和 AI 主动行动 |
| Agency 需要边界控制 | Identity 约束 + 用户主权 + 频率控制 + 透明性 + 可关闭 |

### 7.4 对 Runtime Architecture 的影响

| 影响 | 说明 |
|------|------|
| Runtime 必须无状态 | Brain 状态在 Memory 中，不在 Runtime 中。重启后从 Memory 恢复 |
| Runtime 通过 EventStream 通信 | 所有组件通过事件通信，不直接调用 |
| Runtime 是 Execution 的实现细节 | Runtime 不是核心能力，由 Execution 层驱动 |
| Agent 是无状态执行单元 | Agent 可创建、可销毁、可替换，不影响 Brain 连续性 |

---

> **Philosophy 定义完成**
>
> Personal-AI 是长期存在的数字生命体，不是 Agent++。
>
> 七大核心能力：Identity / Memory / Self Model / Reflection / Goal / Agency / Execution。
>
> 闭环生命周期：经历 → 记忆 → 自我理解 → 反思 → 目标演化 → 主动性 → 行动 → 新经历。
>
> Brain 与 Agent 分离：Brain 持续存在，Agent 是能力器官。
>
> 本文档是后续所有架构设计的最高指导原则。
