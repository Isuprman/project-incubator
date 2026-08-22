# 案例研究 #1 — ToolScope 首次完整跑测（2026-08-22）

> n=1 样本。本文只记录观察事实与机制分析，不预设为普遍规律；结论待第 2、3 个项目验证。

## 项目概况

macOS 机器能力目录（扫描应用+CLI 工具 → SQLite → CLI/Web/MCP 三端查询）。完整版模式，12 张工单，Python/FastAPI/Typer + React/TS。代跑模式：质询由 Hermes 代理应答（用户授权），实现分 6 批派发 Claude Code。

## 结果

产物验收通过：121 tests 全绿、真机扫描 1486 条六来源、五端点/CLI --json/MCP stdio 往返全通、README 与实现零脱节。

## 观察到的问题与机制

| 现象 | 机制分析 | v1.1.0 对策 |
|---|---|---|
| 6 张工单批次零产出静默中断（exit 0）；4 张批完成 1 张后静默死 | 单会话上下文/输出预算耗尽；退出码不可信。当时 skill 无任何批次指引/心跳/续跑协议 | Stage 4 新增 Session sizing & resilience：≤2 张/批 + progress.log 心跳 + 续跑协议 |
| 多词搜索失效（自然语言查询恒空） | spec 未定义多词输入语义，实现者按字面整串 LIKE——spec 洞而非评审失职 | spec-template 新增 Interface Contract 必填段（含多词语义条目） |
| CLI list 缺 --kind/--sort/--limit | 同上：spec 契约面未逐命令枚举 | 同上；review-axes Spec 轴新增契约逐字段核验项 |
| mcp SDK 2.0 高层 API 改名（FastMCP→MCPServer） | 外部依赖漂移；工单硬编码 API 名 | pitfalls 新增：工单写行为意图不写 SDK 符号名 |

## 有效机制的实证

- 工单自包含（Spec excerpt 内嵌）：6 个不同会话开工零需求澄清返工
- 双轴评审抓到 3 个测试未覆盖的真缺陷：SDK 改名适配、工具函数遮蔽 query 层同名导入导致递归、UPSERT 回查 NULL origin 用 = 失配
- frontmatter status 字段天然支持断点续跑（本次靠它定位恢复点）

## 代跑模式备注

15 个质询决策由代理基于用户长期记忆应答。事后看：技术栈/边界类决策质量高；**产品语义类决策（如搜索语义）是盲区**——代理不知道终端用户的隐性期望。建议：代跑产出的 spec 应附「ASSUMED 决策清单」交用户复核。
