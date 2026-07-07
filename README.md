# AI Learning Lab

这个项目用于持续学习 AI 最新技术，并把零散阅读、实验和思考沉淀成可复用的个人知识资产。

它不是资料收藏夹，也不是追热点清单。每一次学习都尽量落到四类产出：

- `notes/`: 精读笔记，记录资料本身讲了什么、为什么重要、边界在哪里。
- `experiments/`: 动手实验，记录环境、步骤、结果、失败点和可复现命令。
- `articles/`: 对外或对内可发布的文章草稿，把学习结果讲清楚。
- `reviews/`: 周/月复盘，保留学习取舍、下一步方向和阶段性判断。

分类时同时看两件事：

- 内容主题放进 `topics/`，例如 agent、models、RAG、evals、AI infra、AI product。
- 产出形态放进 `notes/`、`experiments/`、`articles/` 或 `reviews/`。

这样这个仓库以后可以继续扩展到更多 AI 方向，而不会被当前的 Agent/Loop Engineering 主题限制住。迁入规则见 [04-classification-and-intake.md](04-classification-and-intake.md)。

## 学习主线

当前先按 6 条主线组织，后续可以根据兴趣和工作需要调整：

1. Frontier models: 大模型能力演进、多模态、长上下文、推理、工具调用。
2. Agent engineering: Agent 架构、工具编排、记忆、任务分解、运行时、Codex/Claude Code 类工程实践。
3. RAG and knowledge systems: 检索、知识库、上下文工程、企业知识接入。
4. Evals and safety: 评测体系、回归测试、可观测性、安全边界和质量控制。
5. AI infrastructure: 推理服务、模型部署、成本、延迟、开源模型、本地化运行。
6. AI products: AI 应用形态、工作流改造、产品设计、真实业务集成。

## 推荐工作流

1. 在 `00-source-map.md` 里登记资料来源和追踪频率。
2. 选一个资料或问题，按 `templates/learning-note.md` 写精读笔记。
3. 如果资料涉及工程实现，在 `experiments/` 下新建实验记录。
4. 每周把有价值的笔记合并到 `reviews/`，判断是否值得写成文章。
5. 成熟主题进入 `articles/`，形成可分享的学习成果。

## 目录结构

```text
.
├── 00-source-map.md
├── 01-learning-roadmap.md
├── 02-topic-map.md
├── 03-output-plan.md
├── 04-classification-and-intake.md
├── notes/
├── experiments/
├── articles/
├── reviews/
├── templates/
└── topics/
```

## 记录原则

- 优先读官方文档、工程博客、论文和源码，再读二手解读。
- 每条笔记必须写清楚来源、日期、核心观点、可验证结论和个人判断。
- 不追求一次学全，优先沉淀能复用的概念、代码、架构图和判断框架。
- 对“最新技术”的判断必须带日期，因为 AI 领域变化很快。
