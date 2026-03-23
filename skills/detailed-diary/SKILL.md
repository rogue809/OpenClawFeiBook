---
name: detailed-diary
description: 将指定时间范围的聊天记录整理成日记文档，存储到工作空间，并可手动触发推送到 GitHub。
metadata:
  {
    "openclaw":
      {
        "requires": { "tools": ["exec"] },
      },
  }
---

# 详细日记 (Detailed Diary)

将你和我的聊天记录整理成 Markdown 日记文档。

## 触发方式

当我对你说以下内容时，自动触发：
- "写日记"
- "生成日记"
- "记录今天聊天"
- "把聊天记下来"

## 使用方法

### 1. 基本用法

```
你：写日记
我：请问要记录哪段时间的聊天？（例如：今天早上、昨天、2026-03-23）
```

### 2. 指定时间

```
你：写昨天的日记
我：（获取昨天的聊天记录，生成文档）
```

### 3. 生成文档

- 文件名格式：`diary/YYYY-MM-DD.md`
- 内容格式：**每条消息单独一段，用 `---` 分隔**
- 格式示例：
```markdown
**👤 邱顺** Mon 2026-03-23 11:00 GMT+8

消息内容

---

**🦞 麻辣小龙虾** Mon 2026-03-23 11:00 GMT+8

回复内容

---

**👤 邱顺** ...

```
- **关键**：每条消息后必须加 `---` 分隔符
- 助手消息如果没有时间戳，留空即可

### 4. Git 推送

当你说"上传日记"或"推送日记"时：
- 自动将新增/修改的日记文件加入 git
- 推送到远程仓库

## 实现步骤

### 第一步：找到会话文件
- 路径：`/home/gem/workspace/agent/agents/main/sessions/{session_id}.jsonl`
- 查找方式：`ls -lt /home/gem/workspace/agent/agents/main/sessions/*.jsonl | head -1`

### 第二步：解析 JSONL
- 逐行读取
- 提取 `type=message` 的记录
- 区分 `role: user` 和 `role: assistant`
- 用户消息：从 text 中提取 `[Mon timestamp] ` 后面的内容
- 助手消息：取完整的 text 内容

### 第三步：生成 Markdown
- 每条消息格式：`**👤 邱顺** 时间\n\n内容\n\n---\n\n`
- 过滤敏感信息（Token 等）

## 文件存储

- 路径：`/home/gem/workspace/agent/workspace/diary/`
- 命名：`YYYY-MM-DD.md`

## Git 操作

- 手动触发（需要你明确说"上传"）
- 会把 diary/ 目录下的所有文件一起推送

## 注意事项

- 仅记录当前会话的聊天记录
- 不涉及其他群聊或私聊
- 尊重隐私，不记录敏感信息
- 每次生成后检查格式，确保 `---` 分隔符正确
