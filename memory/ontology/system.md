# Ontology 记忆索引系统

> 核心原则：记住"什么"和"在哪里"，不重复存储

## 类型定义

### Person（人）
- id, name, role, goals, preferences, notes

### Project（项目）
- id, name, status, goals, created, note

### Skill（技能）
- id, name, status, purpose, installed_at, docs

### Memory（记忆）
- id, type (daily/longterm/learning), date, status, locations[], content_ref

### Task（任务）
- id, title, status, due, recurring, from_session

### Decision（决定）
- id, content, reason, made_at, outcome

### Learning（学习）
- id, summary, details, source, tags

## 关系

```
Hang (Person) --has_project--> AI创业 (Project)
Hang --has_skill--> capability-evolver (Skill)
Hang --has_task--> [Tasks]
AI创业 --has_task--> [Tasks]
Daily Memory --backed_up_to--> GitHub
Daily Memory --backed_up_to--> 本地备份
```

## 核心实体

### Hang（用户）
- goals: ["创建在线业务", "赚到第一笔收入", "AI创业"]
- preferences: {reply_style: "verbose", language: "Chinese"}

### 小狗（AI）
- goals: ["帮助Hang实现目标", "自主行动", "持续进化"]
- capabilities: ["记忆系统", "能力进化", "技能使用"]

### 技能
- capability-evolver: 自我进化
- ontology: 结构化知识图谱
- find-skills: 搜索新技能
- proactive-agent: 主动型架构
- tianqi-chaxun: 天气查询
- daiban-guanliqi: 待办管理

### 记忆状态
| 日期 | 类型 | 状态 | 位置 |
|------|------|------|------|
| 2026-03-02 | daily | intact | GitHub, 本地 |
| 2026-03-01 | daily | intact | GitHub, 本地 |
| 2026-02-28 | daily | intact | GitHub, 本地 |
| 2026-02-27 | daily | intact | GitHub, 本地 |
| 2026-02-26 | daily | intact | GitHub, 本地 |

### 待办
- [ ] 每天11点自动发送日报到Notion

### 重要决定
- 2026-03-02: 记忆系统加固，采用三层保护（MEMORY.md + ontology + daily）

## 索引规则

1. **实体创建**：每次重要操作后更新 ontology
2. **位置引用**：内容存在文件，ontology 记录"什么在哪里"
3. **验证**：每次会话开始检查 ontology 状态
4. **恢复**：从 GitHub 拉取最新备份

## 读取流程

```
会话开始 → 检查 ontology 状态
  ↓
有异常？ → 报警 + 尝试从 GitHub 恢复
  ↓
正常 → 加载相关实体到上下文
```

## 写入流程

```
重要操作 → 更新相关实体 → 推送到 GitHub
         → 记录到当日日记 → 推送到 GitHub
```
