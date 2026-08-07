# ADR-005: Agency Autonomy Boundary

> **Status**: Accepted
>
> **Date**: 2026-08-07
>
> **Supersedes**: None
>
> **Related Documents**:
> - `docs/decisions/ADR-000-direction-correction.md`
> - `docs/decisions/ADR-002-evolution-boundary.md`
> - `docs/design/00-Architecture-Overview.md` §4.1 Brain Layer — Agency
> - `docs/design/Personal-AI_Philosophy.md` §3 Agency
> - `docs/research/Personal-AI Landscape Report v1.1.md` §3 Agency 能力设计

---

## Context

### 为什么需要这个决策

Architecture Overview §4.1 定义 Agency 为"Trigger System + Decision Engine + Initiative Queue"，§7.3 Human-centric 原则声明"AI Initiative 的高风险行动必须用户确认"。

v1.1 报告 §3.6 定义了 Agency 的边界约束（Identity 约束、用户主权、透明性、频率控制、可关闭），但缺少可操作的权限分级：

- Agency 可以自动观察什么？
- Agency 可以自动建议什么？
- Agency 可以自动执行什么？
- 哪些行为必须用户确认？
- Low-risk 和 High-risk 的定义维度是什么？

没有明确的权限边界，Agency 要么过于保守（退化为被动响应），要么过于激进（违反用户主权）。

### 未解决的核心问题

1. **Agency 的三个权限层级**——观察、建议、执行的边界
2. **Low-risk vs High-risk 的定义**——风险分级的维度
3. **自动执行的边界**——哪些行动可以自动执行
4. **用户确认的机制**——哪些行动必须用户确认
5. **Agency 关闭后的系统行为**——降级模式

---

## Decision

### D1: Agency 三级权限模型

Agency 的主动性分为三个权限层级，每层有不同的自主度：

```
┌─────────────────────────────────────────────┐
│  Level 1: Autonomous Observation（自主观察）  │
│  无需用户许可，自动执行                        │
├─────────────────────────────────────────────┤
│  Level 2: Autonomous Suggestion（自主建议）   │
│  无需用户许可，自动产生建议，但只呈现不执行     │
├─────────────────────────────────────────────┤
│  Level 3: Conditional Execution（条件执行）   │
│  Low-risk 自动执行 + 事后通知                  │
│  High-risk 必须用户确认                        │
└─────────────────────────────────────────────┘
```

### D2: Level 1 — Autonomous Observation（自主观察）

**定义**：Agency 可以自主观察系统状态和外部环境，无需用户许可。

| Agency 可以做 | 说明 |
|---------------|------|
| 监控 Memory 变化 | 检测新经历写入、记忆冲突、记忆过期 |
| 监控 Goal 状态 | 检测目标完成、目标过期、目标进度 |
| 监控 Self Model 变化 | 检测能力评估变化、用户关系变化 |
| 监控外部事件 | 检测日历事件、文件变化、外部通知 |
| 触发 Reflection | 在条件满足时触发 Memory Reflection 或 Behavior Reflection |
| 更新 Self Model | 根据 Reflection 输出更新自我认知（ADR-002 Layer C） |
| 记录 Trigger 和 Decision | 所有触发和决策记录到 EventStream |

**约束**：观察行为本身不产生任何外部影响。观察结果只进入 Brain 内部，不通知用户、不执行行动。

### D3: Level 2 — Autonomous Suggestion（自主建议）

**定义**：Agency 可以自主产生建议并呈现给用户，无需事前许可，但建议只呈现不执行。

| Agency 可以做 | 说明 |
|---------------|------|
| 产生 Suggestion | "我注意到你的项目进度落后，是否需要调整计划？" |
| 产生 Reminder | "明天早上 10 点有会议" |
| 产生 Notification | "目标已完成" / "检测到目标冲突" |
| 呈现给用户 | 通过用户界面呈现建议，等待用户响应 |

**约束**：
- 建议只呈现，不执行——即使用户忽略建议，也不会有任何行动发生
- 频率控制——建议有频率限制，避免过度打扰（频率参数由 ADR-002 Layer C 自主调整）
- 建议必须可追溯——每条建议记录触发原因、上下文快照、决策推理

### D4: Level 3 — Conditional Execution（条件执行）

**定义**：Agency 可以发起行动执行，但根据风险等级决定是否需要用户确认。

#### D4.1: Low-risk Action（低风险行动）

**定义**：可逆的、影响范围有限的、不涉及外部系统的行动。

| Low-risk Action 示例 | 说明 |
|---------------------|------|
| Memory Dream/Consolidation | 整理记忆，不删除原始记录，可追溯 |
| Self Model 更新 | 更新自我认知，可回滚 |
| Reflection 执行 | 执行反思过程，不产生外部影响 |
| Project Goal / Task 调整 | 调整中短期目标，不涉及 Vision / Long Term Goal |
| 执行策略调整 | 调整 Planning 策略、Agent 调度策略 |
| Memory 检索 | 检索相关记忆，为用户响应准备上下文 |

**执行方式**：自动执行，事后通知用户（通知可通过 Level 2 的 Notification 机制）。

#### D4.2: High-risk Action（高风险行动）

**定义**：不可逆的、影响范围大的、涉及外部系统的、或涉及 Identity/Goal 核心变更的行动。

| High-risk Action 示例 | 说明 |
|----------------------|------|
| 发送消息给用户联系人 | 不可撤回的外部通信 |
| 修改外部文件 | 不可逆的外部操作 |
| 创建/修改/删除日历事件 | 影响用户日程 |
| 调用外部 API 执行操作 | 不可逆的外部系统操作 |
| Identity 人格特征变更 | ADR-002 Layer B，需用户确认 |
| Vision / Long Term Goal 变更 | ADR-002 Layer B，需用户确认 |
| 用户关系定义变更 | ADR-002 Layer B，需用户确认 |
| Agency 权限范围变更 | 涉及 Agency 自身权限，必须用户确认 |

