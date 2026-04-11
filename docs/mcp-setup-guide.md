---
tags: [guide, mcp, setup]
status: active
created: 2026-04-11
---

# MCP Setup Guide — Obsidian + Claude Code

Connect Claude Code to this Obsidian vault via MCP (Model Context Protocol).

## Prerequisites

- Obsidian installed and this vault opened
- Claude Code CLI installed
- Node.js 18+

## Step 1: Install Obsidian Plugins

Open Obsidian → Settings → Community Plugins → Browse:

| Plugin | Purpose | Required |
|--------|---------|----------|
| **Local REST API** | Exposes vault via HTTP (port 27123) | Yes |
| **Dataview** | Query frontmatter across notes | Recommended |
| **Templater** | Advanced templates with variables | Recommended |

After installing Local REST API:
1. Enable the plugin
2. Go to plugin settings → copy the **API Key**
3. Note the port (default: `27123`)

## Step 2: Install MCP Server

Option A — **obsidian-mcp-server** (full-featured, recommended):
```bash
npm install -g @cyanheads/obsidian-mcp-server
```

Option B — **mcpvault** (lightweight):
```bash
npm install -g mcpvault
```

## Step 3: Configure Claude Code

Add MCP config to `~/.claude/claude.json`:

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": [
        "-y",
        "@cyanheads/obsidian-mcp-server"
      ],
      "env": {
        "OBSIDIAN_API_KEY": "<your-api-key-from-step-1>",
        "OBSIDIAN_API_URL": "https://127.0.0.1:27123"
      }
    }
  }
}
```

Or for project-level config, add to `.claude.json` in project root.

## Step 4: Verify Connection

1. Open Obsidian (vault must be open with Local REST API active)
2. Start Claude Code: `claude`
3. Check MCP tools available: `/mcp`
4. Test: ask Claude to "list all notes in the vault"

## Available MCP Operations

Once connected, Claude Code can:

| Operation | Description |
|-----------|-------------|
| `search` | Full-text search across vault |
| `read` | Read note content by path |
| `write` | Create or update notes |
| `list` | List files in directories |
| `tags` | Query notes by tags |
| `frontmatter` | Read/update YAML frontmatter |

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Connection refused | Ensure Obsidian is open + Local REST API enabled |
| 401 Unauthorized | Check API key in env matches plugin settings |
| MCP tools not showing | Restart Claude Code session after config change |
| HTTPS cert error | Local REST API uses self-signed cert; MCP server handles this |

## Security Notes

- API key is stored in `~/.claude/claude.json` (not committed to git)
- Local REST API only listens on `127.0.0.1` (localhost)
- Do not expose port 27123 to network
