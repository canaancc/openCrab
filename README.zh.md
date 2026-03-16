# openCrab 🦀

**5 分钟创建一个带 Telegram 的 Claude Code Agent。**

给你的 AI agent 一个家、一个大脑、一个电话号码。

## 你会得到什么

```
my-agent/
├── CLAUDE.md         # Agent 身份、记忆、安全规则
├── MEMORY.md         # 长期记忆
├── memory/           # 每日工作日志
├── tasks/
│   └── lessons.md    # 自我改进日志
└── .bot/             # Telegram 桥接（可选）
    ├── bridge.py     # 流式聊天桥接
    ├── cron_runner.py # 定时任务执行器
    └── .env          # Bot token & 访问控制
```

**特性：**
- 交互式引导创建 — 回答问题，获得可用的 agent
- Telegram 流式输出 + 实时工具执行状态
- 跨对话 session 持久化
- Telegram 内定时任务（`/every 8:00 morning briefing`）
- 开机自启（macOS launchd / Linux systemd）
- 内置记忆系统（每日笔记 + 长期记忆）
- 自我改进循环（从纠正中学习）

## 快速开始

### 作为 Claude Code Skill 使用（推荐）

```bash
# 安装
git clone https://github.com/canaancc/openCrab.git ~/.claude/skills/opencrab

# 在 Claude Code 中使用
> /opencrab
```

Claude 会引导你完成交互式设置：

1. **名字和 emoji** — "Sentinel 🛡️"
2. **角色** — "安全监控 agent"
3. **性格** — "严谨但友善"
4. **语言** — 中文、English 或混合
5. **工作目录** — `~/sentinel`
6. **工具** — 文件操作、Shell、Web 或全部
7. **领域** — crypto、web 开发等
8. **安全边界** — agent 绝对不能做的事
9. **Telegram bot** — 可选，提供 @BotFather token

完成。你的 agent 已就绪。

## Telegram 桥接

### 命令

| 命令 | 说明 |
|------|------|
| `/start` | 显示可用命令 |
| `/new` | 开始新 session |
| `/status` | 查看当前 session |
| `/check` | 查看后台任务 |
| `/every <时间> <提示词>` | 创建定时任务 |
| `/jobs` | 列出定时任务 |
| `/cancel <id>` | 取消定时任务 |

### 时间格式

| 格式 | 含义 | 示例 |
|------|------|------|
| `30m` | 每 30 分钟 | `/every 30m check server` |
| `8:00` | 每天 8:00 | `/every 8:00 morning briefing` |
| `1d` | 每天午夜 | `/every 1d daily report` |

## 前置要求

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk)（`pip install claude-agent-sdk`）
- Python 3.11+（Telegram 桥接）
- Telegram bot token（可选，从 [@BotFather](https://t.me/BotFather) 获取）

## 安全说明

- **`bypassPermissions`**：Telegram 桥接无需工具审批，agent 自主运行
- **`ALLOWED_USERS`**：仅白名单用户可交互
- **资金操作**：模板包含确认流程，执行前必须用户确认
- **不要提交 `.env`**：包含 bot token

## 许可证

[MIT](LICENSE)

---

基于 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 和 [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk) 构建。
