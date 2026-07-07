# 03. 文章规划

## 系列定位

系列标题建议：

《Loop Engineering：从提示 Agent 到设计工作循环》

这组文章不写成工具教程，而写成工程方法论。目标读者是已经在使用 Codex、Claude Code、Cursor、Devin、Manus 或其他 coding agent 的工程师和技术负责人。

## 文章 1：从 Prompt Engineering 到 Loop Engineering：AI 编程的杠杆点变了

### 主旨

过去的关键能力是写好单轮 prompt；现在更高杠杆的能力是设计一个能持续推进、验证、记录和恢复的工作循环。

### 结构

1. 为什么 prompt 仍然重要，但不再是唯一杠杆。
2. Loop Engineering 解决的不是“这一轮怎么说”，而是“系统怎么持续工作”。
3. 从 Anthropic 的 workflow / agent 分类看 loop 的不同形态。
4. 从 Claude Code 的 agentic loop 看真实 coding agent 如何运行。
5. 工程师的新职责：目标、边界、验证、回滚、成本。

### 主要资料

- Anthropic: Building effective agents
- Claude Code: How Claude Code works
- 本地 Loop Engineering 笔记

### 验收标准

- 读者能分清 prompt、workflow、agent、loop。
- 不夸大自动化，不制造“工程师消失”的结论。

## 文章 2：OpenAI 和 Anthropic 都在讲的 Agent Loop，到底是什么

### 主旨

虽然两家公司术语不同，但共同结构很清楚：context -> action -> feedback -> verification -> next action。

### 结构

1. Anthropic：workflow vs agent。
2. Claude Code：gather context、take action、verify results。
3. OpenAI Codex：workflow、automation、worktree、review。
4. 共同结构：输入、工具、环境反馈、验证、状态。
5. 差异：产品抽象不同，但工程问题相同。

### 主要资料

- Anthropic: Building effective agents
- Claude Code: How Claude Code works
- OpenAI Codex: Workflows
- OpenAI Codex: Automations
- OpenAI Codex: Worktrees

### 验收标准

- 不把 OpenAI / Anthropic 混成一个产品。
- 清楚区分概念共性和实现差异。

## 文章 3：可靠 Agent 不靠聪明，靠闭环：trace、eval 和 repair loop

### 主旨

Agent 可靠性不是靠模型一次性聪明，而是靠可度量反馈、验证、trace、eval 和反复修复。

### 结构

1. 为什么“让模型再试一次”不是闭环。
2. OpenAI repair loop：review、repair、validate。
3. OpenAI improvement loop：trace、feedback、eval、优化。
4. Anthropic evaluator-optimizer：生成者和评估者分离。
5. 最小可落地方案：只读 review loop。

### 主要资料

- OpenAI: Build iterative repair loops with Codex
- OpenAI: Build an Agent Improvement Loop with Traces, Evals, and Codex
- Anthropic: Building effective agents

### 验收标准

- 明确说明可信反馈的来源。
- 明确说明 eval 的风险：弱 eval 会制造假安全。

## 文章 4：工程师不会消失，但会换位置：从写提示词到设计工作系统

### 主旨

Loop Engineering 不是把工程师从系统里拿掉，而是把工程师推到更高层：定义任务、约束权限、设计验证、控制成本、承担质量责任。

### 结构

1. 为什么“自动化越强，判断越贵”。
2. 工程师从执行者转为 loop designer。
3. 个人开发者的 loop：只读检查、每日 triage、手动决策。
4. 团队的 loop：worktree、subagent、CI、PR review。
5. 三个红线：权限、理解债、认知投降。

### 主要资料

- Claude Code: Best practices
- Claude Code: Memory
- OpenAI Codex: Automations
- OpenAI Codex: Worktrees
- OpenAI repair / improvement loop cookbook

### 验收标准

- 给出可落地的低风险起步方式。
- 不鼓励一上来全自动改代码和提交。

## 可选文章 5：一个最小 Loop Engineering 实战模板

### 主旨

把专题落地成一个保守、低风险、可复用的个人工作流：每天自动读 CI / issues / commits，输出 Markdown triage，不自动改代码。

### 结构

1. 输入：CI、issues、recent commits。
2. 处理：triage skill / prompt。
3. 输出：Markdown findings。
4. 验证：人工挑选值得处理的问题。
5. 升级路径：worktree、repair、validate、PR。

### 验收标准

- 可以直接照着搭一个只读 loop。
- 明确下一阶段何时才允许进入自动修复。

