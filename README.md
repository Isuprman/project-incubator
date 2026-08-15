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

## Design Track（内置设计引擎）

设计引擎已**打包进仓库**（`references/design/`，design-taste 合成：SKILL.md + anti-slop /
design-systems / interaction-states / motion / pre-flight 六件套），**任何环境开箱即用**，
不需要用户额外安装任何设计 skill。

| 页面类型 | 主要引擎 | 兜底 |
|---|---|---|
| 营销/产品页 | references/design/（内置 taste） | design-guidelines.md |
| 系统/工具页（表单、表格、仪表盘） | references/design/（内置 taste） | design-guidelines.md |
| 游戏 UI | 环境有 game-ui-design 则用，否则内置 | design-guidelines.md |

`references/design-guidelines.md` 是内置引擎的快速提炼版 + 设计轴审查清单。
环境里另有专业 skill（如 ui-ux-pro-max）时只能增强、不能替代内置引擎。

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
ATTRIBUTION.md                   第三方归因表（来源 + 许可索引）
LICENSES/
  LICENSE-taste-skill.md         MIT © Leonxlnx
  LICENSE-impeccable.md          Apache 2.0 © pbakaus
references/
  socratic-questions.md          Stage 0 问题库
  spec-template.md               Stage 2 spec 模板（含 design brief 小节）
  design/                        内置设计引擎（taste 六件套，开箱即用）
  design-guidelines.md           设计规则快速提炼 + 设计轴审查清单
  ticket-template.md             Stage 3 工单模板（含 kind 标记）
  review-axes.md                 Stage 4 三轴审查细则（规范 / spec / 设计）
```

## Design principles

- **Stage gates**: 每个阶段有明确出口条件，禁止跳过（尤其"未确认共识不得动手实现"）
- **事实 vs 决策**: agent 查事实（文件/工具/子代理），用户只做决策
- **推荐答案强制**: 质询阶段每个问题必带 ➡️ 推荐答案，避免用户从零想
- **设计门**: UI 工单不经过设计规范不得实现，实现后必过设计审查轴（治"agent 前端丑"）
- **自包含设计引擎**: 设计能力打包进仓库，任何用户 clone 即用，不依赖外部安装
- **审查是工单的一部分**: 测试全绿但任何一轴审查不过 = 未完成
- **Tracer-bullet 工单**: 每张工单端到端穿透功能栈，而不是孤立切片

## Attribution

- `ATTRIBUTION.md` — 完整的第三方归因与许可索引
- `LICENSES/` — taste-skill (MIT) 与 impeccable (Apache 2.0) 官方许可证原文
- 主要来源: [mattpocock/skills](https://github.com/mattpocock/skills) (MIT)、
  [taste-skill](https://github.com/Leonxlnx/taste-skill) (MIT)、
  [impeccable](https://github.com/pbakaus/impeccable) (Apache 2.0)、
  AI Agent 从 0 到 1 实战指南（苏格拉底式引导方法论）

## License

MIT — see [LICENSE](LICENSE)