**执行方式**：必须用户确认后执行。用户可以确认、拒绝、修改后确认。

#### D4.3: 风险分级维度

| 维度 | Low-risk | High-risk |
|------|----------|-----------|
| 可逆性 | 可回滚/可撤销 | 不可逆 |
| 影响范围 | Brain 内部 | 外部系统 / 用户关系 |
| 涉及层级 | ADR-002 Layer C（自主演化） | ADR-002 Layer B（引导式演化）或外部操作 |
| 数据影响 | 不修改原始数据 | 修改外部数据或不可恢复地修改内部数据 |
| 用户感知 | 用户可能不会注意到 | 用户会明确感知到 |

### D5: 用户确认机制

High-risk Action 的用户确认流程：

```
Agency 产生 High-risk Initiative
    ↓
进入 AI Initiative Queue（状态：pending）
    ↓
通知用户（Level 2 Notification）
    ↓
用户选择：
    ├── 确认 → Initiative 状态变为 approved → 进入 Execution
    ├── 拒绝 → Initiative 状态变为 rejected → 记录拒绝原因
    ├── 修改 → 用户修改 Initiative 内容 → 重新评估风险等级
    └── 忽略 → Initiative 超时过期（expires_at）→ 记录过期
```

**超时处理**：每个 High-risk Initiative 有过期时间。过期后自动标记为 expired，不执行。过期时间由 Decision Engine 根据行动类型决定。

### D6: Agency 关闭后的降级模式

用户可以关闭 Agency 的主动行动能力（§7.3 Human-centric）。关闭后的系统行为：

| 能力 | Agency 开启 | Agency 关闭 |
|------|-------------|-------------|
| 自主观察（Level 1） | 正常运转 | **仍然运转**——观察是内部行为，不影响用户 |
| 自主建议（Level 2） | 正常运转 | **关闭**——不产生主动建议 |
| 条件执行（Level 3） | 正常运转 | **关闭**——不产生主动行动 |
| 用户请求响应 | 正常运转 | **仍然运转**——用户请求不受 Agency 开关影响 |
| Reflection | 正常运转 | **仍然运转**——Reflection 是内部学习，不受 Agency 开关影响 |
| Self Model 更新 | 正常运转 | **仍然运转**——自我认知更新是内部行为 |
| Goal 追踪 | 正常运转 | **仍然运转**——Goal 状态追踪是内部行为 |

**原则**：关闭 Agency 只关闭"对外主动性"（建议和行动），不关闭"内部学习"（观察、反思、自我认知）。Personal-AI 仍然在学习和成长，只是不主动表达和行动。

### D7: Agency 边界约束

Agency 的所有行为受以下约束：

| 约束 | 来源 | 说明 |
|------|------|------|
| Identity 约束 | ADR-002 Layer A | Agency 的所有决策必须符合 Immutable Core |
| 用户授权边界 | ADR-002 Layer A | Agency 不能超越用户定义的权限范围 |
| 频率控制 | 本 ADR D3 | 建议和通知有频率限制 |
| 透明性 | Architecture Overview §7.5 | 所有 Agency 行为可追溯、可审计 |
| 可关闭 | 本 ADR D6 | 用户可以关闭 Agency 的对外主动性 |

---

## Consequences

### 正面影响

- **用户主权保障**：High-risk Action 必须用户确认，用户始终拥有最终决策权
- **Agency 不失控**：三级权限 + 风险分级，Agency 的自主性有清晰边界
- **降级可用**：关闭 Agency 后系统仍然学习和成长，不退化为静态系统
- **可审计**：所有 Agency 行为（观察、建议、行动）通过 EventStream 记录

### 负面影响

- **High-risk 延迟**：需要用户确认的行动可能延迟数小时到数天
- **风险分级复杂度**：某些行动的风险等级不明确（如"发送一封低重要性邮件"），需要 Phase 2 细化
- **频率参数调优**：建议频率需要根据用户反馈持续调整

---

## Alternatives Considered

### Alt-1: Full Autonomy（完全自主）

**方案**：Agency 可以自主执行所有行动，包括 High-risk。

**为什么不选**：
- 违反 Human-centric 原则——用户失去对不可逆操作的控制
- 信任风险——一次错误的自主行动可能破坏用户对 Personal-AI 的信任
- 安全风险——不可逆的外部操作（如发送消息）可能造成实际损害

### Alt-2: No Autonomy（无自主性）

**方案**：所有 Agency 行动都需要用户确认，包括 Low-risk。

**为什么不选**：
- Agency 退化为通知系统——所有行动都需要用户参与，失去主动性
- 用户负担过重——频繁的确认请求会导致"确认疲劳"
- 违反 Digital Life 范式——退化为被动响应系统

### Alt-3: Binary Risk Model（二元风险模型）

**方案**：只有 Low-risk 和 High-risk 两级，没有 Observation / Suggestion / Execution 的权限分层。

**为什么不选**：
- 观察和建议不需要风险分级——它们不产生外部影响
- 混淆"权限层级"和"风险等级"——权限层级定义"能不能做"，风险等级定义"做了需不需要确认"
- 缺少降级模式的细粒度控制——关闭 Agency 时无法区分"停止建议"和"停止行动"

---

> **ADR-005 决策完成**
>
> Agency 三级权限：自主观察 / 自主建议 / 条件执行。
>
> Low-risk 自动执行 + 事后通知；High-risk 必须用户确认。
>
> 关闭 Agency 只关闭对外主动性，内部学习继续运转。
