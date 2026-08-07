# Personal-AI — Personal AI OS

> 长期存在的 Digital Life Entity：以 Brain 为核心，具备身份连续、经验积累、自我认知、反思学习、目标驱动和主动行动能力。

## 项目定位

Personal-AI 不是另一个 AI Agent。Personal-AI 的目标是成为**长期存在的 Personal AI OS**——一个以 Brain 为核心的 Digital Life Architecture。

与 AI Agent 的根本区别：
- **Agent** 是任务驱动的、不持有权威长期状态的、可替换的执行单元
- **Personal-AI** 是身份驱动的、有状态的、持续演化的 Brain

## 三层架构

```
┌─────────────────────────────────────────────┐
│              Brain Layer                     │
│  Identity / Memory / Self Model /            │
│  Reflection / Goal / Agency / Planning       │
│  (authoritative owner of cognitive state)    │
├─────────────────────────────────────────────┤
│            Execution Layer                   │
│  Agent Orchestration / Tools / Runtime       │
│  (durable-state-free, not literally stateless)│
├─────────────────────────────────────────────┤
│           Environment Layer                  │
│  External systems, data sources, tools       │
└─────────────────────────────────────────────┘
```

## 七大核心能力

| 能力 | 层级 | 定义 |
|------|------|------|
| Identity | Brain | 稳定且可演化的身份认同 |
| Memory | Brain | 跨会话、跨时间、跨场景的认知/经验记忆 |
| Self Model | Brain | 对自身能力、偏好、边界的认知模型 |
| Reflection | Brain | 对自身行为、决策、记忆的元认知能力 |
| Goal | Brain | 长期目标的设定、追踪、分解、调整 |
| Agency | Brain | 自主观察、建议、有条件执行的决策能力 |
| Execution | Execution | 通过 Agent 将 Goal 转化为 Action |

> Planning 是 Brain 的内部认知能力，不是第七个核心能力。

## 核心闭环

```
Experience → Memory → Self Model → Reflection → Goal → Agency → Planning → Execution → Action → New Experience
```

## 文档结构

```
Personal-AI/
├── README.md                  ← 本文件
├── CHANGELOG.md               ← 版本记录
└── docs/
    ├── research/              ← 调研文档
    │   ├── Landscape-Report.md
    │   └── Personal-AI Landscape Report v1.1.md
    ├── design/                ← 架构设计文档
    │   ├── 00-Architecture-Overview.md
    │   └── Personal-AI_Philosophy.md
    └── decisions/             ← 架构决策记录（ADR）
        ├── ADR-000-direction-correction.md
        ├── ADR-001-state-recovery-model.md
        ├── ADR-002-evolution-boundary.md
        ├── ADR-003-eventstream-positioning.md
        ├── ADR-004-brain-execution-boundary.md
        └── ADR-005-agency-autonomy-boundary.md
```

## ADR 状态

| ADR | 标题 | 状态 |
|-----|------|------|
| ADR-000 | Direction Correction: Agent → Digital Life | Accepted |
| ADR-001 | State Recovery Model | Accepted |
| ADR-002 | Evolution Boundary | Accepted |
| ADR-003 | EventStream Positioning | Accepted |
| ADR-004 | Brain-Execution Boundary | Accepted |
| ADR-005 | Agency Autonomy Boundary | Accepted |

## 当前阶段

```
Architecture Design Phase 1.5 — FROZEN
```

- Research Phase 完成（12 项目调研，Landscape Report v1.0/v1.1）
- Architecture Direction 确认（ADR-000：Digital Life Architecture）
- Architecture Overview 完成（三层架构 + 七大核心能力 + 核心闭环）
- ADR-001~005 全部 Accepted
- Phase 1.5 Architecture Review / Freeze 记录已归档

**下一步**：Architecture Design Phase 2（待启动）
