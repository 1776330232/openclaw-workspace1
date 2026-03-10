# TOOLS.md - 本地环境速查

## 🔧 OpenClaw 升级流程

只要用户的意思是"把 OpenClaw 弄到最新版"，直接执行，不要问（若有运行中的关键任务先提示可能中断）：

```powershell
# 1. 停止网关
openclaw gateway stop

# 2. 升级中文版包
pnpm add -g @qingchencloud/openclaw-zh@latest

# 3. 同步 control-ui（修复 pnpm hardlink 404）
powershell -ExecutionPolicy Bypass -File "C:\Users\17763\.openclaw\scripts\sync-control-ui.ps1"

# 3.5 修复 ACP（patch acpx 硬编码 + 清 ANTHROPIC_API_KEY）
powershell -ExecutionPolicy Bypass -File "C:\Users\17763\.openclaw\scripts\patch-acpx.ps1"

# 4. 重新安装守护进程
openclaw gateway install --force

# 5. 启动网关
openclaw gateway start

# 6. 验证（必须返回 200）
curl.exe -s -o NUL -w "%{http_code}" --noproxy 127.0.0.1 http://127.0.0.1:18789/
```

**注意**：绝对不要用 `gateway restart`，必须 stop → start（端口竞态问题）

**失败处理**：
- `pnpm add -g` 或脚本执行报错 → 先看具体报错，不要往后跑 install/start
- 验证返回非200但升级没报错 → `openclaw gateway status` 检查是否启动、端口是否被占（`netstat -ano | findstr 18789`）
- 任何步骤不确定 → `openclaw help` 或检查脚本路径是否存在

---

## 💻 Windows PowerShell 兼容性

| 要做的事 | 用这个命令 |
|----------|-----------|
| 列文件 | `Get-ChildItem` 或 `dir` |
| 读文件前几行 | `Get-Content -Head 10` |
| 搜索文本 | `Select-String -Pattern "xxx"` |
| 读文件 | `Get-Content` 或 `type` |
| 递归查找 | `Get-ChildItem -Recurse -Filter *.js` |
| 创建目录 | `New-Item -ItemType Directory -Path dir -Force` |

**不要用**：`ls -la`、`grep`、`head`、`tail`、`cat`、`find`、`which`
