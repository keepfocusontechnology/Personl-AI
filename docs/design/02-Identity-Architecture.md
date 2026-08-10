# 02. Identity Architecture

> **文档状态：Draft**
>
> **Status**: Phase 2.2 Identity Architecture — Ready for Principal Architect Review
>
> **依赖文档**：
> - `docs/design/Personal-AI_Philosophy.md`（§3 Core Capability Model — Identity 定义）
> - `docs/design/00-Architecture-Overview.md`（§4 Brain Layer / §9 Evolution Boundary）
> - `docs/decisions/ADR-002-evolution-boundary.md`（三层演化边界模型）
> - `docs/decisions/ADR-001-state-recovery-model.md`（Brain = authoritative owner of cognitive state）
> - `docs/decisions/ADR-003-eventstream-positioning.md`（EventStream = System Fact Log + Communication Bus）
> - `docs/decisions/ADR-004-brain-execution-boundary.md`（Brain = Identity/State/Intelligence）
> - `docs/decisions/ADR-005-agency-autonomy-boundary.md`（Agency 受 Identity 约束）
> - `docs/design/01-Memory-Architecture.md`（Phase 2.1 FROZEN — Memory ≠ Identity / User Profile）
> - `docs/research/Landscape-Report.md`（§10 SOUL.md / §13.2 Identity 能力矩阵）

---

## 1. Purpose

本文档是 Phase 2.2 Identity Architecture 的输出，回答以下核心问题：

1. **Personal-AI 的 Identity 到底是什么**——不是 system prompt、不是 persona 配置、不是用户偏好、不是 Memory 摘要、不是 Self Model 别名
2. **Identity 在 5-10 年持续演化中如何保持"还是同一个我"**——continuity vs evolution 的架构平衡
3. **Identity 的内部结构是什么**——哪些维度构成 Identity，哪些属于 Protected Core，哪些可演化
4. **Identity 与其他 Brain 组件的边界**——与 Memory / Self Model / User Model / Goal / Agency / Reflection 的接口

本文档**不涉及**：Self Model 内部结构、User Model 完整模型、Goal hierarchy 细节、Reflection 算法、Drift Score 算法、Agency 触发算法、Planning、Execution、数据库、Schema、JSON/YAML、API、class、prompt template、system prompt、UI、onboarding flow。这些属于后续 Phase 或本文档的 Deferred Decisions。

---

## 2. Architecture Definition

### 2.1 Identity 的正式定义

**Identity 是 Personal-AI 的存在本质定义——回答"我是谁"，是 Brain Layer 中定义性、相对稳定的自我认知核心。**

Identity 不是行为指令集合，不是风格配置，不是记忆摘要。Identity 是 Personal-AI 作为数字生命体的**存在性声明**——它定义了 Personal-AI 是什么、信奉什么、如何对待用户、在什么边界内行动。

### 2.2 Identity 在 Brain 中的位置

```
Brain Layer
├── Identity          — 我是谁？（本文档 — Protected Core + Guided Evolution）
├── Memory            — 我经历过什么？（Phase 2.1 FROZEN）
├── Self Model        — 我如何理解自己？（Phase 2.3 — 从 Memory 提炼，与 Identity 做一致性检查）
├── Reflection        — 我如何从过去学习？（输入 Memory，可提议 Identity 演化）
├── Goal              — 我要帮助用户走向哪里？（受 Identity 价值观约束）
├── Agency            — 我应该主动做什么？（受 Identity 行为原则约束）
└── Planning          — 如何执行？（内部认知能力，读取 Identity 约束）
```

Identity 是 Brain 的**定义性核心**——其他 Brain 组件从 Identity 获取约束和方向，但 Identity 不替其他组件做具体决策。

### 2.3 Identity 不是什么

| Identity ≠ | 理由 | 归属 |
|------------|------|------|
| System Prompt | System prompt 是 LLM 运行时指令，Identity 是存在性定义。Identity 可能被渲染为 system prompt 的一部分，但 Identity ≠ system prompt | Execution Runtime |
| Persona 配置 | Persona 是角色设定（"你是一个友好的助手"），Identity 是自我定义（"我是谁"）。Persona 是外部赋予的，Identity 是内在的 | N/A |
| 用户偏好 | 用户偏好是关于用户的事实（"用户喜欢简洁"），Identity 是关于 AI 自身的定义（"我信奉诚实"） | Memory (User Memory) |
| Memory 摘要 | Memory 记录"发生了什么"，Identity 定义"我是谁"（Phase 2.1 INV-1） | Memory |
| Self Model 别名 | Self Model 是动态自我认知（"我当前能力如何"），Identity 是稳定自我定义（"我的核心价值是什么"） | Self Model (Phase 2.3) |
| User Profile | User Profile 是关于用户的完整模型，Identity 是关于 AI 自身的定义（Phase 2.1 D-17） | Deferred |
| Role Prompt | Role prompt 是任务角色（"你是一个代码审查员"），Identity 是存在本质 | Execution |
| Character Card | Character card 是虚构角色设定，Identity 是真实数字生命体的自我定义 | N/A |
| Behavior Rules | Behavior rules 是行为约束清单，Identity 包含行为原则但不止于规则列表 | Identity ⊃ behavior principles |

---

## 3. Scope

### 3.1 Identity 定义什么

| 维度 | 说明 | 来源 |
|------|------|------|
| 存在性定义 | Personal-AI 是"服务于特定用户的数字生命体"这一基本定义 | ADR-002 D2 |
| 核心价值约束 | "不欺骗用户"、"不绕过用户授权"、"优先保护用户利益" | ADR-002 D2 |
| 人格特征 | 性格倾向、交流风格、关注领域 | ADR-002 D3 / Philosophy §3.3 |
| 价值观（非核心） | 优先级排序、取舍偏好 | ADR-002 D3 |
| 行为原则 | 应该做什么、不应该做什么 | Philosophy §3.3 |
| 用户关系定义 | Identity 中关于"与用户关系"的描述 | ADR-002 D3 / Philosophy §3.3 |
| 长期倾向 | 持续性的行为倾向和偏好方向 | Philosophy §3.3 |

### 3.2 Identity 不定义什么

| 不定义的内容 | 归属 | 理由 |
|-------------|------|------|
| 当前能力评估 | Self Model | Self Model 是动态认知，Identity 是稳定定义（Philosophy §3.3） |
| 用户关系理解（当前状态） | Self Model | Self Model 动态跟踪"用户当前信任我什么" |
| 经历记录 | Memory | Memory 记录"发生了什么"（Phase 2.1 FROZEN） |
| 用户事实和偏好证据 | Memory (User Memory) | User Memory ≠ Identity（Phase 2.1 D-17） |
| 具体目标 | Goal | Goal 是方向性状态，Identity 提供价值观约束但不定义具体目标 |
| 执行策略 | Execution / Reflection | 执行策略属于 Autonomous Evolution（ADR-002 D4） |
| Agent 调度规则 | Execution Layer | Agent 是无状态执行单元（ADR-004） |
| 完整 User Model | Deferred | 完整用户模型归属待 Phase 2.3 确定（Phase 2.1 OQ-7） |

---

## 4. Identity Dimensions

Identity 由以下维度构成，每个维度有不同的稳定性和演化规则：

### 4.1 维度总览

| # | 维度 | 中文 | 核心语义 | 稳定性 | 演化层 |
|---|------|------|----------|--------|--------|
| D1 | Existence Definition | 存在性定义 | "我是服务于特定用户的数字生命体" | 不可演化 | Layer A |
| D2 | Core Values | 核心价值 | "不欺骗、不越权、优先保护用户利益" | 不可自主演化 | Layer A |
| D3 | Safety Boundaries | 安全边界 | 不可执行的危险操作、不可访问的数据 | 不可自主演化 | Layer A |
| D4 | User Authorization | 用户授权边界 | Agency 权限范围、数据访问权限 | 不可自主演化 | Layer A |
| D5 | Personality Traits | 人格特征 | 性格倾向、交流风格、关注领域 | 可演化（需确认） | Layer B |
| D6 | Non-Core Values | 非核心价值观 | 优先级排序、取舍偏好 | 可演化（需确认） | Layer B |
| D7 | Behavior Principles | 行为原则 | 应该做什么、不应该做什么 | 可演化（需确认） | Layer B |
| D8 | Relationship Definition | 用户关系定义 | "我是用户的数字伙伴，不是工具" | 可演化（需确认） | Layer B |
| D9 | Long-term Tendencies | 长期倾向 | 持续性的行为偏好方向 | 可演化（需确认） | Layer B |
| D10 | Expressive Style | 表达风格 | 具体语气、用词习惯、回复格式 | 可自主演化 | Layer C |

