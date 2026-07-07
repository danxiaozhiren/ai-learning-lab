# 02 Topic Map

这个文件记录可长期扩展的学习方向。每个方向都应最终落到笔记、实验、文章或复盘。

`topics/` 是主题地图，不是文件类型。一个主题下面可以有路线图、资料地图和专题索引；具体产出仍然放在 `notes/`、`experiments/`、`articles/` 和 `reviews/`。

## Models

- reasoning models
- multimodal models
- long-context models
- model routing
- open-source models
- small language models

## Agent Engineering

- tool calling
- computer use
- planning and task decomposition
- memory
- multi-agent coordination
- workflow vs autonomous agent
- agent runtime and sandbox
- human-in-the-loop review
- loop engineering
- coding agents
- agent safety boundaries

## RAG and Knowledge

- document ingestion
- chunking strategy
- embedding and vector search
- hybrid search
- reranking
- citation and source grounding
- knowledge freshness
- permission control

## Evals and Observability

- golden datasets
- rubric scoring
- LLM-as-judge
- trace analysis
- regression tests
- prompt/version management
- safety and refusal evaluation

## AI Infrastructure

- inference serving
- latency and cost optimization
- local model runtime
- GPU basics
- quantization
- fine-tuning
- synthetic data

## AI Product and Workflow

- AI coding
- AI research assistant
- AI knowledge worker
- AI customer support
- AI data analysis
- AI operations assistant
- AI in network management
- AI intelligence radar

## Intake Rules

新增内容按下面顺序判断：

1. 先判断主题：它主要属于模型、Agent、RAG、评测、基础设施、产品，还是一个新方向？
2. 再判断产出形态：它是资料笔记、实验记录、文章草稿、复盘，还是专题路线？
3. 如果只是临时资料，先进入 `notes/`，不要直接扩新目录。
4. 如果一个方向连续出现 3 篇以上笔记、1 个实验或 1 篇文章，再在 `topics/` 下建立专题目录。
5. 如果内容包含个人、工作、会议、账号、签名或未脱敏信息，不放进 public 仓库，只摘录可公开的 AI 方法论部分。

## Writing Backlog

- Agent 不是一个功能，而是一套工程系统
- RAG 的难点不在向量库，而在知识治理
- Evals 是 AI 应用从 demo 到生产的分水岭
- 为什么最新模型能力会改变产品交互设计
- 个人如何建立 AI 技术追踪系统
