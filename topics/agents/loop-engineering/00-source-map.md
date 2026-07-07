# 00. 资料地图

## 资料选择原则

本专题优先使用一手资料：

- OpenAI 官方 Developers / Cookbook / Codex 文档。
- Anthropic 官方 Engineering Blog / Claude Code 文档。
- 本地已有 `Loop Engineering` 学习笔记只作为问题框架和阅读线索，不作为外部事实来源。

## P0：必须精读

| 来源 | 类型 | 为什么读 | 链接 |
| --- | --- | --- | --- |
| Anthropic: Building effective agents | 工程博客 | 明确定义 `workflows` 与 `agents` 的区别，并给出 prompt chaining、routing、parallelization、orchestrator-workers、evaluator-optimizer、agents 等模式。 | https://www.anthropic.com/engineering/building-effective-agents |
| Claude Code: How Claude Code works | 产品/工程文档 | 直接解释 `agentic loop`：gather context、take action、verify results，并说明工具、上下文、权限和 session 如何支撑 loop。 | https://code.claude.com/docs/en/how-claude-code-works |
| OpenAI Cookbook: Build iterative repair loops with Codex | Cookbook / 工程样例 | 直接讲 closed-loop agent workflow：review、repair、validate，并让 Codex 根据验证反馈反复修复。 | https://developers.openai.com/cookbook/examples/codex/build_iterative_repair_loops_with_codex |
| OpenAI Cookbook: Build an Agent Improvement Loop with Traces, Evals, and Codex | Cookbook / 工程样例 | 把 trace、human feedback、LLM feedback、evals 和优化串起来，适合作为“质量改进闭环”的核心材料。 | https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop |

## P1：支撑理解

| 来源 | 类型 | 为什么读 | 链接 |
| --- | --- | --- | --- |
| OpenAI Codex: Workflows | 产品方法文档 | 理解 Codex 如何组织任务、review、workflow 和人机协作。 | https://developers.openai.com/codex/workflows |
| OpenAI Codex: Automations | 产品文档 | 对应 loop 的触发器和节奏：定时、提醒、监控、持续推进。 | https://developers.openai.com/codex/app/automations |
| OpenAI Codex: Worktrees | 产品文档 | 对应并行 agent 的隔离边界，避免多个任务互相踩文件。 | https://developers.openai.com/codex/app/worktrees |
| OpenAI Agents SDK | API / SDK 文档 | 理解 agent、handoff、guardrails、state、trace 和 eval 的 API 层抽象。 | https://developers.openai.com/api/docs/guides/agents |
| Claude Code: Best practices | 实践文档 | 理解怎样给 coding agent 提供上下文、验证目标、约束和探索/实现分阶段流程。 | https://code.claude.com/docs/en/best-practices |
| Claude Code: Common workflows | 实践文档 | 收集具体 workflow 样例，用于文章中的落地案例。 | https://code.claude.com/docs/en/common-workflows |
| Claude Code: Memory | 产品/工程文档 | 对应 loop 的跨 session 记忆、项目规则和长期上下文。 | https://code.claude.com/docs/en/memory |
| Claude Code: Extend Claude Code | 产品/工程文档 | 对应 skills、MCP、hooks、subagents 等扩展层。 | https://code.claude.com/docs/en/features-overview |

## P2：本地预备材料

| 来源 | 类型 | 用法 |
| --- | --- | --- |
| [`../../../notes/2026-06-12-loop-engineering.md`](../../../notes/2026-06-12-loop-engineering.md) | 本地学习笔记 | 用来保留已有问题意识、术语框架和复盘问题；后续写正式文章时，需要回到 P0/P1 官方资料核对。 |

## 暂不纳入主资料的内容

- 媒体新闻稿：可用于背景，但不作为工程方法论来源。
- 社交媒体碎片：可用于发现概念，不直接作为文章论据。
- Fable 5 微信文章：保留在另一个主题里，不混入本专题主线。