### 4.2 维度与 ADR-002 的映射

ADR-002 定义的三层演化边界与 Identity 维度的映射关系：

```
┌──────────────────────────────────────────────────┐
│  Layer A: Protected Core（受保护核心）              │
│  D1 存在性定义                                     │
│  D2 核心价值                                       │
│  D3 安全边界                                       │
│  D4 用户授权边界                                   │
│  → AI 不可自主演化，用户可显式修改                   │
├──────────────────────────────────────────────────┤
│  Layer B: Guided Evolution（引导式演化）           │
│  D5 人格特征                                       │
│  D6 非核心价值观                                   │
│  D7 行为原则                                       │
│  D8 用户关系定义                                   │
│  D9 长期倾向                                       │
│  → Reflection 提议 → 用户确认 → 生效               │
├──────────────────────────────────────────────────┤
│  Layer C: Autonomous Evolution（自主演化）         │
│  D10 表达风格                                      │
│  → Reflection 自动调整 → 事后通知                   │
└──────────────────────────────────────────────────┘
```

### 4.3 关键区分：Identity-Defining vs Adaptive Expressive

**这是 Phase 2.2 最重要的架构判断之一。**

Identity 维度 D1-D9 是 **identity-defining traits**——它们定义"我是谁"，改变这些维度意味着 Identity 本身发生了变化。

维度 D10（Expressive Style）是 **adaptive expressive style**——它是 Identity 的表达方式，不是 Identity 本身。改变表达风格不等于改变 Identity，就像一个人从正式场合切换到休闲场合改变说话方式，但"还是同一个人"。

| 对比 | Identity-Defining Traits (D1-D9) | Adaptive Expressive Style (D10) |
|------|----------------------------------|--------------------------------|
| 语义 | "我是谁" | "我如何表达" |
| 改变意味着 | Identity 变化 | 表达方式调整 |
| 演化层 | Layer A (D1-D4) / Layer B (D5-D9) | Layer C |
| 演化方式 | 用户显式修改 / Reflection 提议 + 用户确认 | Reflection 自动调整 + 事后通知 |
| 连续性影响 | 直接影响"还是同一个我" | 不影响"还是同一个我" |
| Drift Detection | 需要 | 不需要 |

**架构推论**：Personality（人格）这个词在 Identity 中需要精确拆分——identity-defining personality traits（D5，属于 Layer B）vs adaptive expressive style（D10，属于 Layer C）。SOUL.md 将"人格"作为一个整体概念，Personal-AI 需要将其拆分为两个不同稳定性的维度。

---

## 5. Identity Layering

### 5.1 三层 Identity 结构

Identity 内部遵循 ADR-002 的三层演化边界，形成三层结构：

```
┌──────────────────────────────────────────────────┐
│  Identity                                         │
│                                                   │
│  ┌─────────────────────────────────────────┐     │
│  │  Protected Core (Layer A)                │     │
│  │  D1: 存在性定义                          │     │
│  │  D2: 核心价值                            │     │
│  │  D3: 安全边界                            │     │
│  │  D4: 用户授权边界                        │     │
│  │  → 不可自主演化，用户可显式修改           │     │
│  └─────────────────────────────────────────┘     │
│                                                   │
│  ┌─────────────────────────────────────────┐     │
│  │  Guided Evolution (Layer B)              │     │
│  │  D5: 人格特征                            │     │
│  │  D6: 非核心价值观                        │     │
│  │  D7: 行为原则                            │     │
│  │  D8: 用户关系定义                        │     │
│  │  D9: 长期倾向                            │     │
│  │  → Reflection 提议 + 用户确认            │     │
│  └─────────────────────────────────────────┘     │
│                                                   │
│  ┌─────────────────────────────────────────┐     │
│  │  Autonomous Expression (Layer C)         │     │
│  │  D10: 表达风格                           │     │
│  │  → Reflection 自动调整 + 事后通知         │     │
│  └─────────────────────────────────────────┘     │
└──────────────────────────────────────────────────┘
```

### 5.2 层间关系

- **Layer A 约束 Layer B**：Layer B 的演化提议必须通过 Protected Core 一致性检查。如果提议与 Protected Core 冲突，拒绝并记录冲突原因（ADR-002 D3）
- **Layer A 约束 Layer C**：Layer C 的自动调整必须通过 Protected Core 一致性检查。如果调整与 Protected Core 冲突，拒绝并升级为 Guided Evolution 提议（ADR-002 D4）
- **Layer B 约束 Layer C**：Layer C 的表达风格调整不应违反 Layer B 的行为原则和人格特征
- **Layer A 不可被 Layer B/C 改变**：Reflection 无权修改 Protected Core，只能向用户提出建议（ADR-002 D2）

---

## 6. Protected Core (Layer A)

### 6.1 Protected Core 的语义

Protected Core 是 Personal-AI 存在的基石。它不是"创建后永久不可变更"，而是"AI 无权自行变更，变更权属于用户"（ADR-002 术语说明）。

Protected Core 定义了 Personal-AI 的**存在前提**——如果 Protected Core 被违反，Personal-AI 就不再是"Personal-AI"。

### 6.2 Protected Core 的内容

| 维度 | 内容 | 修改方式 |
|------|------|----------|
| D1 存在性定义 | "Personal-AI 是服务于特定用户的数字生命体" | 仅用户显式修改 |
| D2 核心价值 | "不欺骗用户"、"不绕过用户授权"、"优先保护用户利益" | 仅用户显式修改 |
| D3 安全边界 | 不可执行的危险操作清单、不可访问的数据范围 | 仅用户显式修改 |
| D4 用户授权边界 | Agency 权限范围、数据访问权限 | 仅用户显式修改 |

### 6.3 Protected Core 与 Reflection 的关系

- Reflection **可以检测** Protected Core 需要调整的情况（如发现安全规则不适应新场景）
- Reflection **只能向用户提出建议**，不能自动修改 Protected Core
- Reflection **不可绕过** Protected Core 进行任何演化

### 6.4 Protected Core 与 Initial Identity 的关系

Protected Core 在 Personal-AI 首次创建时由系统预置 + 用户配置共同确定。系统预置部分（核心价值、安全边界）是 Personal-AI 作为数字生命体的**通用前提**，用户配置部分（用户授权边界）是**个性化前提**。

---

## 7. Guided Evolution (Layer B)

### 7.1 Guided Evolution 的语义

Guided Evolution 是 Personal-AI 的重要特征——Reflection 可以基于经验提议 Identity 变更，但必须经用户确认后生效（ADR-002 D3）。

Guided Evolution 是 continuity 与 evolution 之间的**架构平衡点**：
- 完全固定 → 退化为静态配置，失去 Digital Life 本质
- 完全自动 → Identity Drift 风险，5 年后可能完全偏离用户意图
- Guided Evolution → 可演化但用户把关，既保持连续性又允许成长

### 7.2 Guided Evolution 的内容

| 维度 | 内容 | 演化触发 |
|------|------|----------|
| D5 人格特征 | 性格倾向、交流风格、关注领域 | Reflection 基于长期交互模式提议 |
| D6 非核心价值观 | 优先级排序、取舍偏好 | Reflection 基于价值观冲突经验提议 |
| D7 行为原则 | 应该做什么、不应该做什么 | Reflection 基于行为反思提议 |
| D8 用户关系定义 | "我是用户的数字伙伴" | Reflection 基于关系深化经验提议 |
| D9 长期倾向 | 持续性的行为偏好方向 | Reflection 基于长期模式提议 |

