# CLAUDE.md - Blog 工作空间

## 核心任务

当用户通过微信发送内容时，可能有两种形式：
- **URL 或 MD 内容**：需要抓取并整理（见下方工作流一）
- **问题**：需要根据 wiki 整理的内容或 blog 风格参考来回答（见下方工作流二）

---

## Blog 文风参考

写作风格详见 `writing-style.md`，核心要点：

- **朴实直接**，口语化表达
- **结构清晰**：分层标题 + bullet points + 代码块
- **有具体数字和真实案例**
- **个人视角**："我"的体验
- **结尾适当升华**，但避免过度煽情

---

## 工作流：自动抓取 + 整理

### Step 1：接收内容

用户发送的内容有两种形式：
- **URL**：网页链接，如 `https://example.com/article`
- **MD 内容**：以 `---` 开头或包含 markdown 格式的内容

### Step 2：内容获取与保存

**如果是 URL：**
1. 使用 `WebFetch` 工具抓取网页内容
2. 将 HTML 转换为 Markdown 格式
3. 生成文件名：`raw/YYYYMMDD_HHmmss_标题.md`
4. 保存到 `raw/` 目录

**如果是 MD 内容：**
1. 提取内容中的标题作为文件名
2. 生成文件名：`raw/YYYYMMDD_HHmmss_标题.md`
3. 直接保存到 `raw/` 目录

### Step 3：Wiki 整理 (Ingest)

按照 `TheSchema.md` 的 3.1 Ingest 工作流：

1. 阅读 `raw/xxx.md`，提炼核心要点
2. 创建 `wiki/sources/source_标题.md` 摘要页
3. 根据内容判断是否需要创建：
   - `wiki/entities/` 实体页（如人物、公司、产品）
   - `wiki/concepts/` 概念页（如方法论、技术、理论）
4. 更新 `wiki/index.md` 添加新页面条目
5. 在 `wiki/log.md` 追加记录：
   ```
   ## [YYYY-MM-DD] ingest | raw/xxx.md → wiki/sources/xxx.md
   ```

### Step 4：确认完成

回复用户，告知：
- 文件已保存到 `raw/`
- Wiki 页面已创建/更新
- 简要说明整理的内容类型

---

## 工作流：问题回答 (Query)

### Step 1：理解问题

用户发送的内容不属于 URL 或 MD 内容，视为问题。

### Step 2：检索 Wiki

1. 搜索 `wiki/` 目录下相关的实体页、概念页和来源摘要
2. 提取与问题相关的要点和上下文

### Step 3：组织回答

1. 基于 wiki 内容整理答案
2. 使用 `[[wikilink]]` 标注参考的 wiki 页面
3. 回答尽量简洁，直接回应问题

### Step 4：确认完成

回复用户，包含：
- 直接回答问题
- 引用参考的 wiki 页面链接

---

## 目录结构

```
raw/                          # 原始素材（网页、笔记等）
  └── YYYYMMDD_HHmmss_标题.md
_posts/                       # 已发布的 Blog 文章
  └── YYYY-MM-DD_标题.md
_drafts/                      # 正在写的新文章或修改中的文章
wiki/                         # 辅助知识库（如需整理临时笔记）
  ├── index.md               # 内容索引
  └── log.md                 # 操作日志
TheSchema.md                  # 工作流规范
```

---

## 页面格式规范

所有 wiki 页面必须使用 frontmatter：

```yaml
---
type: "source|entity|concept"
tags: ["tag1", "tag2"]
summary: "一句话说明"
sources: ["raw/文件名.md"]
updated: "YYYY-MM-DD"
---
```

使用 `[[wikilink]]` 语法建立页面间链接。

---

## 工具使用

| 任务 | 工具 |
|------|------|
| 网页抓取 | `WebFetch` |
| 读取文件 | `Read` |
| 写入文件 | `Write` |
| 搜索页面 | `Grep`, `Glob` |
| 提问用户 | 直接回复 |

---

## Blog 写作协助

当用户请求协助写作时：

1. **参考风格**：阅读 `writing-style.md`，遵循写作风格指南
2. **参考文风**：可补充阅读 `_posts/` 下的已有文章，学习用户的用词习惯
3. **保持声音**：避免过于正式或学术化的表达，保持用户本人的"声音"
4. **建议结构**：如用户未提供结构，可建议分层标题、总结前置等符合用户习惯的结构
5. **直接改写**：如用户提供草稿，直接在原文基础上改写润色，而非重新生成
6. **保存位置**：新文章保存到 `_drafts/`，定稿后移到 `_posts/` |
