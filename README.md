# TechLa Knowledge Vault

Obsidian vault serving as persistent knowledge base for Claude Code development workflows.

## Quick Start

1. Open this folder as an Obsidian vault
2. Install community plugins: **Local REST API**, **Dataview**, **Templater**
3. Configure MCP — see `docs/mcp-setup-guide.md`
4. Update `CLAUDE.md` → Active Context before each Claude Code session

## Structure

```
├── CLAUDE.md        # System prompt for Claude Code
├── docs/            # Standards, architecture, guides
├── projects/        # Per-project docs and decisions
├── wiki/            # Reusable knowledge (concepts, tech, patterns)
├── memory/          # Session logs, decision history
├── templates/       # Note templates
├── raw/             # Research notes
└── plans/           # Implementation plans
```

## How It Works

- **Claude Code reads** vault via MCP (obsidian-mcp-server) or directly
- **CLAUDE.md** provides session context and vault rules
- **Wiki** stores reusable technical knowledge across projects
- **Memory** preserves decisions and session history across conversations
