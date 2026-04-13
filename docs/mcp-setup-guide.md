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
3. Note the port (default HTTPS: `27124`)

## Step 2: Register MCP Server (user scope)

**Recommended — user scope** so MCP works from any CWD:

```bash
claude mcp add-json --scope user obsidian '{"command":"npx","args":["-y","obsidian-mcp-server"],"env":{"OBSIDIAN_API_KEY":"<your-api-key>","OBSIDIAN_BASE_URL":"https://127.0.0.1:27124","OBSIDIAN_VERIFY_SSL":"false"}}'
```

**Required env vars:**
| Var | Value | Note |
|-----|-------|------|
| `OBSIDIAN_API_KEY` | API key from plugin settings | Required |
| `OBSIDIAN_BASE_URL` | `https://127.0.0.1:27124` | Must be `BASE_URL`, NOT `API_URL` |
| `OBSIDIAN_VERIFY_SSL` | `false` | Disable cert verify for self-signed |

**Scope options:**
- `--scope user` → works from any directory (recommended)
- `--scope project` → creates `.mcp.json` in CWD, only works when `cd` into that folder
- `--scope local` → user + directory specific

First-run note: `npx -y obsidian-mcp-server` auto-downloads package (~30s first time).

## Step 3: Verify Connection

```bash
claude mcp list
```

Expected output:
```
obsidian: npx -y obsidian-mcp-server - ✓ Connected
```

If `✗ Failed to connect`:
- Test REST API directly: `curl -k -H "Authorization: Bearer <key>" https://127.0.0.1:27124/`
- `000` → Obsidian not running or plugin not enabled
- `401` → Wrong API key
- `200` → REST API OK but MCP config wrong (check env var names)

Then restart Claude Code session so MCP tools load into context.

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
