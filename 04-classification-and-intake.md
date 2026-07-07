# 04 Classification And Intake

这个文件用于决定外部材料、旧笔记和新实验应该放进哪里。目标是让 `ai-learning-lab` 能长期扩展，而不是只服务当前的 Agent/Loop Engineering 主题。

## 两层分类

第一层是主题，放在 `topics/`：

| 主题 | 适合内容 |
| --- | --- |
| `topics/models/` | 新模型、能力变化、多模态、长上下文、开源模型 |
| `topics/agents/` | Agent 架构、tool use、coding agents、loop engineering、runtime、权限和沙箱 |
| `topics/rag-knowledge/` | RAG、企业知识库、上下文工程、检索与引用 |
| `topics/evals/` | 评测集、LLM-as-judge、trace、回归测试、质量控制 |
| `topics/ai-engineering/` | 推理服务、部署、成本、延迟、工程接入、可观测性 |
| `topics/product/` | AI 产品形态、工作流改造、业务场景、产品 PRD |

第二层是产出形态，放在根目录的内容区：

| 形态 | 目录 | 判断标准 |
| --- | --- | --- |
| 资料卡片 / 精读笔记 | `notes/` | 主要回答“这份资料讲了什么，我怎么理解” |
| 实验 / 原型 / PRD | `experiments/` | 有明确可验证目标，后续可能动手实现 |
| 文章草稿 | `articles/` | 已经能围绕一个观点独立成文 |
| 周/月/阶段复盘 | `reviews/` | 总结阶段性判断和下一步学习方向 |

## 从 easyTalk 迁入的第一批内容

| 来源 | 迁入位置 | 分类理由 |
| --- | --- | --- |
| `../loop-engineering-study/` | `topics/agents/loop-engineering/` | 已经是完整 Agent/Loop Engineering 专题路线 |
| `../Anthropic_Managed_Agents架构学习笔记_20260530.md` | `notes/2026-05-30-anthropic-managed-agents-runtime.md` | Agent runtime 架构精读笔记 |
| `../Loop_Engineering_学习笔记_20260612.md` | `notes/2026-06-12-loop-engineering.md` | Loop Engineering 概念学习笔记 |
| `../fable5-agent-workflow-prompt-skill.md` | `articles/2026-06-17-fable5-agent-workflow-prompt-skill.md` | 已经是可独立阅读的中文文章 |
| `../AI情报提醒系统_PRD_20260530.md` | `experiments/ai-info-radar/PRD.md` | AI 信息追踪工具的产品/实验设想 |

## 暂不迁入的内容

这些内容目前不放进 public 仓库：

| 来源 | 原因 |
| --- | --- |
| `TencentMeeting/` | 会议录制和临时素材体积大，且不属于 AI 学习成果 |
| `个人近期工作与思考汇报提纲_20260604.*` | 个人工作汇报，不适合整体公开；后续可摘录 AI 辅助研发方法论 |
| `月底工作学习日记_202606.md` | 大部分是网管/CCIE 工作学习记录；后续只摘录 AI 学习部分 |
| 南京地铁相关文档 | 与 AI 学习目标无关 |
| `output/`、`.hatch-pet/`、`hatch-pet-runs/` | 生成资产和中间产物，不是当前仓库主线 |
| `network-management-system-icon.svg`、`智能城域网图标/` | 工作项目视觉资产，不属于 AI 学习库 |

## 后续迁入规则

- 能公开、能复用、能沉淀判断的内容才迁入。
- 原始工作材料、个人材料、会议材料和大文件默认不迁入。
- 一篇材料如果横跨多个主题，先放在最主要主题；其他主题通过索引链接，不复制多份。
- 新主题不要急着建目录；连续出现多份内容后，再升级为 `topics/` 下的专题。
- 迁入时尽量使用英文文件名，正文保留中文。
