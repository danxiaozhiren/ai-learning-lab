# Loop Engineering 专题学习

## 专题定位

这个目录用于系统学习 `Loop Engineering`：从 OpenAI、Anthropic 的 agent / coding agent / eval / workflow 资料中，整理出一条清晰的学习路线，并逐步沉淀成几篇可发布或可复用的中文文章。

这个专题不混入已有的 Fable 5 文章。Fable 5 文章更像模型能力与 Prompt / Skill 价值变化的观察；本专题关注的是工程方法：怎样设计一个可运行、可验证、可恢复的 agent 工作循环。

## 核心问题

1. `Prompt Engineering` 和 `Loop Engineering` 的边界在哪里？
2. OpenAI 和 Anthropic 如何定义 `workflow`、`agent`、`agentic loop`？
3. 一个可靠 loop 需要哪些构件：context、tools、memory、evals、trace、automation、worktree、subagent？
4. loop 的质量靠什么保证：测试、验证、grader、human review、maker/checker 分离？
5. 工程师在 loop 时代的角色是什么：写 prompt 的人，还是设计工作系统的人？

## 目录结构

- [00-source-map.md](00-source-map.md)：官方资料地图，区分 OpenAI、Anthropic、本地预备笔记。
- [01-learning-roadmap.md](01-learning-roadmap.md)：专题学习路线，从概念到文章产出。
- [02-close-reading-notes.md](02-close-reading-notes.md)：精读笔记模板和第一版摘记。
- [03-article-plan.md](03-article-plan.md)：后续文章选题、主旨、结构和验收标准。
- [articles/README.md](articles/README.md)：正式文章输出目录。

## 工作方式

先读资料，再写文章。

每篇材料精读时，至少回答四个问题：

1. 它对 `loop` 的定义是什么？
2. 它把人的角色放在哪里？
3. 它如何处理验证、回滚、权限和成本？
4. 它能沉淀成什么可复用的工程原则？

## 预期产出

第一阶段先形成 4 篇文章：

1. 《从 Prompt Engineering 到 Loop Engineering：AI 编程的杠杆点变了》
2. 《OpenAI 和 Anthropic 都在讲的 Agent Loop，到底是什么》
3. 《可靠 Agent 不靠聪明，靠闭环：trace、eval 和 repair loop》
4. 《工程师不会消失，但会换位置：从写提示词到设计工作系统》