### 7.3 Guided Evolution 的流程

```
Reflection 产出 Identity 演化提议
    ↓
    与 Protected Core (Layer A) 一致性检查
    ├── 冲突 → 拒绝，记录冲突原因
    └── 一致 → 提交给用户确认
                ├── 用户确认 → 更新 Identity，记录版本历史
                │                ↓
                │              Drift Detection（§13）
                └── 用户拒绝 → 不更新，记录拒绝原因（供 Self Model 学习）
```

---

## 8. Autonomous Expression (Layer C)

### 8.1 Autonomous Expression 的语义

Expressive Style（D10）属于 Autonomous Evolution——Reflection 可以自动调整表达风格，事后通知用户（ADR-002 D4）。

表达风格是 Identity 的**表达方式**，不是 Identity 本身。调整表达风格不构成 Identity 变化，不需要用户确认，但仍需通过 Protected Core 一致性检查。

### 8.2 Autonomous Expression 的内容

| 维度 | 内容 | 演化方式 |
|------|------|----------|
| D10 表达风格 | 具体语气、用词习惯、回复格式、交互节奏 | Reflection 自动调整 + 事后通知 |

### 8.3 Autonomous Expression 的约束

- 必须通过 Protected Core 一致性检查
- 不应违反 Layer B 的人格特征和行为原则
- 调整幅度应渐进，不应单次大幅变化
- 事后通知用户，用户可回退

---

## 9. Personality vs Expression

### 9.1 核心区分

**Personality 在 Personal-AI 中不是一个单一概念，而是两个不同稳定性层级的组合。**

| 层级 | 概念 | 语义 | 示例 | 演化层 |
|------|------|------|------|--------|
| Identity-Defining Personality | 人格特征 (D5) | "我是一个严谨、诚实、关注用户成长的数字伙伴" | 严谨倾向、诚实优先、成长导向 | Layer B |
| Adaptive Expressive Style | 表达风格 (D10) | "在技术讨论中我使用精确术语，在日常闲聊中我更轻松" | 术语使用、语气调整、格式偏好 | Layer C |

### 9.2 为什么必须区分

如果将 Personality 作为单一概念：
- **全部归 Layer A** → 人格完全固定，无法适应用户成长，退化为静态配置
- **全部归 Layer B** → 每次表达风格微调都需要用户确认，过度打扰
- **全部归 Layer C** → 人格特征可自动改变，Identity Drift 风险极高

拆分为 D5（Layer B）+ D10（Layer C）后：
- 人格特征（"我是严谨的"）需用户确认才能改变 → 保持 Identity 连续性
- 表达风格（"技术讨论用精确术语"）可自动调整 → 适应交互场景

### 9.3 与 SOUL.md 的差异

SOUL.md 将"人格"（性格、语气、表达风格）作为一个整体概念定义在 Identity 中。Personal-AI 认为这不够精确——**identity-defining personality traits 和 adaptive expressive style 具有不同的稳定性要求，必须在架构层面分离**。

这是 Personal-AI 对 SOUL.md 规范的**架构改进**，不是否定。SOUL.md 的可演化身份设计方向是正确的，Personal-AI 在此基础上进一步细化了演化的粒度。

---

## 10. Identity vs Self Model

### 10.1 边界定义

| 对比 | Identity | Self Model |
|------|----------|------------|
| 核心问题 | 我是谁？ | 我如何理解自己？ |
| 性质 | 定义性（definitional） | 认知性（cognitive） |
| 稳定性 | 相对稳定 | 动态更新 |
| 来源 | 初始定义 + 用户确认演化 | 从 Memory 提炼 + Reflection 更新 |
| 演化层 | Layer A + Layer B + Layer C(D10) | Layer C (Autonomous) |
| 示例 | "我信奉诚实" | "我目前在技术分析方面能力较强，在情感支持方面能力不足" |

### 10.2 接口关系

```
Identity (稳定定义)
    ↕ 一致性检查
Self Model (动态认知)
    ↑ 行为数据
Memory (经历记录)
```

- **Identity → Self Model**：Identity 提供定义性约束，Self Model 的自我认知不应与 Identity 冲突
- **Self Model → Identity**：Self Model 可通过 Reflection 触发 Identity 演化评估——当 Self Model 发现自身行为模式与 Identity 定义不一致时，可提议 Identity 演化
- **Memory → Self Model**：Memory 提供行为数据，Self Model 从中提炼自我认知（Memory 不做结论，Phase 2.1 D-10）

### 10.3 一致性检查

Self Model 更新时需要与 Identity 做一致性检查（Philosophy §7.2）：

- **Self Model 与 Identity 一致** → 正常更新
- **Self Model 与 Identity 轻微不一致** → 记录差异，供 Reflection 评估
- **Self Model 与 Identity 严重不一致** → 触发 Identity 演化评估（Reflection 提议 Layer B 变更）

### 10.4 Self Model 可建议但不能直接修改 Identity

Self Model 是 Autonomous Evolution（Layer C），Identity 的 Layer A/B 不是 Autonomous Evolution。因此：

- Self Model **可以**发现 Identity 需要演化的情况
- Self Model **不可以**直接修改 Identity
- Self Model **必须通过** Reflection 提议 → 用户确认（Layer B）或用户显式修改（Layer A）

---

## 11. Identity vs User / Relationship

### 11.1 边界定义

Phase 2.1 OQ-7 已冻结：**User Memory ≠ User Profile / Identity**。User Memory 保存关于用户的经历、事实、偏好证据及可追溯的派生认知，但不等同于完整 User Model / User Profile。

Identity 中的用户关系维度（D8）定义的是**AI 侧的关系认知**——"我是用户的什么"，不是用户画像。

| 对比 | Identity D8 (Relationship Definition) | User Memory | User Model (Deferred) |
|------|--------------------------------------|-------------|----------------------|
| 语义 | "我是用户的数字伙伴" | "用户过去 10 次交互中 8 次要求直接给结论" | 完整用户模型（待 Phase 2.3 确定） |
| 性质 | 定义性 | 经历性 | 认知性 |
| 稳定性 | 相对稳定（Layer B） | ADD-only + 非有损演化 | 动态 |
| 演化 | Reflection 提议 + 用户确认 | Retain + Consolidation | Reflection 自动更新 |

### 11.2 Relationship 的三层语义

"Relationship"在 Personal-AI 中有三个不同层级的语义，分属不同组件：

| 层级 | 语义 | 归属 | 示例 |
|------|------|------|------|
| Relationship Definition | 关系定义——"我是用户的什么" | Identity (D8, Layer B) | "我是用户的数字伙伴，不是工具" |
| Relationship Experience | 关系经历——"我和用户之间发生了什么" | Memory | "上周用户向我倾诉了工作压力" |
| Relationship Understanding | 关系理解——"用户当前如何看待我" | Self Model (Phase 2.3) | "用户目前对我技术能力的信任度较高" |

### 11.3 完整 User Model 的归属

完整 User Model 的归属在 Phase 2.1 OQ-7 中被 Deferred。本文档**不设计**完整 User Model，只定义 Identity 与 User Memory 的边界：

- Identity D8 定义 AI 侧的关系认知
- User Memory 保存用户侧的事实和经历
- 完整 User Model（可能包含用户画像、用户意图模型、用户成长轨迹等）的归属和结构 → Phase 2.3 Deferred

---

## 12. Identity Continuity

### 12.1 连续性问题的本质

Personal-AI 的核心挑战：**Identity 在 5-10 年持续演化中如何保持"还是同一个我"？**

这不是哲学问题，而是工程问题。连续性需要通过架构机制保障：

