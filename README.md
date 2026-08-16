# Project Incubator

一个把项目从 0 带到"可运行代码 + 测试 + 审查"的单一引导流程（Agent Skill，SKILL.md 格式）。

A single guided pipeline that takes a project from *nothing* to *working code with tests and review*.

## What it does

融合六个工作流为一条有阶段闸门的流水线：

1. **苏格拉底式引导** — 用户没有 idea 时，一次一个问题地拷问，挖出真实痛点，产出 3 个评分候选方向
2. **决策树质询** — 对选定方向进行连环质询，每问必带推荐答案，用户只做决策
3. **固化** — 质询共识转成 ADR + CONTEXT.md + spec
4. **拆工单** — spec 拆成可独立交付、带依赖关系的工单，内嵌 Spec 摘录
5. **测试先行实现** — 逐张工单 red → green → refactor
6. **内建审查** — 每张工单双重审查（仓库规范 + spec 符合度），通过才算完成

```
Stage 0  苏格拉底引导    (没想法)   → 3 个候选方向评分
Stage 1  决策树质询      (有方向)   → 达成共识的设计树
Stage 2  固化            (共识)     → ADR + CONTEXT.md + spec
Stage 3  拆工单          (spec)     → 带依赖的工单列表（内嵌 Spec 摘录）
Stage 4  实现循环        (工单)     → 每张: 测试 → 代码 → 双轴审查 → 完成后停下等你确认
```

## Execution rhythm（执行节奏）

- 每张工单内部**连续推进到完成**（测试 → 实现 → 重构 → 双轴审查 → 标记完成），不发送中间进度
- 只有两个闸门会停下来等你：开工前的测试 seam 确认、**工单完成后的交付确认**
- 工单完成后汇报一次（交付物 + 测试结果 + 审查结论），你确认后才进入下一张；不会自动连跑

## Anti-drift（防上下文漂移）

长流程会稀释早期上下文（spec、验收标准），agent 容易开始凭记忆干活。固定节点**强制重读文档**对抗漂移：

- 工单开工：重读工单文件（内含 Spec 摘录）+ 验收标准
- 审查前：重读验收标准 + spec 摘录，逐条对照，偏离则 FAIL
- 每 2–3 张工单：重读全局 spec.md + CONTEXT.md（防跨工单漂移）

配套：Stage 3 拆工单时每张工单**内嵌 Spec excerpt**，实现者只读工单文件即可开工，不依赖回忆。

## Usage

Install into any agent that reads SKILL.md (Codex, Claude Code, CodeBuddy, …).
This skill is **explicit-invocation** (`disable-model-invocation: true`) — the model won't
auto-trigger it; invoke it by name:

```
用 project-incubator 这个 skill 帮我从 0 孵化一个项目
用 project-incubator 这个 skill 帮我质询并实现这个想法：<你的 idea>
```

If you already have an idea, skip Stage 0 and the flow starts at grilling.

## Project modes

Stage 0 asks once which mode you want; it changes scoring, spec and ticket templates downstream:

| Mode | Timeline | Purpose | Scoring dimension |
|---|---|---|---|
| MVP | ~2 weeks | Validate a minimal version with real users | 2-week MVP feasibility |
| Full | ~1–2 months | Build a complete system / portfolio piece | Full delivery feasibility |

## Files

```
SKILL.md                         主流程（五阶段 + 阶段闸门 + 执行节奏 + Anti-drift）
references/
  socratic-questions.md          Stage 0 问题库
  spec-template.md               Stage 2 spec 模板（MVP / 完整版双模式）
  ticket-template.md             Stage 3 工单模板（内嵌 Spec excerpt）
  review-axes.md                 Stage 4 双轴审查细则（规范 / spec）
```

## Design principles

- **Stage gates**: 每个阶段有明确出口条件，禁止跳过（尤其"未确认共识不得动手实现"）
- **事实 vs 决策**: agent 查事实（文件/工具/子代理），用户只做决策
- **推荐答案强制**: 质询阶段每个问题必带 ➡️ 推荐答案，避免用户从零想
- **审查是工单的一部分**: 测试全绿但任何一轴审查不过 = 未完成
- **Tracer-bullet 工单**: 每张工单端到端穿透功能栈，而不是孤立切片
- **Anti-drift**: 约束写进工单文件、固定节点重读，不靠对话记忆

## Attribution

- [mattpocock/skills](https://github.com/mattpocock/skills) (MIT) — grilling, to-spec, to-tickets, tdd, code-review workflows
- AI Agent 从 0 到 1 实战指南 — Socratic idea-mining methodology (B-G-U-C-O-A framework, first-principles MVP scoping)

## License

MIT — see [LICENSE](LICENSE)
