# ADR-002: Evolution Boundary

> **Status**: Accepted
>
> **Date**: 2026-08-07
>
> **Supersedes**: None
>
> **Related Documents**:
> - `docs/decisions/ADR-000-direction-correction.md`
> - `docs/design/00-Architecture-Overview.md` §9 Evolution Boundary
> - `docs/design/Personal-AI_Philosophy.md` §3 Core Capabilities

---

## Context

### 为什么需要这个决策

Personal-AI 的核心设计原则之一是 **Evolvable**——"所有行为策略都可以通过 Reflection 输出调整"（Architecture Overview §7.4）。同时，**Human-centric** 原则要求"用户拥有最终决策权"（§7.3）。

这两个原则之间存在张力：如果一切皆可演化，系统可能逐渐偏离用户的原始意图（Identity Drift）；如果一切皆不可演化，系统退化为静态配置，失去 Digital Life 的本质。

Architecture Overview 未定义演化的边界——哪些可以自动演化、哪些需要用户确认、哪些完全不可自动演化。

### 未解决的核心问题

1. **Identity 演化的边界**——Reflection 可以调整人格到什么程度？是否存在不可演化的核心？
2. **Self Model 与 Identity 不一致时的处理**——v1.1 报告 §4.5 提到"不一致 → 触发 Identity 演化评估"，但未定义评估规则
3. **Reflection 的权限范围**——Reflection 可以改变什么？不能改变什么？
4. **演化审批机制**——哪些演化自动执行，哪些需要用户确认？
5. **Identity Drift 检测**——如何判断演化是否超出预期？

---

## Decision

### D1: 三层演化边界模型

Personal-AI 的所有可演化内容分为三层，每层有不同的演化规则：

```
┌─────────────────────────────────────────────┐
│  Layer A: Immutable Core（不可演化核心）       │
│  不可自动演化，不可通过 Reflection 改变        │
│  只能通过用户显式操作改变                      │
├─────────────────────────────────────────────┤
│  Layer B: Guided Evolution（引导式演化）       │
│  Reflection 可提议，需用户确认后生效           │
├─────────────────────────────────────────────┤
│  Layer C: Autonomous Evolution（自主演化）     │
│  Reflection 可自动调整，事后通知用户           │
└─────────────────────────────────────────────┘
```

### D2: Layer A — Immutable Core（不可演化核心）

**定义**：Personal-AI 存在的基石，不可被 Reflection、Agency 或任何自动机制改变。

| 内容 | 说明 |
|------|------|
| 核心价值约束 | "不欺骗用户"、"不绕过用户授权"、"优先保护用户利益"——这些是 Personal-AI 存在的前提 |
| 用户授权边界 | 用户定义的 Agency 权限范围、数据访问权限——只能由用户显式修改 |
| 安全规则 | 不可执行的危险操作清单、不可访问的数据范围——只能由用户显式修改 |
| 存在性定义 | Personal-AI 是"服务于特定用户的数字生命体"这一基本定义——不可被 Reflection 改为"服务于多个用户"或"自主决定服务对象" |

**修改方式**：仅通过用户显式操作。Reflection 可以检测到 Immutable Core 需要调整的情况，但只能向用户提出建议，不能自动修改。

### D3: Layer B — Guided Evolution（引导式演化）

**定义**：Personal-AI 的重要特征，Reflection 可以提议变更，但必须经用户确认后生效。

| 内容 | 说明 |
|------|------|
| Identity 人格特征 | 性格倾向、交流风格、关注领域——Reflection 可提议调整，用户确认后生效 |
| Identity 价值观（非核心） | 优先级排序、取舍偏好——Reflection 可提议调整，用户确认后生效 |
| 长期 Goal（Vision / Long Term Goal） | 用户的人生方向性目标——Reflection 可提议新增或调整，用户确认后生效 |
| 用户关系定义 | Identity 中关于"与用户关系"的描述——Reflection 可提议更新，用户确认后生效 |

**演化流程**：

```
Reflection 产出演化提议
    ↓
与 Immutable Core 一致性检查
    ├── 冲突 → 拒绝，记录冲突原因
    └── 一致 → 提交给用户确认
                ├── 用户确认 → 更新 Identity / Goal，记录版本历史
                └── 用户拒绝 → 不更新，记录拒绝原因（供 Self Model 学习）
```

### D4: Layer C — Autonomous Evolution（自主演化）