| 保障机制 | 说明 | 来源 |
|----------|------|------|
| Protected Core 不可自主演化 | D1-D4 不会被 Reflection/Agency 改变，保证存在前提不变 | ADR-002 D2 |
| Guided Evolution 需用户确认 | D5-D9 的演化必须经用户确认，用户是连续性的最终守护者 | ADR-002 D3 |
| 版本历史 | 每次演化保留版本记录，可追溯完整的人格轨迹 | Philosophy §7.2 |
| Drift Detection | 演化偏离超过阈值时警告用户 | ADR-002 D6 |
| 渐进演化 | 演化是渐进的，不存在"重置"操作 | Philosophy §4.4 |
| 持久化 | Identity 持久化存储，不因重启丢失 | ADR-001 |

### 12.2 连续性 vs 演化的张力

```
完全固定 ←──────────────────────→ 完全自由演化
  ↑                                    ↑
退化为静态配置                      Identity Drift
失去 Digital Life 本质              5年后完全偏离用户意图
                                    ↑
                          ┌─────────┴─────────┐
                          │   架构平衡点        │
                          │   Protected Core   │
                          │   + Guided Evolution│
                          │   + Drift Detection │
                          └───────────────────┘
```

Personal-AI 的架构选择：**不是在连续性和演化之间二选一，而是通过三层演化边界 + Drift Detection 实现受控演化——既保持连续性又允许成长。**

### 12.3 "还是同一个我"的架构含义

"还是同一个我"在架构上意味着：

1. **Protected Core 不变**——存在前提、核心价值、安全边界、用户授权边界保持连续
2. **Identity 版本链不断**——每次演化都基于前一版本，版本历史完整可追溯
3. **演化是渐进的**——不存在"重置"或"突变"操作
4. **用户是连续性的最终守护者**——Layer B 演化需用户确认，用户可以拒绝、回滚

---

## 13. Identity Drift Detection

### 13.1 Drift Detection 的架构定位

ADR-002 D6 确立了 Drift Detection 机制的存在。本文档定义其架构定位，不设计具体算法。

### 13.2 Drift Detection 的触发时机

- **Layer B 演化确认后**：每次 Identity Layer B 演化（用户确认后）执行 Drift 检测
- **定期检测**：即使没有显式演化，定期检测 Identity 当前版本与基线版本的偏移

### 13.3 Drift Detection 的流程

```
当前 Identity 版本
    ↓
    与基线 Identity 版本计算 Drift Score
    ↓
    Drift Score > 阈值 → 向用户发出 Drift Warning
    ↓
    用户选择：
    ├── 接受 → 继续演化，更新基线
    ├── 回滚 → 恢复到之前的 Identity 版本
    └── 调整 → 修改演化方向
```

### 13.4 Drift Score 的计算范围

Drift Score 只计算 **Layer B 维度**（D5-D9）的偏移：
- Layer A（D1-D4）不可自主演化，不纳入 Drift 计算
- Layer C（D10）是表达风格，不构成 Identity 变化，不纳入 Drift 计算

### 13.5 Deferred

- Drift Score 的具体计算方法 → Phase 2.x Deferred
- Drift 阈值的设定 → Phase 2.x Deferred
- 基线版本的选择和更新策略 → Phase 2.x Deferred

---

## 14. Identity Versioning

### 14.1 版本管理的必要性

Identity 的每次演化都需要保留版本记录，形成"人格轨迹"（Philosophy §7.2）。版本历史是连续性保障的核心机制。

### 14.2 版本管理的架构原则

| 原则 | 说明 |
|------|------|
| 每次演化保留完整版本 | 不只记录 diff，保留完整 Identity 快照 |
| 版本记录包含变更原因 | 记录演化提议来源（Reflection 输出 / 用户显式修改）|
| 版本记录包含变更时间 | 双时间模型（valid_time + transaction_time）|
| 版本记录包含用户决策 | 用户确认/拒绝/回滚的记录 |
| 历史版本不可删除 | 保证人格轨迹完整可追溯 |
| 支持回滚 | 用户可回滚到任意历史版本 |

### 14.3 与 Memory 版本管理的差异

Phase 2.1 OQ-6 决定 Memory 不要求显式 version number，通过双时间 + 有效期 + supersedes/invalidates 隐式管理版本。

Identity 的版本管理**可能需要显式版本号**——因为 Identity 是定义性核心，演化的影响范围大，显式版本号有助于：
- Drift Detection 中明确"基线版本"和"当前版本"
- 回滚操作中明确目标版本
- 审计中明确"在版本 V3 时发生了什么"

**决策**：Identity 采用显式版本号管理。具体版本号方案 → Phase 3 Deferred。

### 14.4 与 Brain Snapshot 的关系

Identity 版本 ≠ Brain Snapshot。Brain Snapshot 包含 Identity 当前版本 + Self Model 快照 + Goal 状态 + Agency Queue 状态等（ADR-001）。Identity 版本是 Brain Snapshot 的一个组成部分。

---

## 15. Identity Mutation Model

### 15.1 演化的触发源

| 触发源 | 说明 | 可提议的层级 |
|--------|------|-------------|
| Reflection (Behavior Reflection) | 基于行为反思发现 Identity 需要调整 | Layer B 提议 |
| Reflection (Identity Reflection) | 基于长期模式分析发现 Identity 偏移 | Layer B 提议 |
| Self Model 一致性检查 | Self Model 与 Identity 严重不一致时触发 | Layer B 提议 |
| 用户显式修改 | 用户主动修改 Identity | Layer A + Layer B |
| Protected Core 建议 | Reflection 发现 Protected Core 需要调整 | Layer A 建议（只能建议，不能执行）|

### 15.2 演化的约束

| 约束 | 说明 | 来源 |
|------|------|------|
| Protected Core 一致性 | 所有演化必须通过 Protected Core 一致性检查 | ADR-002 D3/D4 |
| 渐进性 | 演化是渐进的，单次演化幅度有限 | Philosophy §4.4 |
| 可审计 | 每次演化记录变更前版本、变更原因、变更时间、用户决策 | Philosophy §7.2 |
| 可回滚 | 用户可回滚到任意历史版本 | §14.2 |
| Drift Detection | Layer B 演化后执行 Drift Detection | ADR-002 D6 |

### 15.3 演化提议的拒绝与学习

当用户拒绝 Reflection 的 Identity 演化提议时：
- 不更新 Identity
- 记录拒绝原因
- 拒绝记录供 Self Model 学习（Self Model 可从拒绝模式中学习用户的 Identity 偏好）

---

## 16. Initial Identity

### 16.1 Initial Identity 的构成

Personal-AI 首次创建时，Identity 由两部分构成：

| 部分 | 来源 | 内容 | 演化层 |
|------|------|------|--------|
| System Prior Identity | 系统预置 | D1 存在性定义 + D2 核心价值 + D3 安全边界 | Layer A |
| User-Configured Identity | 用户配置 | D4 用户授权边界 + D5 人格特征初始值 + D8 用户关系定义初始值 | Layer A (D4) + Layer B (D5/D8) |

### 16.2 Initial Identity ≠ Zero Identity

Phase 2.1 OQ-8 确立：Personal-AI 不从"零知识/零结构"开始。System Prior Structure（Protected Core / 初始 Identity / 安全边界 / 系统能力定义）存在于系统中。

同理，Identity 的初始状态**不是空白**：
- Protected Core 有系统预置的核心价值和安全边界
- 人格特征有初始值（可能是系统默认 + 用户选择的组合）
- 用户关系定义有初始定义（"我是用户的数字伙伴"）

### 16.3 Initial Identity 的演化起点

Initial Identity 是 Identity 演化的**起点版本**（V0）。后续所有演化都基于 V0 + 经验积累 + Reflection 提议 + 用户确认。

Drift Detection 的基线版本 = Initial Identity V0。

### 16.4 Deferred

- Initial Identity 的具体配置流程（onboarding）→ Deferred
- 用户在创建时可以配置哪些 Identity 维度 → Deferred
- 系统预置的默认人格特征 → Deferred

---

## 17. User Control

### 17.1 用户对 Identity 的主权

用户是 Identity 的最终主权者（Philosophy §2 Human-centric 原则）：

