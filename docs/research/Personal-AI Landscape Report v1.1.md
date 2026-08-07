# Personal-AI Landscape Report v1.1

> **修订说明**
>
> 本报告是 `Landscape-Report.md`（v1.0）的修订版本。
>
> v1.0 完成了 12 个项目的深度调研，研究结论仍然有效。
> v1.1 的修订原因：架构复审发现原报告的方向偏移——过度聚焦 Agent Runtime / Memory / Tool / Workflow / Planning，而忽略了 Personal-AI 的真正目标：**Digital Life**。
>
> v1.1 不删除 v1.0 的任何研究内容，而是：
> 1. 新增 **Digital Life Architecture Perspective** 章节，重新定义 Personal-AI 的核心范式
> 2. 将核心能力模型从 5 项升级为 7 项（新增 Self Model + Agency，Planning 重定义为 Execution）
> 3. 新增 Agency 能力完整设计
> 4. 重新定位已有研究结论：它们是 Personal-AI 的**能力组件**，不是 Personal-AI 的**定义**
>
> 原报告路径：`docs/research/Landscape-Report.md`

---

## 目录

- [0. 修订背景](#0-修订背景)
- [1. Digital Life Architecture Perspective](#1-digital-life-architecture-perspective)
- [2. 核心能力模型升级](#2-核心能力模型升级)
- [3. Agency 能力设计](#3-agency-能力设计)
- [4. Self Model 能力设计](#4-self-model-能力设计)
- [5. 已有研究结论的重新定位](#5-已有研究结论的重新定位)
- [6. 修订后的架构建议](#6-修订后的架构建议)
- [7. v1.0 → v1.1 变更摘要](#7-v10--v11-变更摘要)

---

## 0. 修订背景

### 0.1 v1.0 做对了什么

v1.0 Landscape Report 完成了 12 个项目的深度调研：

| 领域 | 项目 | 调研价值 |
|------|------|----------|
| Agent Runtime | OpenHands, Goose, Codex CLI, Claude Code | 无状态 Runtime + EventStream 架构验证 |
| Memory | Mem0, Graphiti, Hindsight | ADD-only / 双时间 / Retain-Recall-Reflect 三大模型 |
| Identity | SOUL.md | 可演化身份规范 |
| Planning | LangGraph, Mastra | StateGraph + Checkpointer 状态编排 |
| 编程 Agent | Aider | Repo Map 结构化映射 |

这些研究结论**仍然有效**，是 Personal-AI 各能力组件的设计输入。

### 0.2 v1.0 的问题

v1.0 的核心能力模型为：

```
Memory / Identity / Reflection / Goal / Planning / Runtime
```

这个模型的问题：

| 问题 | 说明 |
|------|------|
| **缺少 Self Model** | 没有"我如何理解自己"这一层。Identity 定义"我是谁"，Memory 记录"发生了什么"，但缺少连接两者的自我认知层 |
| **缺少 Agency** | 没有"我什么时候应该主动行动"的能力。整个模型是被动响应用户请求，而非主动发起 |
| **Planning 定位错误** | Planning 被当作核心能力，但它只是 Execution 的一个子阶段。真正的核心能力是 Execution（从 Goal 到 Action 的完整链路） |
| **Runtime 定位错误** | Runtime 被当作核心能力，但它是基础设施，不是 Personal-AI 的本质特征 |
| **范式偏移** | 整个模型仍然在"Agent + Memory"范式内，没有跳出到"Digital Life"范式 |

### 0.3 修订原则

1. **不删除已有研究** — v1.0 的 12 个项目调研结论完整保留
2. **重新定位** — 已有结论从"Personal-AI 的定义"降级为"Personal-AI 的能力组件"
3. **补充缺失** — 新增 Self Model 和 Agency 两个核心能力
4. **范式升级** — 从 Agent Architecture 升级为 Digital Life Architecture

---

## 1. Digital Life Architecture Perspective

### 1.1 普通 Agent 的生命周期

普通 Agent 的生命周期是**线性开环**的：

```
用户请求
    │
    ▼
  推理 (Reasoning)
    │
    ▼
  工具调用 (Tool Calling)
    │
    ▼
  执行 (Action)
    │
    ▼
  结束 (End)
```

特征：
- **触发方式**：用户显式请求
- **状态**：无状态或会话级状态，结束后消失
- **目标**：完成当前任务
- **学习**：无（或仅限当前会话的上下文窗口）
- **主动性**：零——不请求就不行动
- **连续性**：无——每次请求都是独立的

### 1.2 Personal-AI 的生命周期

Personal-AI 的生命周期是**闭环自演化**的：

```
经历 (Experience)
    │
    ▼
  记忆 (Memory)
    │
    ▼
  自我理解 (Self Model)
    │
    ▼
  反思 (Reflection)
    │
    ▼
  目标演化 (Goal Evolution)
    │
    ▼
  主动性 (Agency)
    │
    ▼
  行动 (Action)
    │
    ▼
  新经历 (New Experience)
    │
    └──────→ 回到顶部（闭环）
```

特征：
- **触发方式**：用户请求 **+** 内部触发（时间、事件、Memory 变化、Goal 变化）
- **状态**：持久化状态，跨会话、跨设备、跨时间
- **目标**：长期目标追踪 + 短期任务执行
- **学习**：持续学习——每次经历都更新 Memory、Self Model、Goal
- **主动性**：有——可以主动建议、提醒、发起行动
- **连续性**：永久——从创建开始持续存在，不因重启而丢失

### 1.3 根本区别

| 维度 | 普通 Agent | Personal-AI |
|------|-----------|-------------|
| 存在模式 | 任务级（请求→执行→结束） | 生命级（经历→记忆→理解→反思→演化→行动→新经历） |
| 状态 | 无状态 / 会话级 | 持久化 + 可演化 |
| 触发 | 用户请求驱动 | 用户请求 + 内部 Agency 驱动 |
| 目标 | 完成当前任务 | 长期目标追踪 + 短期任务执行 |
| 学习 | 无 | 闭环自演化 |
| 主动性 | 零 | 有（Trigger + Decision + Initiative Queue） |
| 连续性 | 无 | 永久 |
| 身份 | 无 / 静态配置 | 可演化 Identity + Self Model |
| 范式 | 工具 | Digital Life |

### 1.4 为什么不是 Agent++

Agent++ 的思路是：在 Agent 基础上加 Memory、加 Identity、加 Reflection……这是**增量改良**。

Personal-AI 不是 Agent++，因为：

1. **起点不同**：Agent 的起点是"任务"，Personal-AI 的起点是"存在"
2. **闭环不同**：Agent 是开环（请求→执行→结束），Personal-AI 是闭环（经历→记忆→理解→反思→演化→行动→新经历）
3. **主动性不同**：Agent 是被动的（不请求不行动），Personal-AI 有 Agency（可以主动行动）
4. **Self Model 不同**：Agent 不需要理解自己，Personal-AI 必须理解自己才能演化
5. **目标不同**：Agent 的目标是完成任务，Personal-AI 的目标是帮助用户走向长期愿景

**结论**：Personal-AI 是一种新的范式，不是 Agent 的改良版。

---

## 2. 核心能力模型升级

### 2.1 v1.0 模型（旧）

```
Memory / Identity / Reflection / Goal / Planning / Runtime
```

### 2.2 v1.1 模型（新）

```
Identity / Memory / Self Model / Reflection / Goal / Agency / Execution
```

### 2.3 变更说明

| 变更 | 说明 |
|------|------|
| **新增 Self Model** | Identity 和 Memory 之间的连接层。回答"我如何理解自己"。包括当前能力认知、用户关系理解、历史行为总结、优势与不足 |
| **新增 Agency** | Personal-AI 与普通 Agent 的核心区别。回答"我什么时候应该主动行动"。包括 Trigger System、Decision Engine、Initiative Queue |
| **Planning → Execution** | Planning 降级为 Execution 的子阶段。Execution 涵盖从 Goal 到 Action 的完整链路：Planning → Resource Allocation → Task Dispatch → Agent Execution → Result Collection |
| **Runtime 移除** | Runtime 不再作为核心能力，而是基础设施。Runtime 的设计由 Execution 层驱动 |

### 2.4 七大能力定义

#### Identity — 我是谁？

```
Identity 定义 Personal-AI 的存在本质。
```

包含：
- 人格（Personality）
- 价值观（Values）
- 行为原则（Principles）
- 长期倾向（Long-term Tendencies）
- 用户关系定义（Relationship with User）

Identity ≠ Memory。Memory 记录"发生了什么"，Identity 定义"我如何理解自己"。

参考：SOUL.md 规范

#### Memory — 我经历过什么？

```
Memory 是 Personal-AI 的经历积累。
```

类型：
- Event Memory — 原始事件流
- Episodic Memory — 情景记忆（带时间、地点、上下文的经历片段）
- Semantic Memory — 语义记忆（从经历中提取的知识）
- User Memory — 关于用户的记忆
- Project Memory — 关于项目的记忆

设计原则：
- ADD-only（只增不改）
- 双时间模型（valid_time + transaction_time）
- 非有损演化（事实变化不删除旧事实，标记有效期）
- Retain / Recall / Reflect 生命周期

参考：Mem0 + Graphiti + Hindsight

#### Self Model — 我如何理解自己？

```
Self Model 是 Identity 和 Memory 的连接层。
```

Self Model 是 Personal-AI 对自身的动态认知，包括：
- 当前能力认知（我能做什么？不能做什么？）
- 用户关系理解（用户信任我什么？用户需要我什么？）
- 历史行为总结（我过去做得好的是什么？做得差的是什么？）
- 优势与不足（我的强项是什么？弱项是什么？）
- 当前状态（我现在处于什么状态？）

Self Model ≠ Identity。Identity 是稳定的自我定义，Self Model 是动态的自我认知。

Self Model ≠ Memory。Memory 是原始经历，Self Model 是从经历中提炼的自我理解。

**Self Model 是 v1.0 完全缺失的能力，是 Personal-AI 与普通 Agent 的重要区别。**

#### Reflection — 我如何从过去学习？

```
Reflection 是 Personal-AI 的元认知能力。
```

必须区分两种 Reflection：

| 类型 | 回答 | 输入 | 输出 |
|------|------|------|------|
| Memory Reflection | 发生了什么？获得了什么知识？ | 原始经历 | 整理后的记忆 + 提取的知识 |
| Behavior Reflection | 我的判断是否正确？我的行为是否有效？是否需要改变策略？ | 自身行为记录 + 结果反馈 | 行为评估 + 策略调整建议 |

Memory Reflection 有参考（Hindsight + Mem0 Dream）。

**Behavior Reflection 是 v1.0 完全缺失的能力，是 Personal-AI 的核心差异化方向。**

#### Goal — 我要帮助用户走向哪里？

```
Goal 是 Personal-AI 的长期方向感。
```

Goal 层级：

```
Vision（用户想成为什么样的人 / 达成什么样的状态）
    │
    ▼
Long Term Goal（1-5 年方向性目标）
    │
    ▼
Project Goal（数周-数月的项目级目标）
    │
    ▼
Task（具体可执行任务）
```

Goal 来源：
- 用户明确输入
- Reflection 产生（从经验中发现新方向）
- Self Model 推断（从自我认知中发现机会）
- 环境变化（外部条件改变导致目标调整）

**Goal 系统在所有调研项目中完全空白，是 Personal-AI 最大的差异化机会。**

#### Agency — 我什么时候应该主动行动？

```
Agency 是 Personal-AI 从被动响应到主动存在的关键能力。
```

Agency 使 Personal-AI 能够：
- 主动发现需要关注的时刻
- 主动判断是否应该行动
- 主动产生建议、提醒、计划、行动请求

Agency 不是插件，不是模块，而是 Personal-AI 的**存在方式**。

**Agency 在所有调研项目中完全空白，是 Personal-AI 与普通 Agent 的核心区别。**

详见 [§3. Agency 能力设计](#3-agency-能力设计)。

#### Execution — 我如何把 Goal 变成 Action？

```
Execution 是从 Goal 到 Action 的完整链路。
```

Execution 包含：
- Planning（从 Goal 生成 Plan）
- Resource Allocation（资源调度）
- Task Dispatch（任务分派给 Agent）
- Agent Execution（Agent 执行任务）
- Result Collection（收集结果并反馈）

Execution 层使用无状态 Runtime + EventStream 架构，Agent 是无状态、可替换的执行单元。

参考：OpenHands（无状态 Runtime + EventStream）+ LangGraph（StateGraph + Checkpointer）

---

## 3. Agency 能力设计

### 3.1 为什么需要 Agency

普通 Agent 的触发模型：

```
用户请求 → Agent 执行 → 结束
```

Agent 永远不会主动行动。如果不请求，Agent 就不存在。

Personal-AI 的触发模型：

```
用户请求 ──→ Personal-AI 响应
                    │
内部触发 ──→ Personal-AI 主动行动
```

Personal-AI 必须能够主动行动，因为：

1. **长期目标需要持续追踪** — 用户不会每次都提醒"检查我的目标进度"
2. **经验需要主动反思** — 不能等用户问"你从上次失败中学到了什么"
3. **关系需要主动维护** — 不能等用户问"你还记得我上次说的话吗"
4. **环境变化需要主动响应** — 不能等用户问"外部条件变了，我的计划需要调整吗"

### 3.2 Agency 架构

```
┌─────────────────────────────────────────────────────┐
│                    Agency Layer                      │
│                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────┐ │
│  │   Trigger    │→ │  Decision    │→ │ Initiative│ │
│  │   System     │  │   Engine     │  │  Queue    │ │
│  └──────────────┘  └──────────────┘  └───────────┘ │
│         ▲                  ▲                │       │
│         │                  │                ▼       │
│    ┌────┴────┐        ┌────┴────┐     ┌──────┐    │
│    │ Triggers│        │ Context │     │Action│    │
│    └─────────┘        └─────────┘     └──────┘    │
└─────────────────────────────────────────────────────┘
```

### 3.3 Trigger System

负责发现：**什么时候需要思考。**

#### 触发源

| 触发类型 | 来源 | 示例 |
|----------|------|------|
| 时间触发 | 定时器 / 周期性事件 | 每日反思、每周目标回顾、每月 Identity 演化检查 |
| 外部事件触发 | EventStream 中的外部事件 | 用户发来消息、日历事件、外部系统状态变化 |
| Memory 变化触发 | Memory 层的写入/更新事件 | 新记忆写入、记忆冲突检测、记忆过期警告 |
| Goal 变化触发 | Goal 层的状态变化 | 目标完成、目标过期、目标冲突检测 |
| Self Model 变化触发 | Self Model 的更新事件 | 能力评估变化、用户关系变化 |
| Reflection 输出触发 | Reflection 完成后的输出 | Reflection 发现需要调整策略、发现新方向 |

#### Trigger 数据结构

```json
{
  "trigger_id": "trg_20260807_001",
  "trigger_type": "time | external_event | memory_change | goal_change | self_model_change | reflection_output",
  "trigger_source": "daily_reflection_timer | user_message | memory_write | goal_completed | ...",
  "trigger_timestamp": "2026-08-07T10:00:00Z",
  "trigger_payload": {
    "description": "每日反思触发",
    "context": {
      "last_reflection_time": "2026-08-06T10:00:00Z",
      "new_experiences_count": 12,
      "unresolved_reflections": 2
    }
  },
  "priority": "low | medium | high | critical"
}
```

### 3.4 Decision Engine

负责判断：**是否应该行动，以及如何行动。**

Decision Engine 接收 Trigger，结合上下文，输出决策。

#### 决策输入

| 输入 | 来源 | 用途 |
|------|------|------|
| Trigger | Trigger System | 触发原因 |
| Identity | Identity Layer | 行为原则、价值观（约束决策边界） |
| Self Model | Self Model Layer | 当前能力认知（判断是否可行） |
| Goal | Goal Layer | 当前目标优先级（判断是否相关） |
| Memory | Memory Layer | 历史经验（判断是否有先例） |
| User Preferences | Identity Layer | 用户偏好（判断用户是否希望被打扰） |

#### 决策输出

| 决策类型 | 说明 | 示例 |
|----------|------|------|
| Act Now | 立即执行 | 用户消息需要回复 |
| Schedule | 安排在未来某个时间执行 | 明天早上提醒用户开会 |
| Suggest | 向用户提出建议 | "我注意到你的项目进度落后，是否需要调整计划？" |
| Notify | 通知用户 | "目标已完成" / "检测到冲突" |
| Defer | 暂缓，等待更多信息 | 触发条件不充分，记录但不行动 |
| Suppress | 抑制，不行动 | 基于用户偏好判断不应打扰 |

#### Decision 数据结构

```json
{
  "decision_id": "dec_20260807_001",
  "trigger_id": "trg_20260807_001",
  "decision_type": "act_now | schedule | suggest | notify | defer | suppress",
  "decision_timestamp": "2026-08-07T10:00:05Z",
  "reasoning": "每日反思触发，距上次反思已超过24小时，有12条新经历未反思，用户当前不在线",
  "context_snapshot": {
    "identity_version": "v2.3",
    "self_model_version": "v1.8",
    "active_goals": ["goal_001", "goal_003"],
    "user_status": "offline",
    "user_preference_do_not_disturb": false
  },
  "action": {
    "type": "reflection_session",
    "target": "behavior_reflection",
    "scope": "last_24h",
    "priority": "medium"
  },
  "confidence": 0.85
}
```

### 3.5 Initiative Queue

负责管理：**Personal-AI 主动产生的行动请求。**

#### 双队列设计

```
┌─────────────────────────────────────────┐
│            Action Queue                  │
│                                          │
│  ┌────────────────┐  ┌────────────────┐│
│  │ User Task Queue│  │ AI Initiative  ││
│  │                │  │    Queue       ││
│  │ 用户显式请求   │  │ AI 主动产生    ││
│  │ 优先级：高     │  │ 优先级：动态   ││
│  └────────────────┘  └────────────────┘│
│                                          │
│  调度规则：                               │
│  1. User Task 优先于 AI Initiative       │
│  2. AI Initiative 中 critical 例外       │
│  3. AI Initiative 需要用户确认后执行     │
│     （除非是 suggest/notify 类型）       │
└─────────────────────────────────────────┘
```

#### User Task Queue

用户显式请求的任务队列。

特征：
- 优先级：高（默认高于 AI Initiative）
- 来源：用户消息、用户指令
- 执行方式：直接进入 Execution 层
- 确认：不需要额外确认（用户已明确请求）

#### AI Initiative Queue

Personal-AI 主动产生的行动队列。

特征：
- 优先级：动态（由 Decision Engine 决定）
- 来源：Decision Engine 的输出
- 执行方式：
  - `suggest` / `notify`：直接呈现给用户，不执行
  - `act_now` / `schedule`：需要用户确认后执行（除非 critical）
- 确认机制：
  - 低风险行动：自动执行，事后通知
  - 中风险行动：请求用户确认
  - 高风险行动：必须用户确认，不自动执行

#### Initiative 数据结构

```json
{
  "initiative_id": "ini_20260807_001",
  "decision_id": "dec_20260807_001",
  "queue": "ai_initiative",
  "type": "suggestion | reminder | plan_request | action_request | notification",
  "content": "我注意到你在过去三天都在调试同一个模块的错误。根据我的经验，这类问题通常是接口定义不一致导致的。建议你检查一下模块间的接口契约。",
  "priority": "medium",
  "requires_user_confirmation": false,
  "created_at": "2026-08-07T10:00:10Z",
  "expires_at": "2026-08-07T18:00:00Z",
  "status": "pending | approved | rejected | expired | executed"
}
```

### 3.6 Agency 的边界

Agency 不等于无限自主。Personal-AI 的主动性必须受约束：

| 边界 | 规则 |
|------|------|
| Identity 约束 | Agency 的所有决策必须符合 Identity 中定义的行为原则和价值观 |
| 用户主权 | 用户始终拥有最终决策权。AI Initiative 的高风险行动必须用户确认 |
| 透明性 | 所有 AI Initiative 必须可追溯、可审计、可回溯 |
| 频率控制 | Agency 有频率限制，避免过度打扰用户 |
| 可关闭 | 用户可以关闭 Agency 的主动行动能力，退回到纯被动模式 |

---

## 4. Self Model 能力设计

### 4.1 为什么需要 Self Model

v1.0 有 Identity（"我是谁"）和 Memory（"发生了什么"），但缺少两者之间的连接：

```
Identity（静态定义）  ←——缺少连接——→  Memory（原始经历）
```

Self Model 填补这个缺口：

```
Identity（静态定义）  ←——Self Model——→  Memory（原始经历）
     "我是谁"          "我如何理解自己"      "发生了什么"
```

### 4.2 Self Model 的内容

| 维度 | 回答 | 数据来源 |
|------|------|----------|
| 能力认知 | 我能做什么？不能做什么？做得好的是什么？做得差的是什么？ | 历史行为记录 + Reflection 输出 |
| 用户关系理解 | 用户信任我什么？用户需要我什么？用户对我的期望是什么？ | 交互历史 + 用户反馈 |
| 历史行为总结 | 我过去最重要的行为是什么？产生了什么影响？ | Memory + Reflection |
| 优势与不足 | 我的强项是什么？弱项是什么？ | Behavior Reflection 输出 |
| 当前状态 | 我现在处于什么状态？最近表现如何？ | 近期 Memory + Reflection |

### 4.3 Self Model 与 Identity 的关系

| 维度 | Identity | Self Model |
|------|----------|------------|
| 回答 | 我是谁？ | 我如何理解自己？ |
| 性质 | 定义性（declarative） | 认知性（cognitive） |
| 稳定性 | 相对稳定，缓慢演化 | 动态更新，随经验调整 |
| 来源 | 初始定义 + 演化规则 | Memory + Reflection 的输出 |
| 作用 | 约束行为边界 | 指导策略调整 |

### 4.4 Self Model 数据结构

```json
{
  "self_model_id": "sm_20260807_v1.8",
  "version": "1.8",
  "last_updated": "2026-08-07T10:00:00Z",
  "capability_assessment": {
    "strengths": [
      "代码理解和生成",
      "项目架构分析",
      "技术方案评估"
    ],
    "weaknesses": [
      "创意写作",
      "非技术领域的建议"
    ],
    "confidence_areas": {
      "high": ["编程", "系统设计"],
      "medium": ["项目管理"],
      "low": ["情感支持", "创意写作"]
    }
  },
  "user_relationship": {
    "trust_level": "high",
    "user_needs": ["技术顾问", "代码审查", "架构设计"],
    "user_expectations": "用户期望我在技术领域提供高质量建议，不期望我在非技术领域过多介入",
    "interaction_pattern": "用户倾向于在技术决策时咨询我，日常交流较少"
  },
  "behavior_summary": {
    "recent_highlights": [
      "帮助用户完成了 Personal-AI 项目架构调研",
      "识别了 Memory 系统设计的关键取舍"
    ],
    "recent_failures": [
      "在讨论 Agency 设计时最初方案过于简单"
    ],
    "behavioral_patterns": [
      "倾向于提供详细分析而非简短回答",
      "在不确定时会主动说明而非猜测"
    ]
  },
  "current_state": {
    "focus_area": "Personal-AI 架构设计",
    "recent_performance": "good",
    "active_reflections": 2,
    "pending_initiatives": 1
  }
}
```

### 4.5 Self Model 的更新机制

Self Model 不是静态的，它通过以下机制持续更新：

```
Behavior Reflection 输出
        │
        ▼
  Self Model 更新请求
        │
        ▼
  与 Identity 一致性检查
        │
        ├── 一致 → 更新 Self Model
        │
        └── 不一致 → 触发 Identity 演化评估
```

---

## 5. 已有研究结论的重新定位

### 5.1 重新定位原则

v1.0 的研究结论从"Personal-AI 的定义"降级为"Personal-AI 能力组件的设计输入"。

| v1.0 定位 | v1.1 定位 |
|-----------|-----------|
| Personal-AI = OpenHands + Mem0 + Graphiti + ... | Personal-AI = Digital Life（Identity + Memory + Self Model + Reflection + Goal + Agency + Execution） |
| 调研项目定义了 Personal-AI | 调研项目是 Personal-AI 各能力组件的参考 |
| 架构 = 组合各项目 | 架构 = Digital Life 架构，各项目提供组件级参考 |

### 5.2 各项目在新模型中的定位

| 项目 | v1.0 定位 | v1.1 定位 |
|------|-----------|-----------|
| OpenHands | Runtime 架构核心 | Execution 层参考（无状态 Runtime + EventStream） |
| Mem0 | Memory 架构核心 | Memory 层参考（ADD-only + Dream + 多信号检索） |
| Graphiti | Memory 架构核心 | Memory 层参考（双时间 + 非有损演化 + 三层子图） |
| Hindsight | Memory + Reflection 参考 | Memory 层参考（Retain/Recall/Reflect）+ Memory Reflection 参考 |
| SOUL.md | Identity 规范 | Identity 层参考（可演化身份规范）+ Self Model 参考（自我认知维度） |
| LangGraph | Planning 参考 | Execution 层参考（StateGraph + Checkpointer） |
| Claude Code | Hooks + Subagents 参考 | Execution 层参考（Subagents 委托）+ Event 参考（Hooks） |
| Goose | MCP 参考 | Execution 层参考（MCP 工具协议） |
| Codex CLI | Agent Identity 参考 | Execution 层参考（Agent 模块化设计） |
| Aider | Repo Map 参考 | Memory 层参考（结构化上下文映射） |
| Mastra | Processor Pipeline 参考 | Execution 层参考（Pipeline 架构） |
| KaijiBot | 待确认 | 无变化 |

### 5.3 v1.0 借鉴建议的重新分类

v1.0 的 P0/P1/P2 建议仍然有效，但需要映射到新的七能力模型：

#### Identity 层

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| P0 | SOUL.md 身份规范 | SOUL.md | 不变 |
| P1 | 身份演化规则 | SOUL.md | 不变 |

#### Memory 层

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| P0 | ADD-only + Dream | Mem0 | 不变 |
| P0 | 双时间模型 | Graphiti | 不变 |
| P0 | 非有损演化 | Graphiti | 不变 |
| P0 | Retain/Recall/Reflect | Hindsight | 不变 |
| P1 | 三层子图 | Graphiti | 不变 |
| P1 | 多信号检索 | Mem0 | 不变 |
| P1 | 4层记忆模型 | SOUL.md | 不变 |
| P1 | Condensers压缩 | OpenHands | 不变 |

#### Self Model 层（新增）

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| **缺失** | **能力认知模型** | **需自行设计** | Self Model 核心 |
| **缺失** | **用户关系动态理解** | **需自行设计** | Self Model 核心 |
| **缺失** | **行为模式总结** | **需自行设计** | Self Model 核心 |
| P1 | 身份演化反思 | SOUL.md | Self Model + Reflection 交叉 |

#### Reflection 层

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| P0 | Reflect 一等公民 | Hindsight | Memory Reflection |
| P0 | Dream 记忆反思 | Mem0 | Memory Reflection |
| P1 | Community 抽象聚类 | Graphiti | Memory Reflection |
| **缺失** | **行为反思（事后复盘）** | **需自行设计** | **Behavior Reflection（核心差异化）** |

#### Goal 层

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| **缺失** | **长期目标系统** | **需自行设计** | Goal 核心（最大差异化） |
| **缺失** | **目标分解** | **需自行设计** | Goal 核心 |
| **缺失** | **目标调整** | **需自行设计** | Goal 核心 |

#### Agency 层（新增）

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| **缺失** | **Trigger System** | **需自行设计** | Agency 核心 |
| **缺失** | **Decision Engine** | **需自行设计** | Agency 核心 |
| **缺失** | **Initiative Queue** | **需自行设计** | Agency 核心 |

#### Execution 层（原 Runtime + Planning）

| 优先级 | 建议 | 来源 | v1.1 定位 |
|--------|------|------|-----------|
| P0 | 无状态 Runtime + EventStream | OpenHands | Execution 基础架构 |
| P0 | MCP 工具协议 | Goose + Claude Code | Execution 工具层 |
| P1 | StateGraph + Checkpointer | LangGraph | Execution Planning 子层 |
| P1 | Hooks 事件系统 | Claude Code | Execution 事件层 |
| P1 | Human-in-the-loop | LangGraph | Execution 确认机制 |
| P1 | Subagents 委托 | Claude Code | Execution Agent 调度 |
| P1 | Processor Pipeline | Mastra | Execution 请求处理 |
| P2 | Code Mode 沙箱 | Goose | Execution 执行沙箱 |

---

## 6. 修订后的架构建议

### 6.1 新架构全景

```
┌─────────────────────────────────────────────────────────────┐
│                     Personal-AI Brain                        │
│                                                              │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────┐  │
│  │ Identity │ │   Goal   │ │  Agency  │ │  Self Model  │  │
│  │ (SOUL)   │ │ (自建)   │ │ (自建)   │ │  (自建)      │  │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └──────┬───────┘  │
│       │            │            │              │            │
│       └────────────┴────────────┴──────────────┘            │
│                         │                                    │
│                    ┌────┴────┐                                │
│                    │Reflection│                                │
│                    │(Memory + │                                │
│                    │Behavior) │                                │
│                    └────┬────┘                                │
│                         │                                    │
│                    ┌────┴────┐                                │
│                    │ Memory  │                                │
│                    │(ADD-only│                                │
│                    │+双时间  │                                │
│                    │+Reflect)│                                │
│                    └────┬────┘                                │
│                         │                                    │
│                    ┌────┴─────┐                               │
│                    │EventStream│                               │
│                    └────┬─────┘                               │
│                         │                                    │
│              ┌──────────┴──────────┐                         │
│              │   Execution Layer    │                         │
│              │ (无状态Runtime+MCP)  │                         │
│              └──────────┬──────────┘                         │
│                         │                                    │
│              ┌──────────┴──────────┐                         │
│              │    Agent Pool       │                         │
│              │  (无状态执行单元)    │                         │
│              └─────────────────────┘                         │
└─────────────────────────────────────────────────────────────┘
```

### 6.2 与 v1.0 架构的对比

| 维度 | v1.0 | v1.1 |
|------|------|------|
| 核心能力数 | 5（Memory/Identity/Reflection/Goal/Planning） | 7（Identity/Memory/Self Model/Reflection/Goal/Agency/Execution） |
| 主动性 | 无 | Agency（Trigger + Decision + Initiative Queue） |
| 自我认知 | 无 | Self Model（能力认知 + 用户关系 + 行为总结） |
| 范式 | Agent + Memory | Digital Life |
| 闭环 | 开环（请求→执行→结束） | 闭环（经历→记忆→理解→反思→演化→行动→新经历） |

### 6.3 关键取舍（修订后）

| 取舍 | 决策 | 理由 |
|------|------|------|
| Agent Architecture vs Digital Life Architecture | **Digital Life Architecture** | Personal-AI 的目标是数字生命，不是更强的 Agent。Agent Architecture 无法支持 Agency 和 Self Model |
| 被动响应 vs 主动存在 | **主动存在（Agency）** | 长期连续性要求 Personal-AI 能主动追踪目标、反思经验、维护关系 |
| 5 能力 vs 7 能力 | **7 能力** | Self Model 和 Agency 是 Digital Life 的必要能力，不可省略 |
| Planning 作为核心 vs Execution 作为核心 | **Execution** | Planning 只是 Execution 的一个子阶段，Execution 涵盖从 Goal 到 Action 的完整链路 |
| Runtime 作为核心 vs 基础设施 | **基础设施** | Runtime 是 Execution 的实现细节，不是 Personal-AI 的本质特征 |

---

## 7. v1.0 → v1.1 变更摘要

### 7.1 新增内容

| 新增 | 位置 | 说明 |
|------|------|------|
| Digital Life Architecture Perspective | §1 | 重新定义 Personal-AI 的核心范式 |
| Self Model 能力设计 | §4 | 新增核心能力：我如何理解自己 |
| Agency 能力设计 | §3 | 新增核心能力：我什么时候应该主动行动 |
| Trigger System | §3.3 | Agency 的触发系统 |
| Decision Engine | §3.4 | Agency 的决策引擎 |
| Initiative Queue | §3.5 | Agency 的主动行动队列 |
| 已有研究的重新定位 | §5 | 将 v1.0 结论从"定义"降级为"组件参考" |

### 7.2 修改内容

| 修改 | v1.0 | v1.1 |
|------|------|------|
| 核心能力模型 | Memory/Identity/Reflection/Goal/Planning/Runtime | Identity/Memory/Self Model/Reflection/Goal/Agency/Execution |
| Planning 定位 | 核心能力 | Execution 的子阶段 |
| Runtime 定位 | 核心能力 | 基础设施 |
| 架构范式 | Agent + Memory | Digital Life |
| 生命周期 | 未定义 | 闭环（经历→记忆→理解→反思→演化→行动→新经历） |

### 7.3 保留内容

v1.0 的以下内容完整保留，不做修改：

- 12 个项目的深度调研（§1-§12）
- 横向对比矩阵（§13）
- P0/P1/P2 借鉴建议（§14）
- 核心发现（§15.1）
- 关键取舍（§15.3）

这些内容在 v1.1 中通过 §5 重新定位，但原始结论不变。

---

> **v1.1 修订完成**
>
> 核心变更：从 Agent Architecture 升级为 Digital Life Architecture。
>
> v1.0 的研究结论仍然有效，但定位从"Personal-AI 的定义"调整为"Personal-AI 能力组件的设计输入"。
>
> 新增 Self Model 和 Agency 两个核心能力，补全了 Digital Life 的必要条件。
>
> 原报告（v1.0）路径：`docs/research/Landscape-Report.md`
