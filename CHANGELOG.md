# Changelog

本文件记录 Personal-AI 项目的所有版本变更。

格式参考 [Keep a Changelog](https://keepachangelog.com/)。

---

## [0.4.0] — 2026-08-10

### Phase 2.1 Memory Architecture Freeze

**Architecture Design Phase 2.1 — FROZEN**

`docs/design/01-Memory-Architecture.md` 经 Principal Architect Final Review 后从 Proposed 标记为 Accepted (FROZEN)。Phase 2.1 Memory Architecture 冻结，不再继续打磨。

### Changed

- `01-Memory-Architecture.md` 状态：Proposed → **Accepted (FROZEN)**
- Memory 结构术语：Raw + Derived + User Control Layer → **Raw + Derived cognitive layers + User Control Plane**
- 架构不变量：12 → **15**（新增 INV-13 Memory 不要求显式版本号 / INV-14 Context Condensation ≠ Memory Consolidation / INV-15 Brain Snapshot ≠ Cognitive Memory 语义边界）

### Added

- Phase 2.1 Memory Architecture Freeze 记录（本文件此条目）

### Phase 2.1 Key Decisions

- **D-2**: Memory 采用两层认知层 + 用户控制面（Raw Layer ADD-only + Derived Layer 非有损演化 + User Control Plane）
- **D-11~D-18**: OQ-1~OQ-8 全部 Resolved（Community / Transient Context / Condenser Boundary / Event Memory / Snapshot Boundary / Versioning / User Memory Boundary / Bootstrap）
- **关键边界**：
  - EventStream → cognitive derivation → Event/Episodic Memory（separate but traceable）
  - Context Condensation → Brain Context Assembly（≠ Memory Consolidation → Reflection → Memory）
  - Brain Snapshot ≠ Cognitive Memory（语义边界；物理拓扑 Phase 3 Deferred）
  - User Memory ≠ User Profile / Identity（完整 User Model 归属 Phase 2.2/2.3 Deferred）
  - Memory Bootstrap = no personal experiential history（≠ zero-knowledge system）

### Architecture Status

```
Architecture Design Phase 1.5        — FROZEN
        ↓
Architecture Design Phase 2.1 Memory — FROZEN
        ↓
Architecture Design Phase 2.2 Identity（待启动）
```

---

## [0.3.0] — 2026-08-07

### Phase 1.5 Architecture Freeze

**Architecture Design Phase 1.5 — FROZEN**

ADR-001~005 经 Principal Architect Review 后从 Proposed 标记为 Accepted。Phase 1.5 架构主干冻结，不再继续打磨。

### Changed

- ADR-001~005 状态：Proposed → **Accepted**
- README 同步至当前架构状态：
  - 五大核心能力 → 七大核心能力（Identity / Memory / Self Model / Reflection / Goal / Agency / Execution）
  - 新增三层架构图（Brain Layer / Execution Layer / Environment Layer）
  - 新增核心闭环描述
  - 新增 ADR 状态表
  - 当前阶段标记为 Phase 1.5 — FROZEN

### Added

- Phase 1.5 Architecture Review / Freeze 记录（本文件此条目）

### Architecture Status

```
Architecture Design Phase 1.5
        ↓
      FROZEN
        ↓
Architecture Design Phase 2（待启动）
```

---

## [0.2.0] — 2026-08-07

### Architecture Consistency Reconciliation

对 ADR-001~005 和 Architecture Overview 进行一致性修订，修复 7 个 Blocking Inconsistencies。

### Resolved Inconsistencies

1. **Planning 层级冲突**：Planning 从 Execution Layer 移到 Brain Layer（Brain 内部认知能力，不是第七个核心能力）
2. **State Recovery 表述冲突**：统一为 `Brain = authoritative owner of cognitive state` + `Recovery authority = Snapshot + Event Delta`
3. **Memory vs Brain Persistence 边界**：明确 Memory = cognitive/experiential memory；Brain Persistence 范围大于 Memory
4. **Immutable Core 表述冲突**：重命名为 Protected Core（non-self-evolvable, not permanently unchangeable by user）
5. **Agency Observation 权限措辞**：改为 `在用户已授予的数据访问权限范围内，无需逐次确认`
6. **Execution "无状态"措辞**：统一为 `durable-state-free`（不持有权威长期状态，允许运行期临时状态）
7. **Cognitive vs Operational Decision 边界**：Cognitive Decision → Brain；Operational Decision → Execution

### Changed

- ADR-001~005 状态：Accepted → Proposed（Agent 无权标记 Accepted）
- ADR-002 增加 Goal provenance Phase 2 备注
- Architecture Overview 全文同步修正（架构图 / §3 / §4 / §5 / §6 / §8 / §9 / 附录 B）

---

## [0.1.0] — 2026-08-07

### Added

- 创建项目目录结构 `Personal-AI/`
  - `docs/research/` — 调研文档
  - `docs/design/` — 架构设计文档
  - `docs/decisions/` — 架构决策记录（ADR）
- 创建 `README.md` — 项目定位与文档结构说明
- 创建 `CHANGELOG.md` — 版本记录（本文件）

### Research

- 完成 **Personal-AI Landscape Report**（975 行）
  - 调研 12 个项目：Codex CLI / Claude Code / OpenHands / Aider / Goose / KaijiBot / Mem0 / Graphiti / Hindsight / SOUL.md / LangGraph / Mastra
  - 每个项目 16 个评估维度（14 基础 + 适配难度 + 长期风险）
  - 横向对比矩阵：功能对比 + 五大核心能力矩阵
  - 借鉴建议分层：P0 核心研究（7 项）/ P1 值得吸收（10 项）/ P2 持续观察（5 项）
  - 结论：5 项核心发现 + 架构建议 + 6 项关键取舍 + 5 个差异化机会

### Architecture Direction

- **ADR-000**：Direction Correction — Agent Architecture → Digital Life Architecture（Accepted）
- **Architecture Overview**：三层架构（Brain / Execution / Environment）+ 核心闭环
- **ADR-001~005**：State Recovery / Evolution Boundary / EventStream / Brain-Execution Boundary / Agency Autonomy

### Key Findings

- **Memory 领域**有成熟参考（Mem0 + Graphiti + Hindsight），建议综合三者理念内部自建
- **Identity 领域**有规范参考（SOUL.md），建议以规范为基础实现可演化身份
- **Reflection 领域**部分覆盖（记忆反思有参考，行为反思需自行设计）
- **Goal 领域**完全空白——Personal-AI 最大的差异化机会
- **Planning 领域**有部分参考（LangGraph StateGraph），长期规划需自行设计
- **没有任何单一项目可以作为 Personal-AI 核心**，必须综合多个项目理念内部自建