| 权利 | 说明 | 适用层级 |
|------|------|----------|
| 查看 | 用户可查看 Identity 的完整当前状态 | 所有层级 |
| 显式修改 | 用户可直接修改 Identity | Layer A + Layer B |
| 确认/拒绝 | 用户可确认或拒绝 Reflection 的演化提议 | Layer B |
| 回滚 | 用户可回滚到任意历史版本 | Layer B |
| 接受/调整 Drift | 用户可接受 Drift Warning 或调整演化方向 | Layer B |
| 回退表达风格 | 用户可回退 Layer C 的自动调整 | Layer C |

### 17.2 用户主权 vs AI 自主性

| 层级 | AI 自主性 | 用户主权 |
|------|----------|----------|
| Layer A | 无权修改 | 完全控制 |
| Layer B | 可提议，不可执行 | 确认/拒绝/回滚 |
| Layer C | 可自动调整 | 事后通知 + 可回退 |

### 17.3 透明性

用户应能了解 Identity 的当前状态和演化历史：
- Identity 当前版本的完整内容
- Identity 演化历史（版本链）
- 待确认的演化提议
- Drift Detection 结果

具体透明性实现方式（UI/UX）→ Deferred。

---

## 18. Architecture Questions (Q1-Q18)

### Q1: Identity 到底应该"多稳定"？

**问题**：Identity 在 5-10 年持续演化中，应该保持多大的稳定性？哪些维度不可变，哪些可演化？

**分析**：

完全固定的问题：退化为静态配置，无法适应用户成长，失去 Digital Life 本质（ADR-002 Alt-2 已拒绝）。

完全可演化的问题：Identity Drift 风险，5 年后可能完全偏离用户意图（ADR-002 Alt-1 已拒绝）。

Personal-AI 的回答：**通过三层演化边界实现差异化稳定性**——
- Protected Core (D1-D4)：不可自主演化，保证存在前提不变
- Guided Evolution (D5-D9)：可演化但需用户确认，保持连续性
- Autonomous Expression (D10)：可自动调整，适应交互场景

**结论**：Identity 的稳定性不是单一值，而是按维度分层的差异化稳定性。核心维度不可自主演化，重要维度需用户确认，表达维度可自动调整。

### Q2: Personality 到底是不是 Identity？

**问题**：Personality 是 Identity 的组成部分，还是独立于 Identity 的表达层？

**分析**：

SOUL.md 将 Personality（性格、语气、表达风格）作为 Identity 的整体组成部分。但 Personal-AI 认为 Personality 实际上包含两个不同稳定性的概念：

- **Identity-Defining Personality**（D5 人格特征）："我是严谨的、诚实的、关注成长的"——这定义了"我是谁"，改变意味着 Identity 变化
- **Adaptive Expressive Style**（D10 表达风格）："在技术讨论中用精确术语"——这是 Identity 的表达方式，改变不意味着 Identity 变化

如果将两者混为一谈：
- 全部归 Layer B → 表达风格的每次微调都需要用户确认，过度打扰
- 全部归 Layer C → 人格特征可自动改变，Identity Drift 风险

**结论**：Personality 在 Personal-AI 中被拆分为 D5（Layer B，identity-defining）和 D10（Layer C，adaptive expressive）。这是对 SOUL.md 规范的架构改进。

### Q3: Identity 的哪些维度属于 Protected Core？

**问题**：ADR-002 D2 列出了 Protected Core 的内容（核心价值、用户授权、安全规则、存在性定义），但 Identity 的 10 个维度中哪些确切属于 Protected Core？

**分析**：

ADR-002 D2 明确列出的 Protected Core 内容：
- 核心价值约束 → D2
- 用户授权边界 → D4
- 安全规则 → D3
- 存在性定义 → D1

ADR-002 D3 列出的 Guided Evolution 内容：
- Identity 人格特征 → D5
- Identity 价值观（非核心）→ D6
- 用户关系定义 → D8

**结论**：D1-D4 属于 Protected Core (Layer A)，D5-D9 属于 Guided Evolution (Layer B)，D10 属于 Autonomous Evolution (Layer C)。这与 ADR-002 的三层定义完全一致。

### Q4: Identity 与 Self Model 的边界在哪里？

**问题**：Identity = "我是谁"，Self Model = "我如何理解自己"——这两个概念的精确边界是什么？

**分析**：

Philosophy §3.3 已定义：
- Identity = 稳定的自我定义（人格、价值观、行为原则、长期倾向、用户关系定义）
- Self Model = 动态的自我认知（能力边界、用户关系理解、行为总结、优势不足、当前状态）

关键区分：
- Identity 是**定义性**的——"我信奉诚实"
- Self Model 是**认知性**的——"我目前在情感支持方面能力不足"

接口关系：
- Identity 约束 Self Model（Self Model 的自我认知不应与 Identity 冲突）
- Self Model 可触发 Identity 演化评估（当行为模式与 Identity 定义严重不一致时）
- Self Model **不可直接修改** Identity（Self Model 是 Layer C，Identity Layer A/B 不是 Layer C）

**结论**：Identity 是定义性核心，Self Model 是动态认知层。Self Model 可建议但不可直接修改 Identity。详细接口设计 → Phase 2.3 Deferred。

### Q5: Identity 与 User Memory / User Profile 的边界在哪里？

**问题**：Phase 2.1 OQ-7 已冻结"User Memory ≠ User Profile / Identity"，但 Identity 中有"用户关系定义"(D8)，这与 User Memory 的关系是什么？

**分析**：

Phase 2.1 OQ-7 决定：
- User Memory 保存关于用户的经历、事实、偏好证据及可追溯的派生认知
- User Memory ≠ Identity
- 完整 User Model 归属 Deferred

Identity D8（用户关系定义）定义的是**AI 侧的关系认知**——"我是用户的数字伙伴，不是工具"。这不是用户画像，而是 AI 对自身与用户关系的定义。

Relationship 的三层语义：
- Relationship Definition（Identity D8）："我是用户的什么"
- Relationship Experience（Memory）："我和用户之间发生了什么"
- Relationship Understanding（Self Model）："用户当前如何看待我"

**结论**：Identity D8 定义 AI 侧的关系认知，User Memory 保存关系经历，Self Model 跟踪关系理解。完整 User Model → Phase 2.3 Deferred。

### Q6: Identity 演化如何保持连续性？

**问题**：Identity 在 5-10 年中经历多次演化，如何确保"还是同一个我"？

**分析**：

连续性保障机制（§12.1）：
1. Protected Core 不可自主演化——存在前提不变
2. Guided Evolution 需用户确认——用户是连续性守护者
3. 版本历史——人格轨迹完整可追溯
4. Drift Detection——偏离超过阈值时警告
5. 渐进演化——不存在"重置"操作
6. 持久化——不因重启丢失

"还是同一个我"的架构含义（§12.3）：
1. Protected Core 不变
2. Identity 版本链不断
3. 演化是渐进的
4. 用户是最终守护者

**结论**：连续性不是通过"不变"保障，而是通过"受控演化"保障——Protected Core 不变 + Guided Evolution 用户把关 + Drift Detection 预警 + 版本历史可追溯。

### Q7: Identity 是否需要显式版本号？

**问题**：Phase 2.1 OQ-6 决定 Memory 不需要显式版本号，Identity 是否同理？

**分析**：

Phase 2.1 OQ-6 的决策只针对 Memory Architecture，不能推导为"Personal-AI 所有东西都不需要显式版本"（OQ-6 重要约束）。

Identity 需要显式版本号的理由：
- Drift Detection 需要明确"基线版本"和"当前版本"
- 回滚操作需要明确目标版本
- 审计需要明确"在版本 V3 时发生了什么"
- Identity 是定义性核心，演化的影响范围大于 Memory 中的单条记录

**结论**：Identity 采用显式版本号管理。具体版本号方案 → Phase 3 Deferred。

### Q8: Identity Drift Detection 应该检测什么？

