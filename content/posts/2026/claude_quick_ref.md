+++
title = 'Claude Code Quick Reference'
date = '2026-03-09'
draft = false
tags = ['dev','claude']
#author = 'adriano'
header_image = "/images/claude_code.png"
+++

## Claude Code Quick Reference

Since there are already plenty of tutorials on Claude Code, I decided to create a quick reference guide with all the info I've used so far.

### Starting a Session

| Command | Description |
| :--- | :--- |
| `claude` | Start a new interactive session |
| `claude -c` | Resume the previous session |
| `claude --resume` | Pick a past session to resume from a list |
| `claude "your task"` | One-shot query, no interactive session |
| `claude -p "query"` | Print mode (non-interactive, good for piping) |
| `claude update` | Update Claude Code to latest version |

## Permission Modes (at startup)

| Command | Description |
| :--- | :--- |
| `claude --permission-mode plan "task"` | Read-only — Claude plans but touches nothing |
| `claude --permission-mode acceptEdits "task"` | Auto-accepts file edits, still asks for bash commands |
| `claude --dangerously-skip-permissions "task"` | Full auto — no confirmations at all (YOLO mode) |

## Models

| Command | Description |
| :--- | :--- |
| `claude --model claude-sonnet-4-6` | Use Sonnet (default, fast, cheaper) |
| `claude --model claude-opus-4-6` | Use Opus (slower, more powerful, 5x cost) |
| `/model` | List available models inside a session |

## Plugins & MCP

| Command | Description |
| :--- | :--- |
| `claude plugin install code-simplifier` | Install official plugin |
| `claude mcp add mariadb -- <command>` | Add a MCP server |
| `/mcp list` | List active MCP servers inside a session |

```bash
claude mcp add my_server \
    -e MYSQL_HOST="192.168.1.3" \
    -e MYSQL_PORT="3306" \
    -e MYSQL_USER="root" \
    -e MYSQL_PASS='root' \
    -e MYSQL_DB="my_dabase" \
    -- npx @benborla29/mcp-server-mysql

claude mcp add mongodb / 
    -e MDB_MCP_CONNECTION_STRING='mongodb://admin:admin@192.168.1.3:27018/my_database' / 
    -- npx -y mongodb-mcp-server
```

## Inside a Session (slash commands)

| Command | Description |
| :--- | :--- |
| `/help` | List all available slash commands |
| `/memory` | View and edit Claude's memory files |
| `/config` | Open settings menu |
| `/compact` | Compress conversation history to save tokens |
| `/clear` | Clear the screen |
| `/init` | Bootstrap a `CLAUDE.md` from your codebase |
| `/export full_history.md` | Export full conversation to a Markdown file |
| `/plugin install <name>` | Install a plugin from inside a session |

## Inside a Session (keyboard shortcuts)

| Shortcut | Description |
| :--- | :--- |
| `Shift+Tab` | Cycle permission modes: default → acceptEdits → plan |
| `Ctrl+G` | Open Claude's plan in your editor to review/edit |
| `Option+Enter` | New line without sending (multi-line input) |
| `Ctrl+C` | Stop current generation |
| `Ctrl+D` | Exit Claude Code |

## File Input / Output

| Syntax | Description |
| :--- | :--- |
| `@specs/file.md` | Load a file as input context |
| `"write output to plan.md"` | Ask Claude to write output to a file |

## My Workflow Per Feature

```bash
# 1. Plan
claude --permission-mode plan "@specs/02-feature.md write a plan.md"

# 2. Review plan.md in editor (Ctrl+G or open manually)

# 3. Implement
claude --dangerously-skip-permissions -c "implement plan.md exactly as written"

git add . && git commit -m "feat: feature name"

