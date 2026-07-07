# 02. 精读笔记

## 精读模板

每篇资料按以下结构整理：

```markdown
## 资料标题

- 来源：
- 类型：
- 读它解决的问题：

### 核心主张

### 关键概念

### 对 Loop Engineering 的贡献

### 可复用原则

### 风险提醒

### 可写入文章的观点
```

## Anthropic: Building effective agents

- 来源：https://www.anthropic.com/engineering/building-effective-agents
- 类型：Anthropic Engineering Blog
- 读它解决的问题：先建立 `workflow` 与 `agent` 的模式分类。

### 核心主张

成功的 agentic system 不一定复杂。Anthropic 的经验是，很多生产系统更适合从简单、可组合的模式开始，只有在任务确实需要更强动态决策时才上更自主的 agent。

### 关键概念

- `workflow`：LLM 和工具沿着预定义代码路径被编排。
- `agent`：LLM 动态决定流程和工具使用方式。
- `evaluator-optimizer`：一个模型生成结果，另一个模型评价并反馈，形成迭代改进循环。
- `orchestrator-workers`：中心模型动态拆分任务并分派给 worker。

### 对 Loop Engineering 的贡献

这篇文章提供了 loop 的模式语言。它说明 loop 不只有一种形态：可以是固定路径的 workflow，也可以是模型动态决策的 agent。

### 可复用原则

- 先用最简单的模式。
- 只有当固定路径无法覆盖任务变化时，才增加 agent 自主性。
- 明确 evaluation criteria 后，evaluator-optimizer 才有意义。
- coding agent 适合 agent 化，是因为代码可以通过测试和执行结果验证。

### 风险提醒

- agent 通常会增加延迟和成本。
- 自主性会带来复合错误。
- 需要沙箱、guardrails 和明确停止条件。

### 可写入文章的观点

Loop Engineering 不是“让 AI 自己跑就行”，而是在 workflow 和 agent 之间选择合适的自主性。

## Claude Code: How Claude Code works

- 来源：https://code.claude.com/docs/en/how-claude-code-works
- 类型：Claude Code Docs
- 读它解决的问题：理解 coding agent 的实际循环结构。

### 核心主张

Claude Code 的核心是 agentic loop：收集上下文、采取行动、验证结果，并根据环境反馈继续循环。工具调用不是附加能力，而是 loop 得以成立的反馈通道。

### 关键概念

- gather context
- take action
- verify results
- tools
- session
- context window
- memory
- checkpoints
- permissions

### 对 Loop Engineering 的贡献

这篇文档把 loop 从抽象模式落到 coding agent 的日常工作：搜索文件、读代码、编辑、运行测试、根据结果修正。

### 可复用原则

- 给 agent 一个可验证目标，而不只是一个模糊愿望。
- 复杂任务先探索再实现。
- 通过 memory、instructions、skills、subagents 控制上下文和任务分解。
- 权限和 checkpoints 是 loop 的安全边界。

### 风险提醒

- Claude 能做你在终端能做的很多事，权限边界必须清楚。
- context 会膨胀，不能把长期规则只放在对话里。
- 外部副作用不能只靠文件 checkpoint 回滚。

### 可写入文章的观点

Coding agent 的本质不是“更会写代码的补全器”，而是一个能反复从环境拿反馈的执行循环。

## OpenAI: Build iterative repair loops with Codex

- 来源：https://developers.openai.com/cookbook/examples/codex/build_iterative_repair_loops_with_codex
- 类型：OpenAI Cookbook
- 读它解决的问题：理解最小 closed-loop workflow。

### 核心主张

可靠 agent workflow 可以拆成三段：review、repair、validate。agent 不只是生成答案，还要根据验证反馈进入下一轮修复。

### 关键概念

- review：只读检查当前状态，输出结构化 findings。
- repair：基于 findings 和验证反馈做聚焦修复。
- validate：运行可信检查，报告仍未解决的问题。
- closed-loop workflow：输出、验证、反馈、再改进。

### 对 Loop Engineering 的贡献

这篇 Cookbook 给出了最适合作为落地起点的 loop：先不追求全自动，而是让每轮输出都有验证反馈。

### 可复用原则

- review 和 repair 分开。
- validate 的结果必须能进入下一轮。
- 反馈必须可信，最好来自测试、lint、脚本或结构化检查。
- 任务样例可以换，但模式不变。

### 风险提醒

- 如果 validate 不可靠，loop 会把错误越修越深。
- 如果 repair 范围不聚焦，loop 会引入无关改动。

### 可写入文章的观点

真正的闭环不是“让 agent 多试几次”，而是每次尝试都要被可信反馈约束。

## OpenAI: Build an Agent Improvement Loop with Traces, Evals, and Codex

- 来源：https://developers.openai.com/cookbook/examples/agents_sdk/agent_improvement_loop
- 类型：OpenAI Cookbook
- 读它解决的问题：理解长期质量改进闭环。

### 核心主张

一次 agent 运行留下 trace；trace 可以被人类和模型审查；审查结果可以沉淀成 eval；eval 再反过来约束下一轮 agent 或 harness 改进。

### 关键概念

- trace
- human feedback
- LLM feedback
- eval definition
- grader
- prompt / harness optimization

### 对 Loop Engineering 的贡献

它把 loop 从一次任务推进，提升到系统长期改进：不是只修这一次，而是把这一次暴露的问题变成以后都能检查的 eval。

### 可复用原则

- 保留 trace，才能复盘 agent 真实行为。
- feedback 要能转成可执行 eval。
- 自动生成 eval 可以提速，但人仍要审查弱 eval。
- 优化 prompt 之前，先明确评价标准。

### 风险提醒

- eval 如果太弱，会制造虚假的质量感。
- LLM feedback 不能替代人类对业务和风险的判断。

### 可写入文章的观点

Agent 系统真正变强，不是因为某次 prompt 写得好，而是因为每次失败都沉淀成下一次的检查。

## 待补精读

- OpenAI Codex: Workflows
- OpenAI Codex: Automations
- OpenAI Codex: Worktrees
- Claude Code: Best practices
- Claude Code: Common workflows
- Claude Code: Memory
- Claude Code: Extend Claude Code