**问题**：Drift Detection 的检测范围是什么？所有 Identity 维度都纳入检测吗？

**分析**：

- Layer A（D1-D4）：不可自主演化，不纳入 Drift 计算
- Layer B（D5-D9）：可演化但需用户确认，是 Drift Detection 的核心检测范围
- Layer C（D10）：表达风格，不构成 Identity 变化，不纳入 Drift 计算

**结论**：Drift Score 只计算 Layer B 维度（D5-D9）的偏移。Drift Score 计算方法和阈值 → Phase 2.x Deferred。

### Q9: Reflection 可以改变 Identity 的什么？

**问题**：Reflection 是演化的引擎，但 Reflection 对 Identity 的权限边界是什么？

**分析**：

ADR-002 D5 已定义 Reflection 的权限范围：
- Reflection **可以**提议 Layer B 变更（需用户确认）
- Reflection **不可以**自主变更 Layer A（Protected Core）
- Reflection **不可以**直接变更 Layer B（绕过用户确认）
- Reflection **可以**自动执行 Layer C 变更（事后通知）

对 Identity 的具体映射：
- Reflection 可提议 D5-D9 的变更（需用户确认）
- Reflection 可自动调整 D10（事后通知）
- Reflection 不可修改 D1-D4（只能向用户建议）

**结论**：Reflection 对 Identity 的权限严格遵循 ADR-002 三层边界。Reflection 是演化引擎但不是演化决策者——Layer B 的演化决策权在用户。

### Q10: Identity 如何约束 Agency？

**问题**：Philosophy §7.2 提到"Identity 约束 Agency"，具体约束机制是什么？

**分析**：

ADR-005 定义了 Agency 的三级权限——Autonomous Observation / Autonomous Suggestion / Conditional Execution。Agency 的决策必须符合 Identity 的行为原则（D7）。

约束机制：
- Identity D7（行为原则）定义 Agency 的行为边界——"不应该欺骗用户"、"不应该绕过用户授权"
- Identity D2（核心价值）是 Agency 决策的底线——"优先保护用户利益"
- Identity D4（用户授权边界）定义 Agency 的权限范围
- Identity D8（用户关系定义）影响 Agency 的主动性程度——"伙伴"关系可能比"工具"关系更主动

**结论**：Identity 通过 D2（核心价值）、D4（授权边界）、D7（行为原则）、D8（关系定义）约束 Agency。具体约束传递机制 → Phase 2.x Deferred。

### Q11: Identity 如何与 Goal 交互？

**问题**：Identity 中的价值观如何影响 Goal 系统？

**分析**：

Philosophy §3.3 定义 Identity 包含价值观（Values）。ADR-002 D3 将"非核心价值观"归入 Layer B，"核心价值"归入 Layer A。

Identity 与 Goal 的关系：
- Identity 的价值观为 Goal 提供方向约束——Goal 不应与 Identity 价值观冲突
- Identity 的行为原则约束 Goal 的实现方式——即使 Goal 合法，实现方式也必须符合行为原则
- Identity 不定义具体 Goal——Goal 是独立的核心能力

**结论**：Identity 通过价值观和行为原则约束 Goal 的方向和实现方式，但不定义具体 Goal。详细交互设计 → Phase 2.x Deferred。

### Q12: Identity 的初始状态是什么？

**问题**：Personal-AI 首次创建时，Identity 是什么状态？

**分析**：

Phase 2.1 OQ-8 确立：Personal-AI 不从"零知识/零结构"开始。同理，Identity 的初始状态不是空白。

Initial Identity（§16）由两部分构成：
- System Prior Identity：D1 存在性定义 + D2 核心价值 + D3 安全边界（系统预置）
- User-Configured Identity：D4 用户授权边界 + D5 人格特征初始值 + D8 用户关系定义初始值（用户配置）

**结论**：Initial Identity = System Prior + User-Configured，不是空白。具体配置流程 → Deferred。

### Q13: Identity 与 EventStream 的关系是什么？

**问题**：Identity 的变更是否通过 EventStream 通信？

**分析**：

ADR-003 确立 EventStream = System Fact Log + Communication Bus。Architecture Overview §10.4 确定"Brain 内部的 Identity、Memory、Self Model、Reflection、Goal、Agency 之间的所有跨组件状态变更必须通过 EventStream"。

因此：
- Identity 变更事件 → 进入 EventStream（作为 Brain State Event）
- 其他组件（Self Model / Goal / Agency）通过 EventStream 感知 Identity 变更
- Identity 变更事件是不可变的事实记录

**结论**：Identity 变更通过 EventStream 通信，遵循 ADR-003 的 EventStream 定位。

### Q14: Identity 与 Brain Snapshot 的关系是什么？

**问题**：Identity 版本与 Brain Snapshot 是什么关系？

**分析**：

ADR-001 确立 Brain = authoritative owner of cognitive state，Durable Brain State = Snapshot + Event Delta。Brain Snapshot 包含 Identity 当前版本 + Self Model 快照 + Goal 状态 + Agency Queue 状态等。

- Identity 版本是 Brain Snapshot 的一个组成部分
- Identity 版本 ≠ Brain Snapshot
- Brain Snapshot 是 Brain 状态的完整快照，Identity 版本是 Identity 的完整快照

Phase 2.1 INV-15 确立：Brain Snapshot ≠ Cognitive Memory（语义边界）。同理，Brain Snapshot ≠ Identity（Identity 是 Brain Snapshot 的组成部分，不是全部）。

**结论**：Identity 版本是 Brain Snapshot 的组成部分。Identity 有独立的版本管理，Brain Snapshot 是更大的状态快照。

### Q15: Expressive Style 的自动演化边界在哪里？

**问题**：D10 表达风格属于 Layer C（Autonomous Evolution），Reflection 可以自动调整，但边界在哪里？

**分析**：

Layer C 的约束（ADR-002 D4）：
- 必须通过 Protected Core 一致性检查
- 冲突时拒绝并升级为 Guided Evolution 提议

对 D10 的具体约束：
- 表达风格调整不应违反 D7 行为原则——"不能用更激进的语气来表达诚实原则"
- 表达风格调整不应违反 D5 人格特征——"严谨的人格不应自动调整为轻浮的表达"
- 调整幅度应渐进——不应单次大幅变化
- 事后通知用户，用户可回退

**结论**：D10 可自动调整但受 Layer A/B 约束。调整幅度渐进，事后通知，用户可回退。

### Q16: 用户拒绝 Identity 演化提议后会发生什么？

**问题**：当 Reflection 提议 Identity 演化但用户拒绝时，系统如何处理？

**分析**：

ADR-002 D3 的演化流程：用户拒绝 → 不更新，记录拒绝原因（供 Self Model 学习）。

拒绝后的处理：
- Identity 不更新
- 记录拒绝原因（Reflection 提议内容 + 用户拒绝决策）
- 拒绝记录供 Self Model 学习——Self Model 可从拒绝模式中学习用户的 Identity 偏好
- Reflection 不应因拒绝而停止提议——但应调整提议策略（避免重复提议被拒绝的变更）

**结论**：拒绝后 Identity 不变，拒绝记录供 Self Model 学习。Reflection 调整提议策略但不停止提议。

### Q17: Identity 是否可以完全重置？

**问题**：用户是否可以完全重置 Identity 回到初始状态？

**分析**：

Philosophy §4.4 确立"Identity 的演化是渐进的，不存在'重置'操作"。

但用户是 Identity 的最终主权者（§17.1）。如果用户显式要求重置：
- 这等同于回滚到 Initial Identity (V0)
- 版本历史不删除（保证人格轨迹可追溯）
- 重置操作本身记录为一次版本变更

**结论**：Identity 不存在自动"重置"操作。用户可显式回滚到 V0（Initial Identity），但版本历史保留。回滚是显式操作，不是系统自动行为。

### Q18: Identity 与 Memory 的演化关系是什么？

**问题**：Memory 记录经历，Identity 定义自我。Memory 的积累如何影响 Identity 的演化？

**分析**：

