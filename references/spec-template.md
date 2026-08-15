# spec.md Template

Copy this template into `spec.md` at Stage 2. Fill every section.
Self-contained: an agent reading only this file must be able to implement without asking the user.

```markdown
# Spec — <Project Name>

## 1. Problem & Target Users

目标用户在……时，难以……，导致……。
- 用户画像：<who, in what situation, how they currently cope>
- 首个验证场景：<the narrowest surface to prove value first>

## 2. Core Value & MVP Scope

用户使用后，能在……分钟内得到……。

MVP 必须有：
- <must-have 1>
- <must-have 2>

明确不做（Non-goals）：
- <explicit non-goal>
- <explicit non-goal>

## 3. Protocol (if the product is AI-review / evaluation)

- 输入：<what the user submits>
- 评审维度 / rubric：<fixed dimensions>
- 输出格式：<schema or structure, max items, per-item fields>
- 评分含义：<what the score means; avoid fake objective totals>

## 4. Acceptance Criteria

- <verifiable criterion 1>（例如：5 位试用者中至少 3 位能独立完成一次核心流程）
- <verifiable criterion 2>

## 5. Boundaries & Must-Not-Delete Core

- 不可删的核心：<things that absolutely must ship>
- 可朴素实现但不能取消：<things that must exist but can be simple>
- 边界：<what is explicitly out of scope for MVP>

## 6. Validation Plan

- <how the MVP will be validated: who, how many rounds, what signal counts as success>
- <what to do if the signal is weak — narrow & diagnose, do NOT expand scope>

## 7. Design Brief (if the product has any UI)

> 单独产出 design-brief.md，本节省略号；要点如下：

- 页面清单 + 页面类型分类（system/tool / marketing / game UI —— 决定设计 skill 路由）
- 每页布局、组件状态、交互要求
- 设计原则（来自用户的审美偏好：无装饰背景块、纯排版层级、居中对称、状态徽章不挤标题等）
- 2–3 个视觉方向 + 取舍 → 用户选一个 → 细化
```
