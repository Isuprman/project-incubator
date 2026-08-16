# Ticket Template (Stage 3)

One ticket per independently deliverable, verifiable unit of work.
Publish to the configured tracker (default: `.scratch/<feature>/issues/NN-<slug>.md`).

```markdown
---
ticket: <NN>
status: ready-for-agent
---

# <Short Title>

## Deliverable

<what this ticket produces — one paragraph>

## Acceptance Criteria

- [ ] <verifiable criterion 1>
- [ ] <verifiable criterion 2>

## Spec excerpt (self-contained)

<该工单需要的 spec 相关章节摘录：涉及的功能、协议、验收标准原文。>
<让实现者只读本工单就能开工，不依赖回忆或翻全文。>

## Blocked by

- <ticket NN>（无依赖则写：无，可立即开始）

## Notes

<implementation notes, links to spec sections>
```

## 自包含原则（Critical）

- 每张工单必须包含 Spec excerpt，把该工单需要的约束从 spec 中摘录进工单文件本身。
- 实现者开工时**只读本工单文件**即可，不需要回忆对话或翻全文。
- 摘录要完整到"不读原文也能实现"，而不是只写"见 spec 第 X 节"。

## Tracer-bullet principle

- Each ticket must be a **tracer bullet**: it traverses the full stack of the feature
  end-to-end (even if thin), not a vertical slice in isolation.
- Grain: small enough to complete and verify in one sitting; big enough to be meaningful.
- Dependencies must form a DAG. Ticket N+1 must not be startable before its blockers are done.

## Typical first tickets

1. 入口 / 会话生命周期（无依赖，可立即开始）
2. 输入提交（Blocked by: 01）
3. 核心处理 + 校验（Blocked by: 02）← 通常拆成 schema 定义与核心调用两张
4. 结果呈现（Blocked by: 03）
5. 反馈闭环（Blocked by: 04）
6. 端到端验证（Blocked by: 01–05 全部）