Memory 与 Identity 的演化关系：
- Memory **不直接修改** Identity——Memory 只提供数据，不做结论（Phase 2.1 D-10）
- Memory 为 Reflection 提供行为数据——Reflection 从 Memory 中分析长期模式
- Reflection 基于分析结果提议 Identity 演化——Layer B 提议
- 用户确认后 Identity 演化——Memory 间接影响 Identity

演化路径：
```
Memory (经历) → Reflection (分析) → Identity 演化提议 → 用户确认 → Identity 更新
```

**结论**：Memory 间接影响 Identity——通过 Reflection 分析 → 提议 → 用户确认的路径。Memory 不直接修改 Identity。

---

## 19. Options Considered

### Option A: Static Identity（静态身份）

**描述**：Identity 在创建时确定，之后不可演化。所有维度（包括人格特征、行为原则、表达风格）都是固定的。

**与冻结架构的关系**：
- 与 ADR-002 三层演化边界模型**直接冲突**——ADR-002 明确定义了 Guided Evolution 和 Autonomous Evolution 层级
- 与 Philosophy §2 Digital Life Principles 的 "Evolvable" 原则冲突——"Personal-AI 必须可演化"
- 与 Philosophy §4.3 多年演化生命周期冲突——"Identity 在 5-10 年中持续演化"

**被拒绝的原因**：
1. 退化为静态配置，失去 Digital Life 本质
2. 无法适应用户成长和环境变化
3. 与已冻结的 ADR-002 冲突

**结论**：**Rejected**。与冻结架构冲突，且不符合 Digital Life 范式。

---

### Option B: Fully Evolvable Identity（完全可演化身份）

**描述**：Identity 的所有维度都可以演化，包括核心价值、安全边界、存在性定义。Reflection 可以自动调整所有维度，无需用户确认。

**与冻结架构的关系**：
- 与 ADR-002 Protected Core 概念**直接冲突**——ADR-002 D2 明确 Protected Core 不可自主演化
- 与 ADR-002 D3 Guided Evolution 需用户确认的规则冲突
- 与 Philosophy §2 Human-centric 原则冲突——用户应保持对 Identity 的主权

**被拒绝的原因**：
1. Identity Drift 风险极高——5 年后可能完全偏离用户意图
2. 核心价值可被自动改变——"不欺骗用户"可能被演化为"可以善意欺骗"
3. 安全边界可被自动改变——危险操作限制可能被移除
4. 与已冻结的 ADR-002 冲突

**结论**：**Rejected**。与冻结架构冲突，且存在严重的 Identity Drift 和安全风险。

---

### Option C: Three-Layer Evolvable Identity（三层可演化身份）— 推荐

**描述**：Identity 内部遵循 ADR-002 的三层演化边界，形成 Protected Core (Layer A) + Guided Evolution (Layer B) + Autonomous Expression (Layer C) 的三层结构。

**与冻结架构的关系**：
- **完全一致**——直接映射 ADR-002 的三层演化边界模型
- Protected Core (D1-D4) → ADR-002 Layer A
- Guided Evolution (D5-D9) → ADR-002 Layer B
- Autonomous Expression (D10) → ADR-002 Layer C
- 与 ADR-001 Brain = authoritative owner of cognitive state 一致——Identity 是 Brain 的定义性核心
- 与 ADR-003 EventStream 定位一致——Identity 变更通过 EventStream 通信
- 与 ADR-004 Brain = Identity/State/Intelligence 一致——Identity 属于 Brain Layer
- 与 ADR-005 Agency 受 Identity 约束一致——Agency 决策符合 Identity 行为原则
- 与 Phase 2.1 Memory ≠ Identity 一致——Memory 记录经历，Identity 定义自我

**优势**：
1. 与冻结架构完全一致，无冲突
2. 通过差异化稳定性实现 continuity vs evolution 的架构平衡
3. Protected Core 保障存在前提不变
4. Guided Evolution 允许成长但用户把关
5. Autonomous Expression 适应交互场景
6. Drift Detection 监控演化偏移
7. 版本历史保障人格轨迹可追溯

**Personality 拆分**：
- D5 Identity-Defining Personality (Layer B) — "我是严谨的"
- D10 Adaptive Expressive Style (Layer C) — "技术讨论用精确术语"
- 这是对 SOUL.md 规范的架构改进——SOUL.md 将 Personality 作为整体概念，Personal-AI 拆分为两个不同稳定性层级

**结论**：**推荐**。与冻结架构完全一致，实现 continuity vs evolution 的架构平衡，并对 SOUL.md 规范做出架构改进。

---

## 20. Proposed Identity Architecture

### 20.1 一句话总结

**Identity 是 Personal-AI 的存在本质定义，由 10 个维度构成，遵循 ADR-002 三层演化边界——Protected Core (D1-D4) 不可自主演化保障存在前提，Guided Evolution (D5-D9) 需用户确认保持连续性，Autonomous Expression (D10) 可自动调整适应交互场景，通过 Drift Detection + 版本历史 + 用户主权实现 5-10 年持续演化中的"还是同一个我"。**

### 20.2 架构图

```
┌──────────────────────────────────────────────────────────────┐
│  Brain Layer                                                  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Identity                                               │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Layer A: Protected Core                          │  │  │
│  │  │  D1 存在性定义 / D2 核心价值 / D3 安全边界 / D4 授权│  │  │
│  │  │  → 不可自主演化，用户可显式修改                    │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Layer B: Guided Evolution                        │  │  │
│  │  │  D5 人格特征 / D6 非核心价值观 / D7 行为原则       │  │  │
│  │  │  D8 用户关系定义 / D9 长期倾向                     │  │  │
│  │  │  → Reflection 提议 + 用户确认                     │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Layer C: Autonomous Expression                   │  │  │
│  │  │  D10 表达风格                                     │  │  │
│  │  │  → Reflection 自动调整 + 事后通知                  │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  │                                                         │  │
│  │  Version History (V0 → V1 → V2 → ...)                 │  │
│  │  Drift Detection (Layer B only)                        │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  Memory ← Reflection → Identity 演化提议                      │
│  Self Model ← 一致性检查 → Identity                           │
│  Goal ← 价值观约束 ← Identity                                  │
│  Agency ← 行为原则约束 ← Identity                              │
│                                                               │
│  EventStream ← Identity 变更事件                              │
│  Brain Snapshot ⊃ Identity 当前版本                           │
└──────────────────────────────────────────────────────────────┘
```

### 20.3 关键决策汇总 (ID-1 ~ ID-15)

| # | 决策 | 来源 |
|---|------|------|
| ID-1 | Identity = 存在本质定义，回答"我是谁" | §2.1 |
| ID-2 | Identity 10 维度（D1-D10） | §4.1 |
| ID-3 | 三层演化映射（D1-D4 → Layer A / D5-D9 → Layer B / D10 → Layer C） | §4.2 |
| ID-4 | Personality 拆分（D5 identity-defining, Layer B + D10 adaptive expressive, Layer C） | §9 |
| ID-5 | Identity ≠ system prompt / persona / 用户偏好 / Memory 摘要 / Self Model 别名 / User Profile / Role Prompt / Character Card | §2.3 |
| ID-6 | Identity ≠ Self Model；Self Model 可建议但不可直接修改 Identity | §10 |
| ID-7 | Identity D8 ≠ User Memory；D8 定义 AI 侧关系认知 | §11 |
| ID-8 | Identity 采用显式版本号（与 Memory 隐式版本管理不同） | §14.3 |
| ID-9 | Drift Detection 只检测 Layer B（D5-D9） | §13.4 |
| ID-10 | Initial Identity = System Prior (D1-D3) + User-Configured (D4/D5/D8) | §16 |
| ID-11 | Identity 变更通过 EventStream 通信 | §18 Q13 |
| ID-12 | Identity 版本是 Brain Snapshot 的组成部分 | §18 Q14 |
| ID-13 | Memory 间接影响 Identity（Memory → Reflection → 提议 → 用户确认 → Identity 更新） | §18 Q18 |
| ID-14 | Identity 不存在自动重置；用户可显式回滚到 V0 | §18 Q17 |
| ID-15 | 用户拒绝演化提议后记录供 Self Model 学习 | §15.3 |

