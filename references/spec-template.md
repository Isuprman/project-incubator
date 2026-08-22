# spec.md Template

Copy this template into `spec.md` at Stage 2. Fill every section.
Self-contained: an agent reading only this file must be able to implement without asking the user.

```markdown
# Spec — <Project Name>

> 项目形态：<MVP | 完整版>（Stage 0 选择，贯穿本 spec）

## 1. Problem & Target Users

目标用户在……时，难以……，导致……。
- 用户画像：<who, in what situation, how they currently cope>
- 首个验证场景：<the narrowest surface to prove value first>（MVP 模式必填；完整版模式可选）

## 2. Core Value & Scope

用户使用后，能在……分钟内得到……。

### MVP 模式

MVP 必须有：
- <must-have 1>
- <must-have 2>

明确不做（Non-goals）：
- <explicit non-goal>
- <explicit non-goal>

### 完整版模式

核心模块（module list，1–2 个月完整交付）：
- <module 1> — <职责 / 数据模型 / 用户旅程>
- <module 2> — <职责 / 数据模型 / 用户旅程>

完整用户旅程：
- <journey 1: 从进入到完成，逐步骤>
- <journey 2: …>

明确不做（Out of scope）：
- <explicit out-of-scope>
- <explicit out-of-scope>

## 3. Interface Contract（接口契约表 — 必填，任何形态不得省略）

> 本表是实现的唯一接口依据，也是 Stage 4 评审逐字段核验的基准。缺本表 = spec 未完成。
> 每个对外暴露的 CLI 命令 / API 端点 / MCP 工具 / 关键函数签名都必须列出一行。

| 名称 | 类型(cli/api/mcp/fn) | 选项/参数 | 输出形状 | 错误约定 |
|---|---|---|---|---|
| <tool cmd> | cli | <--opt1> <--opt2> | <信封结构/表格描述> | <无命中 exit N 等> |
| <GET /path> | api | <query 参数> | <JSON 形状> | <错误码约定> |

补充语义定义（按需）：
- <查询类命令的多词输入语义：拆词 AND / 短语匹配 / 其他——不写明则实现者自行猜测>
- <分页、排序、过滤的默认值与边界>

## 4. Protocol (if the product is AI-review / evaluation)

- 输入：<what the user submits>
- 评审维度 / rubric：<fixed dimensions>
- 输出格式：<schema or structure, max items, per-item fields>
- 评分含义：<what the score means; avoid fake objective totals>

## 5. Acceptance Criteria

- <verifiable criterion 1>（例如：5 位试用者中至少 3 位能独立完成一次核心流程）
- <verifiable criterion 2>

## 6. Boundaries & Must-Not-Delete Core

- 不可删的核心：<things that absolutely must ship>（MVP：核心闭环；完整版：每个模块的核心路径）
- 可朴素实现但不能取消：<things that must exist but can be simple>
- 边界：<what is explicitly out of scope>（MVP：out of scope for MVP；完整版：out of scope for v1 / 后置到下一版本）

## 7. Validation Plan

- MVP 模式：<how the MVP will be validated: who, how many rounds, what signal counts as success>
  <what to do if the signal is weak — narrow & diagnose, do NOT expand scope>
- 完整版模式：<release plan / phase boundaries: 分阶段验收点，每阶段可演示、可审查>；
  <每个模块的验收信号与模块间依赖顺序>
- 两种模式通用：<what to do if the signal is weak — narrow & diagnose, do NOT expand scope>
```
