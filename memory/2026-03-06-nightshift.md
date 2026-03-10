# 2026-03-06 Night Shift Learning

## 20:07 学习记录

### 学习点 1: OpenClaw Skills 系统
- **来源**: https://docs.openclaw.ai/tools/skills
- **要点**:
  - Skills 使用 AgentSkills 兼容格式，每个 skill 是包含 SKILL.md 的目录
  - SKILL.md 必须包含 YAML frontmatter (name, description, metadata)
  - 加载优先级: workspace > ~/.openclaw/skills > bundled skills
  - 支持 per-agent (workspace内) 和 shared skills
  - 可通过 ClawHub 安装同步: `clawhub install <skill-slug>`
  - metadata 可配置 gating: requires bins/env/config 条件

### 学习点 2: Browser 工具 - 隔离浏览器
- **来源**: https://docs.openclaw.ai/tools/browser
- **要点**:
  - openclaw profile: OpenClaw 管理的独立浏览器（无需扩展）
  - chrome profile: 扩展 relay 连接到系统浏览器（需 OpenClaw 扩展）
  - 配置: browser.enabled, browser.defaultProfile, browser.executablePath
  - 支持多 profile: openclaw, work, remote 等
  - CDP 端口自动分配，可远程控制
  - SSRF 保护: browser.ssrfPolicy.dangerouslyAllowPrivateNetwork

### 学习点 3: Nodes 设备配对与远程执行
- **来源**: https://docs.openclaw.ai/nodes
- **要点**:
  - Node 是连接到 Gateway 的 companion 设备 (macOS/iOS/Android)
  - 配对流程: openclaw devices list → approve → nodes status
  - 远程 node host: Gateway 转发 exec 到远程机器执行
  - 启动 node: `openclaw node run --host <gateway-host>`
  - SSH tunnel 模式支持 loopback-only Gateway
  - Exec approvals 在 node 本地: ~/.openclaw/exec-approvals.json
  - 配置 host=node 即可将 exec 定向到远程设备

### 学习点 4: Tavily AI 搜索
- **来源**: ~/.openclaw/workspace/skills/tavily/SKILL.md
- **要点**:
  - 专为 LLM 优化的搜索 API
  - 搜索模式: basic (1-2s) vs advanced (5-10s)
  - 主题过滤: general (全网) vs news (最近7天)
  - 域名过滤: --include-domains / --exclude-domains
  - 支持 AI Answer 生成和 Raw Content 提取
  - 对比: 比 Brave 更好的 AI 摘要，比 Perplexity 更可控的源
  - 获取 API key: https://tavily.com (tvly- 开头)
