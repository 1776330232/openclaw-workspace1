# Errors

> Command failures, exceptions

---

## [ERR-20260301-001] powershell_json_escape

**Logged**: 2026-03-01T16:39:00+08:00
**Priority**: high
**Status**: resolved
**Area**: config

### Summary

PowerShell 写入 JSON 文件导致转义字符问题，Claude Code 无法解析配置。

### Error

Claude Code 登录失败，要求重新登录，即使配置了 MiniMax API。

### Context

- 操作：修改 ~/.claude/settings.json
- 输入：PowerShell 字符串拼接的 JSON
- 环境：Windows, Claude Code 2.1.63

### Suggested Fix

使用 Python 写入正确的 JSON 格式：
```python
import json
with open('settings.json', 'w') as f:
    json.dump(settings, f, indent=2)
```

### Metadata

- Reproducible: yes
- Related Files: ~/.claude/settings.json

### Resolution

- **Resolved**: 2026-03-01T16:40:00+08:00
- **Notes**: 用 Python 重写了 settings.json，问题解决
