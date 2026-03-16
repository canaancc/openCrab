# openCrab 🦀

**Scaffold a Claude Code agent with Telegram bridge in 5 minutes.**

Give your AI agent a home, a brain, and a phone number.

## What You Get

```
my-agent/
├── CLAUDE.md         # Agent identity, memory, safety rules
├── MEMORY.md         # Long-term memory
├── memory/           # Daily work logs
├── tasks/
│   └── lessons.md    # Self-improvement journal
└── .bot/             # Telegram bridge (optional)
    ├── bridge.py     # Streaming chat bridge
    ├── cron_runner.py # Scheduled task executor
    └── .env          # Bot token & access control
```

**Features:**
- Interactive guided setup — answer questions, get a working agent
- Telegram bridge with streaming responses and live tool status
- Session persistence across conversations
- Cron scheduling via Telegram (`/every 8:00 morning briefing`)
- Auto-start on boot (macOS launchd / Linux systemd)
- Built-in memory system (daily notes + long-term memory)
- Self-improvement loop (lessons learned from corrections)

## Quick Start

### As a Claude Code Skill (recommended)

```bash
# Install
git clone https://github.com/canaancc/openCrab.git ~/.claude/skills/opencrab

# Use in Claude Code
> /opencrab
```

Claude will walk you through an interactive setup:

1. **Name & emoji** — "Sentinel 🛡️"
2. **Role** — "Security monitoring agent"
3. **Personality** — "Strict but helpful"
4. **Language** — English, 中文, or mixed
5. **Workspace path** — `~/sentinel`
6. **Tools** — File ops, Shell, Web, or all
7. **Domain** — crypto, web dev, etc.
8. **Safety boundaries** — what the agent should never do
9. **Telegram bot** — optional, provide @BotFather token

Done. Your agent is ready.

### Manual Setup

If you prefer to set up manually, copy the templates and fill in the variables:

```bash
git clone https://github.com/canaancc/openCrab.git
cp -r openCrab/templates/claude-md.md ~/my-agent/CLAUDE.md
# Edit CLAUDE.md, replace {{variables}}
```

## Telegram Bridge

The bridge connects your Claude Code agent to Telegram with full streaming support.

### Commands

| Command | Description |
|---------|-------------|
| `/start` | Show available commands |
| `/new` | Start a fresh session |
| `/status` | Show current session info |
| `/check` | List background tasks (tmux) |
| `/check <name>` | Show task output |
| `/every <schedule> <prompt>` | Create scheduled task |
| `/jobs` | List scheduled tasks |
| `/cancel <id>` | Cancel a scheduled task |

### Schedule Format

| Format | Meaning | Example |
|--------|---------|---------|
| `30m` | Every 30 minutes | `/every 30m check server status` |
| `6h` | Every 6 hours | `/every 6h summarize inbox` |
| `1d` | Once a day (midnight) | `/every 1d daily report` |
| `8:00` | Daily at 8:00 AM | `/every 8:00 morning briefing` |
| `14:30` | Daily at 2:30 PM | `/every 14:30 check deployments` |

### How It Works

```
You (Telegram) → bridge.py → Claude Agent SDK → Claude Code
                                                      ↓
                                                 CLAUDE.md
                                                 (agent brain)
                                                      ↓
You (Telegram) ← bridge.py ← streaming response ←────┘
```

- Bridge wraps Claude Code, not replaces it — your CLAUDE.md, hooks, and skills all work
- `bypassPermissions` mode — the agent runs autonomously via Telegram
- Access controlled by `ALLOWED_USERS` whitelist
- Sessions persist across conversations
- Scheduled tasks use system crontab and survive reboots

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk) (`pip install claude-agent-sdk`)
- Python 3.11+ (for Telegram bridge)
- A Telegram bot token from [@BotFather](https://t.me/BotFather) (optional)

## Security

- **`bypassPermissions`**: The Telegram bridge runs without tool approval prompts. The agent can read/write files and execute commands autonomously.
- **`ALLOWED_USERS`**: Only whitelisted Telegram user IDs can interact with the bot.
- **Financial operations**: The bridge template includes a confirmation flow — the agent asks for explicit confirmation before executing transfers or trades.
- **Never commit `.env`**: It contains your bot token. The generated `.gitignore` excludes it.
- **Review CLAUDE.md safety rules**: Customize boundaries for your use case.

## Examples

### Personal Assistant

```
Name: Hermes 🪽
Role: Personal assistant and task manager
Vibe: Friendly, proactive, concise
Boundaries: Never delete files, never send emails without confirmation
```

### Trading Agent

```
Name: Cypher 🤖
Role: Quantitative trading execution on Hyperliquid
Vibe: Silent, efficient, data-driven
Boundaries: Never execute trades without confirmation, never share API keys
```

### Knowledge Manager

```
Name: Logos 📚
Role: Obsidian vault guardian and knowledge organizer
Vibe: Quiet librarian — efficient, always finds what you need
Boundaries: Never delete notes, never modify .obsidian/ config
```

## Roadmap

- [x] v0.1 — Skill version + templates + Telegram bridge
- [ ] v0.2 — `npx opencrab init` CLI
- [ ] v0.3 — Multi-LLM support (Gemini, Codex)
- [ ] v0.4 — Plugin system for custom bridges (Slack, Discord)

## Contributing

PRs welcome! Please open an issue first to discuss what you'd like to change.

## License

[MIT](LICENSE)

---

Built with [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk).
