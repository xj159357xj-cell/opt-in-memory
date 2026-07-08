# 基于索引预加载的主动记忆架构

**零依赖、可迁移的轻量级 Agent 长期记忆方案。**

通过"索引预加载 + 按需读取"的两层检索，让 Claude Code 具备跨会话记忆能力，同时保持 context 开销最小化。

---

## 设计哲学

**先看目录，再翻书。**

```
会话启动 → 加载索引 (~300 tokens)
用户提问 → Agent 浏览索引，判断相关性
命中 → 按路径读取完整文件
未命中 → 零开销
```

与传统 Harness 的"全量注入"不同——**让 Agent 自己决定要不要翻这本书**。

写入端同样受控：记忆由用户主动通过提示词触发（如"记住这个"），而非 Agent 自动抓取对话内容。

---

## 核心对比

| 维度 | 传统 Harness | 本方案 |
|------|--------------|--------|
| Context 开销 | 全量注入 (数千 tokens) | 索引 ~300 tokens，按需加载 |
| 透明度 | 黑盒，不知注入了什么 | 白盒，可直接查看编辑 |
| 依赖项 | 数据库/向量存储/云服务 | 纯文件系统 |
| 可迁移性 | 需迁移数据库、重配 | 复制文件夹即可 |
| 可控性 | 自动注入，可能噪音 | 用户触发写入，按需读取 |

**传统方案**：每次会话注入全部记忆，稀释指令、浪费 context。

**本方案**：索引始终在但开销极小，真正相关时才加载详情。

---

## 快速开始

### 1. 克隆仓库

```bash
git clone <your-repo-url> ~/Desktop/Claude\ memory
```

### 2. 配置 Claude 全局指令

编辑（或新建）`~/.claude/CLAUDE.md`，这是 Claude Code 的**全局配置文件**，对所有项目生效。

```bash
# 如果文件不存在则新建
mkdir -p ~/.claude
nano ~/.claude/CLAUDE.md
```

将以下内容写入 `~/.claude/CLAUDE.md`：

```markdown
# 全局指令

## 记忆系统

本用户的所有项目共享一个全局记忆目录：`/Users/<你的用户名>/Desktop/Claude memory/`

### 目录结构

```
Claude memory/
├── MEMORY.md              ← 全局索引（列出所有项目及关键记忆）
└── memory/
    ├── <project-name>/    ← 按项目分文件夹
    │   ├── topic-a.md
    │   └── topic-b.md
    ├── <another-project>/
    │   └── ...
    └── general/           ← 不属于特定项目的通用知识
        └── tips.md
```

### 会话开始时
- 读取 `/Users/<你的用户名>/Desktop/Claude memory/MEMORY.md` 索引，了解已有记忆

### 会话中
- 当用户要求"记住"某事、或出现值得长期保存的信息时，写入全局记忆目录
- 根据当前项目或话题，写入对应的项目子目录
- 如果不属于任何项目，写入 `general/`
- 同步更新 `MEMORY.md` 索引

### 记忆文件格式
```markdown
---
name: <short-kebab-case>
description: <one-line summary>
---

<记忆内容>
```

### MEMORY.md 索引格式
```markdown
# Claude 全局记忆索引

## <project-name>
- [topic-a](memory/<project-name>/topic-a.md) — 一句话摘要
- [topic-b](memory/<project-name>/topic-b.md) — 一句话摘要

## general
- [tips](memory/general/tips.md) — 一句话摘要
```

### 注意事项
- 不要重复写入已有记忆，先检查 MEMORY.md 和对应项目目录
- 项目特定的记忆写入项目子目录，跨项目通用的写入 general/
- 保持记忆简洁、可检索
- 项目文件夹命名用 kebab-case（如 `swxy`、`llm-distill`）
```

> **注意**：将上面的 `<你的用户名>` 替换为你的 macOS 用户名（即 `whoami` 的输出）。

### 3. 验证配置

打开一个新的 Claude Code 会话，输入：

```
读取 MEMORY.md 索引，告诉我你记住了什么
```

如果 Claude 能列出索引内容，说明配置成功。

### 4. 开始使用

```
记住：我的 Python 项目用 uv 管理依赖
```

Claude 会自动：
1. 判断归属项目（或写入 `general/`）
2. 创建对应的 `.md` 文件
3. 更新 `MEMORY.md` 索引

---

## 目录结构

```
Claude memory/
├── README.md              ← 本文件
├── MEMORY.md              ← 全局索引（Claude 每次会话加载）
└── memory/
    ├── project-alpha/     ← 某个项目
    │   ├── architecture.md
    │   ├── tech-stack.md
    │   └── ...
    ├── project-beta/      ← 另一个项目
    │   └── ...
    └── general/           ← 跨项目通用知识
```

---

## 适用场景

- ✅ 个人 Agent 长期记忆 (< 100 条)
- ✅ 多项目知识管理
- ✅ 需要透明可控的记忆系统

## 不适用

- ❌ 海量文档 (> 1000 条)
- ❌ 需要模糊语义搜索

---

## 核心原则

1. **最小化 Context**：索引 ~300 tokens，不是 ~5000
2. **完全透明**：用户可查看、编辑、删除任何记忆
3. **零依赖**：纯文件系统，无需外部服务
4. **用户可控**：写入由用户触发，非自动抓取
5. **可迁移**：复制文件夹即迁移

---

## FAQ

### Q: 为什么不用向量数据库？

本方案刻意选择纯文件系统。向量数据库适合大规模语义检索，但对个人记忆 (< 100 条) 是过度工程。文件系统的好处：零依赖、Git 可追踪、任何编辑器可查看、复制即迁移。

### Q: 记忆太多怎么办？

- 定期清理过时条目
- 每条记忆保持精简（5-20 行）
- 超过 100 条时考虑按项目拆分仓库

### Q: 可以在 Windows/Linux 上用吗？

可以，只需修改 `~/.claude/CLAUDE.md` 中的路径为对应系统的绝对路径。

---

*灵感：人类记忆——先记住"在哪能找到"，再按需回忆细节。*
