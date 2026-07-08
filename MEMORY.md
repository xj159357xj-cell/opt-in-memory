# Claude 全局记忆索引

## 使用指南
1. 先阅读此索引，判断是否有与当前问题相关的记忆条目
2. 如果相关，读取对应的 `.md` 文件获取完整内容
3. 如果不相关，直接忽略记忆系统

---

## trav-agent
- [architecture](memory/trav-agent/architecture.md) — Aligo旅行助手三层架构 [标签: 架构, 多Agent, 编排]
- [tech-stack](memory/trav-agent/tech-stack.md) — 技术栈：AgentScope+DeepSeek [标签: 技术栈, LLM, RAG]
- [skills-plugins](memory/trav-agent/skills-plugins.md) — 8个Skill插件的职责和优先级 [标签: 插件, 技能, 开发规范]
- [known-issues](memory/trav-agent/known-issues.md) — API Key硬编码等问题 [标签: 问题, 待修复]
- [file-map](memory/trav-agent/file-map.md) — 关键文件路径速查表 [标签: 文件, 路径, 速查]

## agent-sft
- [data-sandbox](memory/agent-sft/data-sandbox.md) — 数据沙盒4层合成数据生成流程 [标签: 数据, 合成, SFT]

## abcd（本地RAG系统）
- [architecture](memory/abcd/architecture.md) — 前后端分离RAG系统三层架构 [标签: 架构, RAG, 全栈]
- [tech-stack](memory/abcd/tech-stack.md) — FastAPI+React+ES+PG+Redis技术栈 [标签: 技术栈, RAG, 全栈]
- [pros-cons](memory/abcd/pros-cons.md) — 优劣势分析 [标签: 评估, 优劣势]
- [file-map](memory/abcd/file-map.md) — 关键文件路径速查表 [标签: 文件, 路径, 速查]