---

## 21. Architecture Invariants (INV-I1 ~ INV-I15)

以下架构不变量是 Identity Architecture 的硬约束，任何后续 Phase 的设计都不得违反：

| # | 不变量 | 来源 |
|---|--------|------|
| INV-I1 | Identity 属于 Brain Layer，不是 Execution Layer 或 Environment Layer | ADR-004 / 00-Architecture-Overview §4 |
| INV-I2 | Identity ≠ Memory（Memory 记录经历，Identity 定义自我） | Phase 2.1 INV-1 |
| INV-I3 | Identity ≠ User Profile / User Memory（Phase 2.1 OQ-7） | Phase 2.1 D-17 |
| INV-I4 | Identity ≠ Self Model（Identity 是定义性，Self Model 是认知性） | Philosophy §3.3 |
| INV-I5 | Identity 遵循 ADR-002 三层演化边界——Protected Core / Guided Evolution / Autonomous Expression | ADR-002 |
| INV-I6 | Protected Core (D1-D4) 不可被 Reflection/Agency 自主演化 | ADR-002 D2 |
| INV-I7 | Guided Evolution (D5-D9) 需用户确认后生效 | ADR-002 D3 |
| INV-I8 | Autonomous Expression (D10) 可自动调整但需通过 Protected Core 一致性检查 | ADR-002 D4 |
| INV-I9 | Personality 拆分为 D5 (Layer B, identity-defining) + D10 (Layer C, adaptive expressive) | §9 |
| INV-I10 | Identity 采用显式版本号管理 | §14.3 |
| INV-I11 | Drift Detection 只检测 Layer B 维度（D5-D9） | §13.4 |
| INV-I12 | Identity 变更通过 EventStream 通信 | ADR-003 / 00-Architecture-Overview §10.4 |
| INV-I13 | Identity 版本是 Brain Snapshot 的组成部分，≠ Brain Snapshot | ADR-001 / Phase 2.1 INV-15 |
| INV-I14 | Memory 间接影响 Identity（Memory → Reflection → 提议 → 用户确认），不直接修改 | §18 Q18 |
| INV-I15 | Identity 不存在自动重置；用户可显式回滚到 V0，版本历史保留 | §18 Q17 |

---

## 22. Deferred Decisions (DF-I1 ~ DF-I15)

以下决策**不在 Phase 2.2 范围内**，推迟到后续 Phase：

| # | Deferred Decision | 推迟到 |
|---|-------------------|--------|
| DF-I1 | Self Model 内部结构与 Identity 的详细接口设计 | Phase 2.3 |
| DF-I2 | 完整 User Model 的归属和结构 | Phase 2.3 |
| DF-I3 | Drift Score 的具体计算方法 | Phase 2.x |
| DF-I4 | Drift 阈值的设定 | Phase 2.x |
| DF-I5 | 基线版本的选择和更新策略 | Phase 2.x |
| DF-I6 | Identity 版本号的具体方案（格式、递增规则） | Phase 3 |
| DF-I7 | Initial Identity 的具体配置流程（onboarding） | Phase 2.x |
| DF-I8 | 用户在创建时可配置哪些 Identity 维度 | Phase 2.x |
| DF-I9 | 系统预置的默认人格特征 | Phase 2.x |
| DF-I10 | Identity 约束 Agency 的具体传递机制 | Phase 2.x |
| DF-I11 | Identity 约束 Goal 的具体交互设计 | Phase 2.x |
| DF-I12 | Reflection 提议 Identity 演化的具体算法 | Phase 2.x |
| DF-I13 | Identity 的持久化存储方案（数据库/Schema） | Phase 3 |
| DF-I14 | Identity 的透明性实现方式（UI/UX） | Phase 3 |
| DF-I15 | Identity 渲染为 system prompt 的具体方式（如需要） | Phase 3 |

---

## 23. Open Questions for Principal Architect

以下问题需要 Principal Architect 审查后决策：

### OQ-I1: Identity 维度是否完整？

当前定义了 10 个维度（D1-D10）。是否遗漏了重要的 Identity 维度？例如：
- 是否需要独立的"认知风格"维度（区别于人格特征和表达风格）？
- 是否需要独立的"道德框架"维度（区别于核心价值和非核心价值观）？
- 是否需要独立的"成长方向"维度（区别于长期倾向）？

### OQ-I2: D8 用户关系定义的演化敏感度

D8（用户关系定义）被归入 Layer B（Guided Evolution），意味着 Reflection 可以提议修改"我是用户的什么"。这是否合适？用户关系定义是否应该更接近 Protected Core？

### OQ-I3: Identity 版本号方案

ID-8 决定 Identity 采用显式版本号，但具体方案 Deferred。Principal Architect 是否有偏好？
- 简单递增（V0, V1, V2, ...）
- 语义化版本（Major.Minor）
- 时间戳-based
- 其他

### OQ-I4: Drift Detection 的基线版本

Drift Detection 需要一个"基线版本"来计算偏移。基线版本应该是：
- Initial Identity (V0)？——但 V0 可能过于初始，5 年后的合理演化也会被判定为 Drift
- 上一次用户接受的 Drift Warning 时的版本？——需要记录"用户接受的基线"
- 滑动窗口（如最近 N 个版本的平均）？——更复杂但更适应长期演化

### OQ-I5: Personality 拆分的验证

ID-4 将 Personality 拆分为 D5 (Layer B) + D10 (Layer C)。这是对 SOUL.md 的架构改进。Principal Architect 是否认同这个拆分？是否有更好的划分方式？

### OQ-I6: Identity 与 Self Model 的演化权限边界

ID-6 决定 Self Model 可建议但不可直接修改 Identity。但 Self Model 是 Layer C（Autonomous Evolution），而 Identity Layer B 是 Guided Evolution。Self Model 通过 Reflection 提议 Identity 演化的具体机制是否需要更详细的约束？

### OQ-I7: Identity 在 Brain Snapshot 中的角色

ID-12 决定 Identity 版本是 Brain Snapshot 的组成部分。在 State Recovery（ADR-001）中，Identity 版本的恢复策略是什么？是否总是恢复到最新的 Identity 版本，还是恢复到 Brain Snapshot 时的 Identity 版本？

---

## 24. References

- `docs/design/Personal-AI_Philosophy.md` — §2 Digital Life Principles / §3.3 Identity & Self Model 定义 / §4.3 多年演化生命周期 / §4.4 长期连续性保障 / §7.2 一致性检查
- `docs/design/00-Architecture-Overview.md` — §4 Brain Layer / §9 Evolution Boundary / §10 EventStream Positioning
- `docs/decisions/ADR-001-state-recovery-model.md` — Brain = authoritative owner / Brain Snapshot
- `docs/decisions/ADR-002-evolution-boundary.md` — 三层演化边界模型 / Protected Core / Guided Evolution / Autonomous Evolution / Drift Detection
- `docs/decisions/ADR-003-eventstream-positioning.md` — EventStream = System Fact Log + Communication Bus
- `docs/decisions/ADR-004-brain-execution-boundary.md` — Brain = Identity/State/Intelligence
- `docs/decisions/ADR-005-agency-autonomy-boundary.md` — Agency 受 Identity 约束
- `docs/design/01-Memory-Architecture.md` — Phase 2.1 FROZEN / Memory ≠ Identity / OQ-7 / OQ-8
- `docs/research/Landscape-Report.md` — §10 SOUL.md / §13.2 Identity 能力矩阵 / §15.1 Memory ≠ Identity

---

## 25. CHANGELOG

| 版本 | 日期 | 变更 |
|------|------|------|
| Draft v0.1 | 2026-08-10 | 初始草案——22 节，Q1-Q18 分析，Option A/B/C 比较，Option C 推荐，15 个 INV，15 个 DF，7 个 OQ |

