# 01. 学习路线

## 阶段 0：建立问题框架

目标：先弄清楚这个专题不是“某个模型很强”，而是“工程师如何设计 agent 工作系统”。

阅读：

- [../../../notes/2026-06-12-loop-engineering.md](../../../notes/2026-06-12-loop-engineering.md)
- [00-source-map.md](00-source-map.md)

输出：

- 一句话定义 `Loop Engineering`。
- 一张概念表：`prompt`、`workflow`、`agent`、`loop`、`eval`、`automation`。

## 阶段 1：读 Anthropic 的模式分类

目标：理解 `workflow` 和 `agent` 的边界。

精读：

- Anthropic: Building effective agents

重点问题：

1. 为什么 Anthropic 说最成功的实现往往是简单、可组合的模式？
2. `workflow` 和 `agent` 的区别是什么？
3. evaluator-optimizer 为什么是 loop 的最小原型？
4. Anthropic 为什么反复提醒不要一开始就上复杂 agent？

输出：

- 一张模式表：prompt chaining、routing、parallelization、orchestrator-workers、evaluator-optimizer、agent。
- 一段中文解释：什么时候用 workflow，什么时候用 agent。

## 阶段 2：读 Claude Code 的 agentic loop

目标：理解 coding agent 的实际运行循环。

精读：

- Claude Code: How Claude Code works
- Claude Code: Best practices

重点问题：

1. Claude Code 的 loop 是哪几个阶段？
2. 工具调用如何把环境反馈送回模型？
3. `CLAUDE.md`、memory、skills、subagents 如何影响上下文？
4. 为什么“给 Claude 可验证目标”比“规定每一步”更重要？

输出：

- 一张流程图：gather context -> take action -> verify results -> repeat。
- 一份实践原则：如何给 coding agent 任务、边界和验收标准。

## 阶段 3：读 OpenAI 的 repair loop

目标：理解可度量反馈如何形成闭环。

精读：

- OpenAI Cookbook: Build iterative repair loops with Codex

重点问题：

1. `review`、`repair`、`validate` 三段各自承担什么责任？
2. 为什么 validate 不是最后一步，而是下一轮 repair 的输入？
3. 这个模式能迁移到哪些场景：文档、测试、迁移、代码修复、数据清洗？
4. 什么样的反馈才足够可信，可以驱动下一轮修复？

输出：

- 一个通用 repair loop 模板。
- 一个“只读 review -> repair -> validate”的最小落地方案。

## 阶段 4：读 OpenAI 的 improvement loop

目标：理解 trace、feedback、eval 如何让 agent 系统长期变好。

精读：

- OpenAI Cookbook: Build an Agent Improvement Loop with Traces, Evals, and Codex

重点问题：

1. trace 记录的是什么？
2. human feedback 和 LLM feedback 分别适合做什么？
3. eval 是如何从一次任务反馈沉淀成可复用检查项的？
4. 为什么“自动生成 eval”仍然需要人来收紧和审查？

输出：

- 一张质量改进闭环图：trace -> feedback -> eval -> prompt / harness 改进 -> 新 trace。
- 一篇文章草稿：《可靠 Agent 不靠聪明，靠闭环》。

## 阶段 5：读运行层构件

目标：把 loop 从一次对话变成工程系统。

精读：

- OpenAI Codex: Automations
- OpenAI Codex: Worktrees
- Claude Code: Memory
- Claude Code: Extend Claude Code

重点问题：

1. automation 解决什么问题？什么时候不该自动化？
2. worktree 如何降低并行 agent 的文件冲突？
3. memory / instructions 如何降低重复解释成本？
4. skills、MCP、hooks、subagents 分别在 loop 里放在哪里？

输出：

- 一张 loop 构件图。
- 一套个人开发者可落地的只读 loop。
- 一套团队可落地的半自动 PR loop。

## 阶段 6：生成文章系列

目标：把精读结果变成可讲、可发、可复用的中文文章。

建议顺序：

1. 先写总论：为什么从 Prompt Engineering 走向 Loop Engineering。
2. 再写对比：OpenAI 和 Anthropic 的共同结构。
3. 再写验证：repair loop、trace、eval 如何保证质量。
4. 最后写落地：个人和团队如何从只读 loop 开始。
