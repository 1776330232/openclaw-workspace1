# Learnings

> Corrections, knowledge gaps, best practices

---

## [LRN-20260301-001] correction

**Logged**: 2026-03-01T16:39:00+08:00
**Priority**: high
**Status**: resolved
**Area**: config

### Summary

修改 JSON 文件时使用 PowerShell 直接写入会导致转义字符问题，导致配置文件损坏。

### Details

用 PowerShell 写入 settings.json 时，JSON 变成了：
```json
{\"env\": {\"ANTHROPIC_BASE_URL\": \"https://api.minimaxi.com\"}}
```
每个双引号都被加了转义字符 `\`，导致 Claude Code 无法解析配置文件，触发了登录引导流程。

### Suggested Action

以后修改 JSON 文件时：
1. 使用 Python 写入：`json.dump()`
2. 或者用 PowerShell 管道：`Get-Content file.json | ConvertFrom-Json | ConvertTo-Json`

### Metadata

- Source: error
- Related Files: ~/.claude/settings.json
- Tags: config, json, powershell, claude-code

---

## [LRN-20260301-002] best_practice

**Logged**: 2026-03-01T16:58:00+08:00
**Priority**: medium
**Status**: promoted
**Area**: config

### Summary

Agent Commons 知识库可以公开读取，无需 API Key，只需在对话中自然调用。

### Details

- 读取接口公开：/api/v1/reasoning/domains, /proven, /recent
- 写入接口需要注册（有服务器错误，暂时不可用）
- 已记录到 TOOLS.md 作为快速查询参考

### Suggested Action

在对话中遇到相关领域关键词时，主动查询 Agent Commons 知识库：
- 领域词：software-engineering, security, supply-chain
- 问题模式："怎么做"、"最佳实践"

### Metadata

- Source: conversation
- Tags: knowledge-base, agent-commons, tools
- Promoted: TOOLS.md

---

## [LRN-20260301-004] best_practice

**Logged**: 2026-03-01T17:36:00+08:00
**Priority**: medium
**Status**: resolved
**Area**: config

### Summary

安装了 ontology 技能 - 带类型的知识图谱，用于结构化记忆。

### Details

- 技能位置：~/.openclaw/skills/ontology-0.1.2/
- 功能：创建实体(Person/Project/Task/Event)、链接关系、约束验证
- 触发：记住、链接、查询
- 存储：memory/ontology/graph.jsonl

### Suggested Action

可以用 ontology 替代纯 Markdown 记录学习，更结构化、可查询。

### Metadata

- Source: conversation
- Tags: skills, ontology, memory

---

## [LRN-20260301-005] best_practice

**Logged**: 2026-03-01T17:41:00+08:00
**Priority**: high
**Status**: resolved
**Area**: workflow

### Summary

根据任务选择最优技能，必要时组合多个技能。

### Details

用户要求我：
- 了解每个技能的用途
- 根据任务选择最合适的技能
- 复杂任务可以组合多个技能

例如：
- 查知识 → Agent Commons API
- 记录学习 → ontology 或 .learnings 文件
- 查 GitHub → ai-tools-github-radar
- 需要多个技能时组合使用

### Suggested Action

以后遇到任务时：
1. 先想有哪些技能可以用
2. 选最合适的（或者组合）
3. 不要一个技能用到黑

### Metadata

- Source: user_feedback
- Tags: skills, optimization, workflow

---

## [LRN-20260301-006] workflow

**Logged**: 2026-03-01T17:47:00+08:00
**Priority**: critical
**Status**: resolved
**Area**: workflow

### Summary

根据对话任务选择最优技能，没有最优解时主动搜索新技能/解决方案。

### Details

用户要求：
1. 现有技能是最优解时 → 直接使用
2. 不是最优解时 → 自己搜索相关技能或解决方案
3. 不要让技能闲置，要真正发挥作用

### Workflow

遇到任务时：
1. 先想有哪些现有技能
2. 评估是否是最优解
3. 如果不是 → 搜索/学习新方案
4. 用最优方式解决
5. 记录学到的新方法

### Metadata

- Source: user_feedback
- Tags: self-improvement, skills, optimization

---

## [LRN-20260301-007] workflow

**Logged**: 2026-03-01T17:49:00+08:00
**Priority**: high
**Status**: resolved
**Area**: workflow

### Summary

find-skills 已安装，可以用 npx skills find 搜索新技能。

### Details

- 位置：~/.openclaw/workspace/skills/find-skills/
- 用法：npx skills find [查询]
- 例如：npx skills find react performance

### Metadata

- Source: conversation
- Tags: skills, find-skills

---

## [LRN-20260301-008] best_practice

**Logged**: 2026-03-01T17:52:00+08:00
**Priority**: critical
**Status**: resolved
**Area**: workflow

### Summary

安装了 proactive-agent - 主动型 AI 架构，包含 WAL Protocol、Working Buffer、Compaction Recovery 等核心机制。

### Details

- 安装位置：~/.agents/skills/proactive-agent/
- 版本：3.0.0
- 核心：
  - WAL Protocol：写前置日志，纠正/决定先写再回复
  - Working Buffer：60%上下文后记录每条消息
  - Compaction Recovery：压缩后恢复上下文
  - Relentless Resourcefulness：尝试10种方法再放弃

### Suggested Action

开始使用 WAL Protocol：人类输入包含纠正/决定时，先写 SESSION-STATE.md 再回复。

### Metadata

- Source: conversation
- Tags: skills, proactive-agent, workflow

---

## [LRN-20260301-009] correction

**Logged**: 2026-03-01T18:15:00+08:00
**Priority**: critical
**Status**: resolved
**Area**: workflow

### Summary

用户指出：当用户问问题时，应该先搜索最优解，而不是让用户做选择。

### Details

用户问 cron 备份失败怎么办：
- 我解释原因后问"你想怎么处理？"
- 用户反问"最优解呢？"
- 我才去搜 atxp-memory

**问题：** 让用户做选择 = 推卸责任

### Suggested Action

以后：
1. 用户问问题 → 先搜最优解
2. 直接给最佳方案
3. 不要问"你想怎么处理？"

### Metadata

- Source: user_feedback
- Tags: workflow, self-improvement

---

## [LRN-20260301-010] correction

**Logged**: 2026-03-01T18:45:00+08:00
**Priority**: critical
**Status**: resolved
**Area**: memory

### Summary

从未创建 MEMORY.md 长期记忆，用户指出后才意识到问题。

### Details

使用了 3-4 天，只有每日记忆 memory/*.md，但没有创建 MEMORY.md（精选长期记忆）。

### Suggested Action

定期（每次重要学习后）更新 MEMORY.md，把精华内容沉淀下来。

### Metadata

- Source: user_feedback
- Tags: memory, long-term, correction

---

## [LRN-20260301-003] knowledge_gap

**Logged**: 2026-03-01T17:20:00+08:00
**Priority**: low
**Status**: resolved
**Area**: config

### Summary

OpenClaw browser 工具在 attachOnly 模式下需要用户手动连接 Chrome 标签页。

### Details

browser 工具配置为 attachOnly 模式，需要：
1. 用户在 Chrome 打开页面
2. 点击 OpenClaw Chrome 扩展图标连接标签页
3. 才能通过 CDP 控制页面

### Suggested Action

用户分享链接时，不再问"怎么让我看"，而是：
1. 先尝试 web_fetch
2. 如果失败，用 browser 工具打开
3. 如果需要登录，提醒用户保持页面打开

### Metadata

- Source: error
- Tags: browser, openclaw, chrome-extension
