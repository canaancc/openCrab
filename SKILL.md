---
name: opencrab
description: "Scaffold a Claude Code agent workspace with Telegram bridge in minutes. Use when: (1) user says 'create agent', 'new agent', 'scaffold agent', 'opencrab', (2) user wants to set up a new AI agent with Telegram bot integration."
---

# openCrab -- Agent Scaffolding for Claude Code

Create a fully functional Claude Code agent workspace through guided conversation.

**What you get in ~5 minutes:**
- A workspace with CLAUDE.md (identity, memory, self-improvement, safety)
- Optional Telegram bridge with streaming, session persistence, and cron scheduling
- macOS launchd / Linux systemd service for auto-start
- Ready to use: `cd workspace && claude` or chat via Telegram

## Workflow

### Phase 1: Identity (required)

Ask ONE question at a time. Wait for the answer before proceeding.

1. **Name & Emoji**: "What's the agent's name? Pick an emoji too (e.g., Sentinel 🛡️)"
2. **Role**: "Describe in one sentence what this agent does."
3. **Vibe**: "What's the personality? (e.g., strict security expert / friendly assistant / silent efficient executor)"
4. **Language**: "What language should the agent use? (e.g., English / 中文 / mixed)"
5. **Nickname**: "How should the agent call you? (e.g., Boss, Captain, your name — or skip for no nickname)"
6. **Workspace**: "Where should the workspace live? (e.g., ~/sentinel)"

### Phase 2: Capabilities (required)

7. **Primary tools**: "What tools does this agent need? Options:
   - File ops (Read/Write/Edit/Glob/Grep)
   - Shell (Bash)
   - Web (WebSearch/WebFetch)
   - All of the above (default)"
8. **Domain**: "Any specific domain knowledge? (e.g., crypto/DeFi, web dev, data analysis — or skip)"

### Phase 3: Safety (required)

9. **Boundaries**: "What should this agent NEVER do? (e.g., never delete files, never execute transfers — or use defaults)"

### Phase 4: Bridge (optional)

10. **Telegram**: "Want a Telegram bot for this agent? (y/n)"
   - If yes: "Provide bot token (from @BotFather):"
   - Then: "Your Telegram user ID (for access control):"
   - If no: skip, tell user they can add it later

### Phase 5: Generate

After collecting all answers:

1. Read the CLAUDE.md template: `templates/claude-md.md`
2. Read the bridge template: `templates/bridge.py`
3. Create the workspace:

```
{workspace}/
├── CLAUDE.md              # Generated from template with user's answers
├── MEMORY.md              # Empty, with header
├── memory/                # Daily notes directory
├── tasks/
│   └── lessons.md         # Self-improvement log, with header
```

4. If Telegram bridge requested:

```
{workspace}/.bot/
├── bridge.py              # Generated from bridge template
├── cron_runner.py         # Standalone cron executor
├── .env                   # TOKEN + ALLOWED_USERS
├── requirements.txt       # Dependencies
```

   The bridge uses **system crontab** for scheduled tasks (`/every`, `/jobs`, `/cancel`).
   `cron_runner.py` runs independently — even if bridge is down, scheduled jobs still fire.

5. **Install dependencies & create venv**:
   ```bash
   cd {workspace}/.bot
   uv venv && uv pip install -r requirements.txt
   # If uv not available: python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
   ```

6. **Set up persistent background service**:

   **macOS (launchd):**
   - Read the template: `templates/launchd.plist`
   - Generate `~/Library/LaunchAgents/com.{agent_id}.bridge.plist`
   - Load: `launchctl load ~/Library/LaunchAgents/com.{agent_id}.bridge.plist`

   **Linux (systemd):**
   - Read the template: `templates/systemd.service`
   - Generate `~/.config/systemd/user/{agent_id}-bridge.service`
   - Enable: `systemctl --user enable --now {agent_id}-bridge`

7. Show the user:
   - Summary of what was created
   - How to start: `cd {workspace} && claude` (CLI) or Telegram bot (auto-started)
   - Scheduled tasks: `/every 8:00 morning briefing`
   - Service management commands
   - Remind: "Edit CLAUDE.md anytime to adjust the agent's behavior"

## Generation Rules

- `requirements.txt` content for bridge:
  ```
  python-telegram-bot>=21.0
  python-dotenv
  claude-agent-sdk
  httpx
  ```
- Fill template variables from user's answers, do NOT leave any `{{placeholders}}`
- `{{AGENT_ID}}` = lowercase agent name (e.g., "crab", "sentinel")
- `{{HOME}}` = user's home directory (e.g., "/Users/username" or "/home/username")
- `{{USER_NICKNAME}}` = how the agent calls the user (from Phase 1 Q5, remove line if skipped)
- Memory section is always included
- Self-improvement section is always included (lessons.md + Gotchas refinement loop)
- Gotchas section in CLAUDE.md: auto-refined from lessons.md when same error type ≥3 times. This ensures recurring mistakes become permanent behavioral rules without manual intervention
- Safety section uses user's boundaries + sensible defaults
- Keep CLAUDE.md under 150 lines
- Use user's chosen language for CLAUDE.md content
- `{{USER_NICKNAME}}` in bridge.py: fill with user's answer from Phase 1 Q5. If skipped, remove the "Call the user..." line
- Bridge must respond in the same language the user writes in — if they send English, reply in English; if Chinese, reply in Chinese
- On macOS: use launchd; on Linux: use systemd
- Add `.bot/` to `.gitignore` if workspace is a git repo (contains sensitive .env)

## Security Notes

- The bridge runs with `permission_mode: "bypassPermissions"` — the agent can execute any tool without user approval
- Access is controlled by `ALLOWED_USERS` whitelist in `.env`
- For financial operations, the bridge template includes a confirmation flow (user must reply to confirm)
- **Never commit `.env` files** — they contain bot tokens
- Consider network security: the bridge connects to Telegram servers, ensure your environment allows outbound HTTPS
