# Stage 4 — Review Axes

Every ticket is reviewed on three independent axes, run in parallel sub-agents,
reported side by side. A ticket is **done** only when tests are green AND all reviews pass.

## Axis 1 — Standards

Does the code follow the repo's documented standards?

- 是否符合仓库既有约定（目录结构、命名、样式系统、错误处理）
- 是否有无关重构（scope creep）
- 是否有安全/性能/可访问性明显问题
- 是否有重复代码本应复用现有实现

## Axis 2 — Spec

Does the code match what the spec asked for?

- 是否实现了 spec 中该工单覆盖的验收标准（逐条对照）
- 是否包含 spec 明确不做的功能（越界）
- 行为是否符合 spec 定义的协议/输出格式
- 测试是否覆盖了该工单的公共契约（happy path + 边界 + 失败分支）

## Axis 3 — Design (UI tickets only)

Does the interface follow the design spec / design guidelines?

- 对照 `design-brief.md` 中该页面的设计规范
- 用 `references/design-guidelines.md` 的审查速查逐项检查：
  - 有无装饰性背景块
  - 间距是否统一 scale、对齐是否一致
  - 字号层级是否分明（≥1.25 倍）
  - 强调色是否唯一且全页一致
  - 8 状态是否齐全（尤其空/错/加载）
  - 表单标签在上、错误在下
  - 无 AI 味元素（渐变文字/玻璃拟态/千篇一律卡片网格等）
  - 布局居中对称、主次分明、状态徽章不挤标题

> 若环境存在设计 skill（ui-ux-pro-max / design-taste / game-ui-design），设计轴优先
> 按 Design Track 路由使用对应 skill 的规范审查；design-guidelines.md 始终作为兜底基准。

## Report format

```
## Review: <ticket NN>

### Standards
- ✅ / ❌ <finding>（证据）
- …

### Spec
- ✅ / ❌ <finding>（对照 spec 第 X 节）
- …

### Design (UI tickets)
- ✅ / ❌ <finding>（对照 design brief / design-guidelines）
- …

### Verdict
PASS / FAIL（附最优先修复项）
```

## If review fails

1. 修复发现的问题
2. 重跑测试
3. 重新审查（所有轴）
4. 通过后才标记工单完成，进入下一张

Never skip review to save time. Review is part of the ticket.
