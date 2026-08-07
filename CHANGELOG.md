# Changelog

本文件记录 Personal-AI 项目的所有版本变更。

格式参考 [Keep a Changelog](https://keepachangelog.com/)。

---

## [0.1.0] — 2026-08-07

### Added

- 创建项目目录结构 `Personal-AI/`
  - `docs/research/` — 调研文档
  - `docs/design/` — 架构设计文档（待填充）
  - `docs/decisions/` — 架构决策记录（待填充）
- 创建 `README.md` — 项目定位与文档结构说明
- 创建 `CHANGELOG.md` — 版本记录（本文件）

### Research

- 完成 **Personal-AI Landscape Report**（975 行）
  - 调研 12 个项目：Codex CLI / Claude Code / OpenHands / Aider / Goose / KaijiBot / Mem0 / Graphiti / Hindsight / SOUL.md / LangGraph / Mastra
  - 每个项目 16 个评估维度（14 基础 + 适配难度 + 长期风险）
  - 横向对比矩阵：功能对比 + 五大核心能力矩阵
  - 借鉴建议分层：P0 核心研究（7 项）/ P1 值得吸收（10 项）/ P2 持续观察（5 项）
  - 结论：5 项核心发现 + 架构建议 + 6 项关键取舍 + 5 个差异化机会

### Key Findings

- **Memory 领域**有成熟参考（Mem0 + Graphiti + Hindsight），建议综合三者理念内部自建
- **Identity 领域**有规范参考（SOUL.md），建议以规范为基础实现可演化身份
- **Reflection 领域**部分覆盖（记忆反思有参考，行为反思需自行设计）
- **Goal 领域**完全空白——Personal-AI 最大的差异化机会
- **Planning 领域**有部分参考（LangGraph StateGraph），长期规划需自行设计
- **没有任何单一项目可以作为 Personal-AI 核心**，必须综合多个项目理念内部自建

### Next Steps

- P0 架构决策：确定无状态 Runtime + EventStream 架构
- P0 原型验证：ADD-only + Dream 记忆模型、双时间模型、SOUL.md 身份规范
- 自行设计：长期 Goal 系统、行为 Reflection、长期 Planning
