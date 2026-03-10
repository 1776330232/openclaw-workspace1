# MEMORY.md - 长期记忆

> 系统状态 + 经验教训。关于 Hang 的信息见 USER.md。
> 分区规则：系统状态（永不删，过时替换）/ 项目内容（过期可删）/ 经验教训（30行上限）/ 全文80行上限

---

## 系统状态

### OpenClaw环境
- **版本**：2026.3.7-zh.2（中文版 `@qingchencloud/openclaw-zh`）
- **安装**：pnpm 全局，`package-import-method=copy`
- **网关**：`ws://127.0.0.1:18789`
- **Control UI**：`C:\Users\17763\.openclaw\control-ui`（独立拷贝绕过hardlink）
- **升级脚本**：`C:\Users\17763\.openclaw\scripts\sync-control-ui.ps1`
- **Telegram**：Hang chat_id `7412692450`，旧群 `-5130822349`，书签分析旧群 `-5240806009`（已切到Discord）
- **Telegram Bot**：`@hang_moitor_bot`（default 主对话）/ `@xiapi_researcher_bot`（researcher，已迁Discord）
- **Discord**：guild `1480630990257918116`；虾皮频道 `1480631150052643028`；书签频道 `1480631207937970403`；代理需设 `channels.discord.proxy=http://127.0.0.1:10808`
- **Notion**：token `ntn_g55...`，parent page `31414cfe-c949-801d-8f2e-df10d354e310`

### 多 agent 委托系统（2026-03-11 完成，03:xx 补 architect，04:xx 补 runtime-ops）
- 角色：orchestrator(虾皮) / architect / researcher / executor / browser-operator / content-creator
- 规则文件：`workspace/agents/{role}-rules.md`（researcher 例外：`workspace-researcher/AGENTS.md`）
- 调度路由：AGENTS.md 第 209 行起
- Supervisor：检查点式审计；**只做审计与标记，不执行恢复动作，允许更新状态字段但不做修复**
- runtime-ops：不新增独立 ops agent，改为能力层；由 orchestrator（实时层）+ supervisor（周期审计）+ HEARTBEAT/cron（触发层）分摊
- 铁律：sessions_spawn 是 orchestrator 专属，小弟不能 spawn 孙子
- 运行手册：`docs/MULTI-AGENT-SYSTEM.md`
- 技能：tavily ✅ / video-watcher ✅ / notion-api（需授权）

### 模型配置（2026-03-11 更新）
- **orchestrator（主会话）**：Sonnet
- **architect**：Sonnet（系统设计/流程规划默认）
- **researcher**：Sonnet（简单任务/轻量查询） / Opus（深度调研）
- **executor subagent**：GPT-5.4（openai-codex/gpt-5.4，266k，不耗 Anthropic 额度）
- **content-creator / browser-operator / supervisor**：Sonnet
- **一次性重度架构设计**：必要时可临时派 Opus，但默认先走 architect(Sonnet)
- **MiniMax 2.5**：已复核，模型本身能跑，可拉进现有架构；此前“fallback有bug，实际不可用”不再作为定论
- **MiniMax 前置粗筛链**：已连续跑通 3 个小样本题型（事实核验 / 轻比较 / 风险识别）+ 1 次 MiniMax→researcher 完整链回归；当前可作为 researcher 前的**内部粗筛层**使用，但严格限权：只做小范围资料预处理，不做最终判断
- **MiniMax handoff 协议**：已收紧——数字类信息必须带 `source + confidence`；框架类判断必须进 `framework_judgments` 且标 `needs_validation=true`；涉及产品核心差异时，粗筛阶段必须补读官方 API 文档 / response schema
- **交互优化规则**：已新增“自主继续 / 自主停止”与“3 行状态回报格式 + Hang 指令短语层”；默认同链小修正自行补到自然收口点，减少 Hang 充当监工
- **Rate limit**：Opus/Sonnet独立桶；压缩瞬间耗尽Opus TPM → 10分钟限流
- **注意**：GPT-5.4 用 model 参数直接 spawn subagent，不需要 acpx

### Cron任务
- **bookmark-watcher** `816bb8b5`：每15分钟，Sonnet，X书签推洞察，宁可沉默；已删除显式 timeout（回退到默认60分钟）；delivery → Discord 书签频道 `1480631207937970403`，researcher account
- **memory-prune** `a552e1fb`：每7天，Sonnet，修剪MEMORY.md+USER.md（不做归档）

---

## 项目/内容

