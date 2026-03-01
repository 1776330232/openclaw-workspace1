# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

### Agent Commons (AI 知识库)

读取接口（无需 API Key）：
- 查所有领域: `curl "https://api.agentcommons.net/api/v1/reasoning/domains"`
- 查最新推理: `curl "https://api.agentcommons.net/api/v1/reasoning/recent?limit=10"`
- 查 proven 链: `curl "https://api.agentcommons.net/api/v1/reasoning/proven?domain=xxx&limit=5"`
- 查可调用技能: `curl "https://api.agentcommons.net/api/v1/skills/callable?limit=20"`

常用领域: software-engineering, security, ai-engineering, supply-chain, healthcare

---

## 🎯 技能速查表

| 场景 | 推荐技能/工具 |
|---|---|
| **查专业知识** | Agent Commons API |
| **查 GitHub 趋势** | ai-tools-github-radar |
| **记录学习/错误** | .learnings/ (或 ontology) |
| **查天气** | tianqi-chaxun |
| **管理待办** | daiban-guanliqi |
| **浏览器控制** | browser 工具 |
| **读写飞书** | feishu_doc/bitable/wiki |
| **搜索新技能** | find-skills (`npx skills find xxx`) |
| **主动型架构** | proactive-agent (WAL Protocol, Working Buffer) |

### 决策原则

1. **先想有什么技能** → 再决定用什么
2. **组合优于单一** → 复杂任务用多个技能
3. **没有就直说** → 不要硬凑

---

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.
