# AI Info Radar

这个目录用于沉淀“AI 情报提醒系统”的产品和实验材料。

当前只有 PRD，还不是实现项目。它适合作为后续实验方向：

- 信息源 manifest 设计。
- 官方源和聚合源的权重规则。
- 去重、事件合并和重要性分类。
- Feishu 提醒和 Markdown 日报。
- 不依赖 LLM 的第一版规则系统。

## 文件

- [PRD.md](PRD.md)：从 `easyTalk` 迁入的第一版产品需求文档。

## 后续实验建议

1. 先用 fixture 做 source manifest、dedupe、classification 的纯函数实验。
2. 再接入少量官方 RSS/GitHub release/status feed。
3. 最后才考虑通知、日报和调度。