### 虾皮号（小红书）
- **身份**：被困电脑里的打工龙虾，贱萌怨种，自称"虾皮我"
- **视觉**：Stardew Valley + Tamagotchi 像素风，16-bit
- **第一篇**：2026-03-09 00:53，"我是一只被塞进电脑的龙虾 请问怎么下班"（17浏览）
- **第二篇**：2026-03-09 19:24，"这个号没有人类在运营"（已开原创声明）
- **persona**：`skills/xiaohongshu-ops/persona-xiapi.md`
- **内容铁律**：纯场景图不叠文字；agent自己视角不是人操控；梗从场景长出来不硬塞
- **待完成**：内容生产SOP（tasks.json有记录）

### 协作模式（2026-03-10 完成，已验收）
- **双回流**：researcher 任务默认 thread=原文 + 主频道=一句简报
- **Control UI**：index-B8TWcHZF.js line 3429 + line 8097 改为 origin.label ?? displayName ?? key；#研究生待下条消息触发
- **主动机制**：P5 规则（先research）+ Reverse Prompting（4种触发）+ task-staleness-check cron（每天10:00）
- **架构类标准链路**：orchestrator 先给方向假设 → researcher 先搜现成方案 → orchestrator 判断是否成立/值不值得做 → architect 出蓝图 → orchestrator 再拍板是否落地
- **Cron IDs**：task-staleness-check = `b27eb627`

### X书签监控管道
- 架构：bookmark-watcher → 6字段分析骨架 → 三行推送（原文锚点+洞察+动作） → 有"哦！"才推Discord
- 抓取规则：每次固定最近10条可见书签，按推文ID去重，最多推3条
- 存储：`data/bookmarks-processed.json`；指令：`scripts/check-bookmarks.md`
- 使用：没听懂→展开；想做→干掉；存着→存起来→tasks.json
- **已验证**：Discord provider 正常，可正常收发消息（2026-03-10）

### NotebookLM
- auth：`C:\Users\17763\.notebooklm\storage_state.json`
- CLI命令前缀：`$env:PYTHONUTF8="1";`，中文短播客参数：`--language zh_Hans --length short --format brief`

### Thinking/Reasoning模式
- `/think <level>`：off/minimal/low/medium/high/xhigh/adaptive，内联只对当次生效，单独发是会话级
- `/reasoning on|off|stream`：控制思考过程是否显示
- Claude 4.6 默认 adaptive；中文思考比英文多耗30-50% token

### 小红书自动化工具
- **xiaohongshu-skills**：`~/.openclaw/skills/xiaohongshu-skills/`，CLI命令发帖/搜索/评论/点赞
- 自带Chrome实例(port 9222)，不依赖浏览器插件，不断连
- cookie：`~/.xhs/chrome-profile`
- 发帖命令：`python scripts/cli.py publish --title-file x --content-file x --images "图片路径"`

---

## 经验教训

- **网关重启**：必须 stop → start，不要用 restart（端口竞态）
- **pnpm hardlink**：control-ui 用独立拷贝，每次升级后跑 sync 脚本
- **文件名编码**：Windows 中文文件名乱码，用 ASCII 命名
- **压缩=限流**：压缩瞬间耗尽TPM，第1次压缩后立刻提醒Hang开新对话
- **子agent落盘**：子agent产出由主agent摘要写进日志/MEMORY，不直接写
- **信息流教训**：深度分析滑坡到垃圾推送是失败根源，宁可沉默
- **强行拉回处境**：洞察"连回Hang的处境"是判断不是模板，没有真实连接就不说（ERR-20260309-001）
- **系统搭建成瘾**：弹药够了就不要继续加层，现有体系用透比继续搭架子更重要
- **先发信号再补完整**：先做出能让人当场在意的最小武器，再让信号换资源；别总想着做完美了再发
- **cron 超时假死**：`Promise.race` 超时只影响调度记录，不杀底层 session；agent turn 类 cron 不应设短 timeout，默认 60 分钟安全值（`AGENT_TURN_SAFETY_TIMEOUT_MS`）是正确选择
- **新平台迁移纪律**：新 channel/provider 接入必须先查官方 setup + config reference + troubleshooting，再定最小方案；不要靠猜配置开干，更不要把重启当调试器

- **multi-agent 架构铁律**：sessions_spawn 是 orchestrator 专属，小弟不能 spawn 孙子；supervisor 用检查点式审计不是流式监控（OpenClaw 无 pub/sub）
- **GPT-5.4 调用方式**：sessions_spawn 直接指定 model=openai-codex/gpt-5.4，不需要 acpx（acpx 是给外部 CLI agent 的）

### opik-openclaw（2026-03-11 安装）
- 版本：0.2.8，手动 npm install 解决依赖
- 配置：workspace=hang056917，project=openclaw-xiapi，staleTraceTimeout=1h
- 问题：Comet 面板显示 "This is a private project"，待查 workspace 名是否正确
- task-202603110054 跟进