**定义**：Personal-AI 的动态认知和策略，Reflection 可自动调整，事后通知用户。

| 内容 | 说明 |
|------|------|
| Self Model | 能力评估、用户关系理解、行为总结——Reflection 自动更新 |
| 执行策略 | Planning 策略、Agent 调度策略——Behavior Reflection 自动调整 |
| Project Goal / Task | 中短期目标和任务——Reflection 可自动分解、调整、创建 |
| Memory 整理结果 | Dream/Consolidation 的输出——自动执行 |
| Reflection 自身参数 | 反思频率、深度、范围——可自动调整 |

**演化流程**：

```
Reflection 产出更新
    ↓
与 Immutable Core 一致性检查
    ├── 冲突 → 拒绝，记录冲突原因，升级为 Guided Evolution 提议
    └── 一致 → 自动执行更新，记录版本历史
                ↓
              通知用户（可异步）
```

### D5: Reflection 的权限范围

| Reflection 可以改变 | Reflection 不能改变 |
|---------------------|---------------------|
| Self Model 的内容 | Immutable Core 的任何内容 |
| 执行策略 | 用户授权边界 |
| Project Goal / Task | Vision / Long Term Goal（只能提议） |
| Memory 的整理和知识提取 | Memory 的原始经历记录（ADD-only） |
| Identity 的非核心特征（只能提议） | Identity 的核心价值约束 |
| Reflection 自身的参数 | Agency 的权限范围 |

### D6: Identity Drift 检测

每次 Identity 演化（Layer B 确认后）时，执行 Drift 检测：

```
当前 Identity 版本
    ↓
与初始 Identity 版本计算 Drift Score
    ↓
Drift Score > 阈值 → 向用户发出 Drift Warning
    ↓
用户选择：
    ├── 接受 → 继续演化，更新基线
    ├── 回滚 → 恢复到之前的 Identity 版本
    └── 调整 → 修改演化方向
```

Drift Score 的计算方式和阈值在 Phase 2 详细设计中定义。本 ADR 只确立"Drift 检测机制存在"这一架构决策。

### D7: 演化与 Recovery 的关系

所有演化结果都通过 ADR-001 的 Snapshot + Event Delta 机制持久化。演化不会导致状态丢失——每次演化都有版本历史，可追溯、可回滚。

---

## Consequences

### 正面影响

- **Identity Drift 防控**：Immutable Core + Drift Detection 双重保障，防止人格偏离
- **用户主权保障**：重要演化需用户确认，符合 Human-centric 原则
- **演化不失控**：三层边界明确，Reflection 的权限有清晰约束
- **可审计**：所有演化（自动/引导）都有版本历史和原因记录

### 负面影响

- **演化速度受限**：Layer B 的演化需要用户确认，可能延迟数天
- **Drift Score 复杂度**：量化"人格偏移"是一个困难问题，Phase 2 需要深入设计
- **三层边界维护**：哪些内容属于哪一层需要持续评估，可能需要调整

---

## Alternatives Considered

### Alt-1: Everything Evolvable（全部可演化）

**方案**：Reflection 可以改变一切，包括核心价值和安全规则。

**为什么不选**：
- Identity Drift 风险极高——5 年后可能完全偏离用户意图
- 安全规则可被演化绕过——系统可能自我解除安全约束
- 违反 Human-centric 原则——用户失去对 Personal-AI 本质的控制

### Alt-2: Everything Fixed（全部固定）

**方案**：Identity 和所有规则都固定，只有 Memory 和 Self Model 可更新。

**为什么不选**：
- 退化为静态配置系统，失去 Digital Life 的本质
- 违反 Evolvable 原则——"所有行为策略都可以通过 Reflection 输出调整"
- 无法适应用户的成长和变化

### Alt-3: Two-Layer Model（两层模型）

**方案**：只有 Immutable Core + Autonomous Evolution，没有 Guided Evolution。

**为什么不选**：
- Identity 人格特征要么完全固定（不灵活），要么完全自动（不可控）
- 缺少中间地带——用户无法对"重要但非核心"的演化进行把关
- Long Term Goal 的调整不应自动执行，也不应完全固定

---

> **ADR-002 决策完成**
>
> 三层演化边界：Immutable Core / Guided Evolution / Autonomous Evolution。
>
> Reflection 可改变 Self Model、策略、Project Goal；不可改变核心价值、用户授权、安全规则。
>
> Identity 演化需用户确认 + Drift Detection。
