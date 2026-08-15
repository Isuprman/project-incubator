# Project Incubator

一个把项目从 0 带到"可运行代码 + 测试 + 审查"的单一引导流程（Agent Skill，SKILL.md 格式）。

A single guided pipeline that takes a project from *nothing* to *working code with tests and review*.

## What it does

融合七个工作流为一条有阶段闸门的流水线：

1. **苏格拉底式引导** — 用户没有 idea 时，一次一个问题地拷问，挖出真实痛点，产出 3 个评分候选方向
2. **决策树质询** — 对选定方向进行连环质询，每问必带推荐答案，用户只做决策
3. **固化** — 质询共识转成 ADR + CONTEXT.md + spec + design brief
4. **拆工单** — spec 拆成可独立交付、带依赖关系的工单，标记 UI 工单
5. **设计门** — UI 工单实现前先过设计规范，实现后过设计审查轴（解决"agent 前端丑"）
6. **测试先行实现** — 逐张工单 red → green → refactor
7. **内建审查** — 每张工单三重审查（仓库规范 + spec + 设计），通过才算完成

```
Stage 0  苏格拉底引导    (没想法)   → 3 个候选方向评分
Stage 1  决策树质询      (有方向)   → 达成共识的设计树
Stage 2  固化            (共识)     → ADR + CONTEXT.md + spec + design brief
Stage 3  拆工单          (spec)     → 带依赖的工单列表 + UI 标记
Stage 4  实现循环        (工单)     → 每张: 设计门 → 测试 → 代码 → 三轴审查 → 完成
```

## Design Track（设计路由）

做系统/网页 UI 时按页面类型自动路由设计 skill，没有对应 skill 时用内置兜底规则：

| 页面类型 | 优先设计 skill | 兜底 |
|---|---|---|
| 系统/工具页（表单、表格、仪表盘） | ui-ux-pro-max | design-guidelines.md |
| 营销/产品页 | design-taste | design-guidelines.md |
| 游戏 UI | game-ui-design | design-guidelines.md |

内置 `references/design-guidelines.md` 编码了同样的硬规则（无背景块、间距 scale、层级、8 状态、反 AI 味），
任何环境都能跑，只是效果略降。

## Usage

Install into any agent that reads SKILL.md (Codex, Claude Code, CodeBuddy, …),
then trigger by natural language:

```
用 project-incubator 这个 skill 帮我从 0 孵化一个项目
用 project-incubator 这个 skill 帮我质询并实现这个想法：<你的 idea>
```

If you already have an idea, skip Stage 0 and the flow starts at grilling.

## Files

```
SKILL.md                         主流程（五阶段 + 阶段闸门 + Design Track）
references/
  socratic-questions.md          Stage 0 问题库
  spec-template.md               Stage 2 spec 模板（含 design brief 小节）
  design-guidelines.md           内置设计规则 + 设计轴审查清单（无设计 skill 时兜底）
  ticket-template.md             Stage 3 工单模板（含 kind 标记）
  review-axes.md                 Stage 4 三轴审查细则（规范 / spec / 设计）
```

## Design principles

- **Stage gates**: 每个阶段有明确出口条件，禁止跳过（尤其"未确认共识不得动手实现"）
- **事实 vs 决策**: agent 查事实（文件/工具/子代理），用户只做决策
- **推荐答案强制**: 质询阶段每个问题必带 ➡️ 推荐答案，避免用户从零想
- **设计门**: UI 工单不经过设计规范不得实现，实现后必过设计审查轴（治"agent 前端丑"）
- **审查是工单的一部分**: 测试全绿但任何一轴审查不过 = 未完成
- **Tracer-bullet 工单**: 每张工单端到端穿透功能栈，而不是孤立切片

## Attribution

This skill synthesizes ideas from:

- [mattpocock/skills](https://github.com/mattpocock/skills) (MIT) — grilling, to-spec, to-tickets, tdd, code-review workflows
- AI Agent 从 0 到 1 实战指南 — Socratic idea-mining methodology (B-G-U-C-O-A framework, first-principles MVP scoping)

## License

MIT — see [LICENSE](LICENSE)
