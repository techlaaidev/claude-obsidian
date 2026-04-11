# Obsidian + Claude Code Integration Research Report

**Date:** 2026-04-11  
**Topic:** Using Obsidian as a knowledge management tool for Claude Code workflows  
**Report Type:** Technical Research & Feasibility Analysis

---

## Executive Summary

Obsidian and Claude Code form a powerful pairing for AI-assisted development and knowledge management. Two primary integration patterns exist: **(1) Plugins embedded in Obsidian** that invoke Claude Code from the UI, and **(2) MCP (Model Context Protocol) servers** that connect Claude Code to Obsidian vaults for bidirectional access. The integration enables persistent project memory, context retention across sessions, and AI-assisted documentation/code generation.

**Recommendation:** Use MCP-based integration (obsidian-mcp-server or mcpvault) + structured CLAUDE.md for production workflows. Use Obsidian plugins (Claudian, obsidian-claude-code) for lighter, interactive tasks within the vault.

---

## 1. Integration Patterns

### 1.1 Pattern A: Obsidian Plugins (Synchronous, UI-Driven)

Claude Code runs **inside Obsidian UI** via plugins that embed the CLI tool.

**Available Plugins:**
- **Claudian** ([GitHub](https://github.com/YishenTu/claudian)) — Integrates Claude Code CLI into Obsidian sidebar. Requires Claude API key or compatible provider (OpenRouter, Kimi). Best for: quick text edits, inline code generation.
- **obsidian-claude-code** ([GitHub](https://github.com/Roasbeef/obsidian-claude-code)) — Native plugin with full tool access (Read, Write, Edit, Bash, Grep, Glob, WebFetch, WebSearch). Stores API key locally. Best for: full-featured AI assistance within vault.
- **obsidian-claude-code-plugin** ([GitHub](https://github.com/deivid11/obsidian-claude-code-plugin)) — Interactive Claude Code integration (under Obsidian validation; not yet in community plugins). Supports context-aware understanding of vault.
- **CAO (Claude AI for Obsidian)** — Basic conversational Claude access within notes.
- **Agent Client** ([Forum](https://forum.obsidian.md/t/new-plugin-agent-client-bring-claude-code-codex-gemini-cli-inside-obsidian/108448)) — Multi-agent support (Claude, Codex, Gemini).

**Pros:**
- No terminal context switching needed
- Real-time UI feedback
- Vault context is implicit
- Fast for interactive tasks

**Cons:**
- Plugin stability/maintenance varies
- Limited async/background task support
- Requires vault to be open in Obsidian

### 1.2 Pattern B: MCP Servers (Asynchronous, CLI-Driven)

Claude Code runs **from terminal/CLI** and interacts with Obsidian via MCP protocol through the Obsidian Local REST API plugin.

**Available MCP Servers:**
- **obsidian-mcp-server** ([GitHub](https://github.com/cyanheads/obsidian-mcp-server)) — Comprehensive MCP implementation. Provides read/write/search/tag/frontmatter operations. Acts as bridge between Claude Code and Obsidian via REST API. **Maturity: Production-ready.**
- **mcpvault** ([GitHub](https://github.com/bitbonsai/mcpvault)) — Lightweight, safe implementation. Prevents YAML corruption. Works with Claude, ChatGPT, OpenCode, Cursor. **Maturity: Stable.**
- **smart-connections-mcp** ([GitHub](https://github.com/msdanyg/smart-connections-mcp)) — Semantic search + knowledge graphs using Smart Connections embeddings.

**Setup Requirements:**
1. Install Obsidian Local REST API plugin in vault
2. Configure MCP server config in `~/.claude/claude.json` or project `.claude/config.json`
3. Run MCP server as separate process or invoke from Claude Code session
4. Claude Code accesses vault via MCP tools

**Pros:**
- Claude Code runs from terminal (full control)
- Can operate without Obsidian UI open
- Better for batch operations and automation
- Async/scheduled tasks possible
- Direct integration with Claude Code's tool ecosystem

**Cons:**
- Requires REST API plugin + separate MCP server
- More setup complexity
- REST API plugin must be running in Obsidian

---

## 2. Obsidian Vault Structure for Claude Code

### 2.1 Recommended Folder Layout

```
vault/
├── CLAUDE.md                      # System prompt + vault rules
├── docs/
│   ├── code-standards.md
│   ├── architecture.md
│   ├── development-roadmap.md
│   └── project-changelog.md
├── projects/                      # Per-project documentation
│   ├── project-a/
│   │   ├── _index.md             # MOC (Map of Contents)
│   │   ├── design.md
│   │   ├── decisions.md
│   │   └── knowledge/
│   │       ├── patterns.md
│   │       ├── gotchas.md
│   │       └── references.md
│   └── project-b/
├── wiki/                          # Persistent knowledge base
│   ├── concepts/
│   │   ├── authentication.md
│   │   ├── caching.md
│   │   └── deployment.md
│   ├── technologies/
│   │   ├── nextjs.md
│   │   ├── postgres.md
│   │   └── docker.md
│   └── patterns/
│       ├── error-handling.md
│       ├── testing.md
│       └── logging.md
├── raw/                           # Source docs, research notes
├── memory/                        # Claude Code session snapshots
│   ├── session-20260411.md
│   ├── decisions-log.md
│   └── blockers.md
└── _index.md                      # Master index + search guide
```

### 2.2 Organizational Principles

**For AI-Assisted Development:**
- Keep files **atomic** (one concept per file, not 500-line monoliths)
- Use **bidirectional links** heavily instead of folder hierarchies (Obsidian strength)
- Create **MOC files** (Maps of Contents) to aggregate related topics
- Front-matter metadata for Claude: `tags: [[design-decision]], `status: active`, `last-reviewed: 2026-04-11`
- Use **dataview plugin** to query frontmatter across vault (Claude can read the generated tables)

**Vault is Claude-Readable, Not Claude-Writable (by default):**
- Claude reads existing knowledge to contextualize decisions
- Claude writes to session memory files (e.g., `memory/session-YYYYMMDD.md`)
- Avoid having Claude edit production docs directly (human review first)

---

## 3. CLAUDE.md: System Prompt for Vault

This is the **single most important file** for Claude Code + Obsidian workflows.

### 3.1 What CLAUDE.md Should Contain

```markdown
# CLAUDE.md - Vault System Prompt

## Vault Purpose
Brief statement of what this vault tracks (e.g., "Knowledge base for TechLa project").

## Folder Structure
- `docs/`: Project documentation and standards
- `projects/`: Per-project design docs and decisions
- `wiki/`: Reusable technical knowledge base
- `memory/`: Session snapshots and blockers log

## Rules for Claude Code
1. Always read `docs/code-standards.md` before writing code
2. Check `projects/[project-name]/_index.md` for active context
3. When creating new docs, link to existing wiki articles
4. Update `memory/decisions-log.md` with significant decisions
5. Flag blockers in `memory/blockers.md` immediately
6. Use H1 for page titles, H2 for sections

## Active Context (Update before each session)
- **Current Project:** TechLa (authentication module)
- **Current Phase:** Implementation of OAuth2 flow
- **Key Decision:** Using Passkey-based auth, not password reset
- **Blocker:** Integration test requires real Postgres setup

## Conventions
- Link format: `[[wiki/technologies/nextjs]]` (internal links)
- Code blocks include language: ` ```typescript `
- Decisions are logged in frontmatter: `decision: [[auth-oauth2-design]]`

## DO NOT
- Modify production docs directly; use session memory instead
- Delete old decisions (archive to `/archive`)
- Leave CLAUDE.md stale; refresh every session
```

### 3.2 Maintenance Critical

**CLAUDE.md Drift Problem:** The "Active Context" section becomes stale if not updated before each session. Stale instructions cause Claude Code to work against obsolete project state. **Solution:** Spend 5 minutes refreshing CLAUDE.md at the start of each session.

---

## 4. MCP Implementation Guide

### 4.1 Setup: obsidian-mcp-server

1. **Install Obsidian Local REST API plugin** in your vault:
   - Go to Obsidian → Settings → Community Plugins
   - Search "Local REST API"
   - Install and enable; note the port (default 27123)

2. **Add MCP configuration** to Claude Code:
   ```json
   // ~/.claude/claude.json or ./claude.json
   {
     "mcp_servers": {
       "obsidian": {
         "command": "npx",
         "args": ["@mcp-server/obsidian", "--port", "27123"]
       }
     }
   }
   ```

3. **Run Claude Code with MCP:**
   ```bash
   cd /path/to/project
   claude --mcp obsidian
   ```

### 4.2 What Claude Code Can Do via MCP

- **Read vault:** Search notes, query tags, read file content
- **Write vault:** Create notes, update existing ones, append to files
- **Query tags:** Find all notes tagged `[[project-auth]]`
- **Manage frontmatter:** Extract and update metadata
- **Search semantically:** Use obsidian-mcp-server's indexing

**Use Case Example:**
```
User: "Summarize all design decisions for the auth module"
Claude Code (via MCP):
1. Search vault for tag [[project-auth]]
2. Read all matching files
3. Extract decision frontmatter
4. Compile summary in memory/session-YYYYMMDD.md
5. Return summary to user
```

---

## 5. Plugin vs. MCP: Decision Matrix

| Dimension | Obsidian Plugin | MCP Server |
|-----------|-----------------|-----------|
| **Setup Complexity** | Low (install plugin) | Medium (REST API + MCP config) |
| **UI Integration** | Yes (sidebar/modal) | No (terminal only) |
| **Automation** | Limited (interactive) | Excellent (full async/batch) |
| **Vault Must Be Open** | Yes | No |
| **Use Case** | Quick edits, drafting | Full development workflows |
| **Maturity (Apr 2026)** | Medium (varies by plugin) | High (obsidian-mcp-server stable) |
| **Cost** | Free (plugins) | Free (servers) |
| **Scaling** | Single sessions | Multi-user/multi-project |

**Decision:** Use **MCP for production workflows** (CI/CD, batch operations, headless servers). Use **plugins for interactive work** (drafting, exploring notes).

---

## 6. Knowledge Base Patterns for Claude Code

### 6.1 Karpathy-Style Wiki Pattern

Inspired by Andrej Karpathy's AI knowledge structure:
- **wiki/concepts/** — Atomic concept notes (e.g., "OAuth2 Flow")
- **wiki/topics/** — Broader topic overviews that link to concepts
- **wiki/topics/_index.md** — Master table with status (draft/stable/deprecated)

Benefits: Claude can traverse and link concepts, build comprehensive answers from atomic pieces.

### 6.2 Session Memory Pattern

Each Claude Code session writes to `memory/session-YYYYMMDD.md`:
```markdown
# Session 2026-04-11

## Tasks Completed
- [ ] Implement OAuth callback endpoint
- [ ] Write unit tests for token refresh
- [ ] Update docs/architecture.md

## Decisions Made
- **Decision:** Use HS256 for JWT signing (not RS256)
  - Reason: Simpler key management for single auth server
  - Trade-off: Less suitable if auth server scales to multiple instances

## Blockers
- Real Postgres instance needed for integration tests (blocked until CI pipeline ready)

## Next Session
- Unblock: Set up test Postgres in Docker
- Continue: Implement profile endpoint
```

Claude Code reads this on next session to re-contextualize.

### 6.3 Decision Log

**File:** `memory/decisions-log.md`

```markdown
# Decision Log

## Decision: OAuth2 vs. Passkeys (2026-04-11)
- **Status:** Active (implemented)
- **Context:** [[projects/techla/auth-module]]
- **Choice:** Passkey-based, with OAuth2 fallback
- **Rationale:** Better UX, compliance with FIDO2 standards
- **Trade-off:** More complex initial setup
- **Reversible:** No (schema changes committed)
```

Claude Code references this to avoid re-debating resolved decisions.

---

## 7. Adoption Risk & Maturity Assessment

### 7.1 Maturity Status (April 2026)

| Component | Maturity | Notes |
|-----------|----------|-------|
| **Obsidian Core** | Production | Stable, widely adopted |
| **Obsidian Local REST API** | Production | Official plugin, reliable |
| **obsidian-mcp-server** | Production | ~6 months stable, active maintenance |
| **mcpvault** | Stable | Lightweight, well-maintained |
| **Claudian Plugin** | Stable | Community-driven, popular |
| **Claude Code** | Production | Anthropic official tool |

### 7.2 Adoption Risks

**Low Risk:**
- Using Obsidian as documentation storage (read-only for Claude)
- Plugins for interactive editing within vault
- CLAUDE.md system prompts

**Medium Risk:**
- MCP servers for automated vault writes (privacy/sync concerns)
- Tight coupling between project code and vault decisions
- Relying on plugin maintenance (community projects can be abandoned)

**High Risk:**
- Making vault the single source of truth (SPOF)
- Delegating entire knowledge base generation to Claude without review
- Storing credentials or sensitive data in vault

### 7.3 Breaking Changes / Abandonment Risk

- **Obsidian Local REST API:** Official, unlikely to break. Obsidian has 10+ year horizon.
- **obsidian-mcp-server:** Community project, but actively maintained and used by researchers. Abandonment risk = moderate. Mitigation: Fork if needed (open-source).
- **Claude Code:** Anthropic-official. Stable API, not going anywhere.
- **Obsidian Plugins:** Varies. Claudian is popular; obsidian-claude-code is newer (higher risk).

---

## 8. Concrete Recommendation

### For This Project (techla-project/claude-obsidian)

**Phase 1: Lightweight Setup (Week 1)**
1. Create vault structure (docs/, projects/, wiki/, memory/)
2. Write CLAUDE.md with vault purpose and rules
3. Install Claudian plugin for interactive work
4. Populate wiki/ with existing project knowledge (architecture, standards)

**Phase 2: MCP Integration (Week 2-3)**
1. Install Obsidian Local REST API plugin
2. Deploy obsidian-mcp-server locally
3. Configure Claude Code with MCP
4. Test: Claude Code reads vault via MCP, summarizes decisions

**Phase 3: Workflow Automation (Week 4+)**
1. Use MCP to auto-generate session memory from Claude Code runs
2. Set up `_index.md` queries (dataview) to track project status
3. Integrate vault into CI/CD for doc validation

**Why This Order:** UI-first (Claudian) gives you immediate value; MCP adds power later without disruption.

---

## 9. Code Examples & Quick Start

### 9.1 CLAUDE.md for TechLa Project

```markdown
# CLAUDE.md - TechLa Project Vault

## Purpose
Knowledge base + development memory for TechLa authentication module.

## Folder Guide
- `docs/`: Code standards, architecture, roadmap
- `projects/techla/`: Auth module design, decisions, references
- `wiki/`: Reusable tech knowledge (OAuth2, JWT, Postgres, etc.)
- `memory/`: Session logs and decision history

## Rules
1. Read `projects/techla/_index.md` at session start
2. Link to `wiki/` when documenting patterns
3. Log decisions in `memory/decisions-log.md`
4. Keep code comments DRY; link to wiki instead
5. Use `decision:` frontmatter for reversible choices

## Current Session Context
- **Phase:** OAuth2 endpoint implementation
- **Blocker:** None
- **Last updated:** 2026-04-11 14:30
```

### 9.2 Folder Structure Setup

```bash
mkdir -p docs projects/techla/knowledge wiki/{concepts,technologies,patterns} memory raw

# Create index files
touch docs/_index.md projects/techla/_index.md wiki/_index.md memory/decisions-log.md

# Create CLAUDE.md
cat > CLAUDE.md << 'EOF'
# CLAUDE.md - TechLa Project
[insert content from 9.1 above]
EOF
```

### 9.3 Sample MCP Request (Claude Code)

```bash
# Claude Code reads vault via MCP
claude --mcp obsidian

# In Claude Code session:
# "Read all decisions tagged [[project-auth]] and summarize conflicts"
# 
# Claude Code (via MCP tools):
# 1. query_vault: search tag [[project-auth]]
# 2. read_file: memory/decisions-log.md
# 3. read_file: projects/techla/design-decisions.md
# 4. Summarize and report
```

---

## 10. Unresolved Questions

1. **Vault Sync Across Devices:** How to keep vault in sync if using Claude Code on remote CI/CD? (Answer: Git-track vault, pull before MCP operations.)
2. **Performance at Scale:** Does obsidian-mcp-server handle large vaults (10K+ notes) efficiently? (Requires testing with real data.)
3. **Plugin Maintenance:** Which Obsidian Claude plugins will be maintained long-term? (Recommendation: Stick with obsidian-claude-code which is newer.)
4. **Sensitive Data:** Is it safe to store architecture diagrams, schema designs, or API keys in vault? (Best practice: No. Use `.gitignore` for sensitive vault folders.)
5. **MCP Server Hosting:** Can obsidian-mcp-server run on a headless server without Obsidian UI? (Requires Obsidian to be running somewhere; REST API plugin must be active.)

---

## Sources

- [One Developer Let Claude Code Loose on Five Years of Obsidian Notes](https://www.webpronews.com/one-developer-let-claude-code-loose-on-five-years-of-obsidian-notes-the-results-say-a-lot-about-where-ai-assisted-productivity-is-headed/)
- [Obsidian AI Second Brain: Complete Guide (2026)](https://www.nxcode.io/resources/news/obsidian-ai-second-brain-complete-guide-2026)
- [GitHub: obsidian-claude-code-mcp](https://github.com/iansinnott/obsidian-claude-code-mcp)
- [How to Build an AI Second Brain with Claude Code and Obsidian](https://www.mindstudio.ai/blog/build-ai-second-brain-claude-code-obsidian)
- [Combine Claude Code & Obsidian Notes for Persistent Project Memory](https://www.geeky-gadgets.com/claude-code-obsidian-vault-memory/)
- [Agent Client Plugin - Obsidian Forum](https://forum.obsidian.md/t/new-plugin-agent-client-bring-claude-code-codex-gemini-cli-inside-obsidian/108448)
- [GitHub: obsidian-claude-code](https://github.com/deivid11/obsidian-claude-code-plugin)
- [GitHub: claude-obsidian](https://github.com/AgriciDaniel/claude-obsidian)
- [Connect Claude AI with Obsidian: A Game-Changer for Knowledge Management](https://dev.to/sroy8091/connect-claude-ai-with-obsidian-a-game-changer-for-knowledge-management-25o2)
- [Connect Claude Code with Obsidian — Part 1](https://medium.com/@edwardanil/connect-claude-code-with-obsidian-part-1-f1c5c8199a02)
- [GitHub: Claudian](https://github.com/YishenTu/claudian)
- [CAO - Claude AI for Obsidian](https://www.obsidianstats.com/plugins/cao)
- [GitHub: obsidian-claude-code (Roasbeef)](https://github.com/Roasbeef/obsidian-claude-code)
- [Creating an Obsidian Plugin with Claude AI](https://www.stephanmiller.com/creating-an-obsidian-plugin-with-claude/)
- [Claude MCP for Obsidian using Rest API - Obsidian Forum](https://forum.obsidian.md/t/claude-mcp-for-obsidian-using-rest-api/93284)
- [GitHub: obsidian-mcp-server](https://github.com/cyanheads/obsidian-mcp-server)
- [GitHub: mcpvault](https://github.com/bitbonsai/mcpvault)
- [GitHub: obsidian-mcp-plugin](https://github.com/aaronsb/obsidian-mcp-plugin)
- [Smart Connections MCP - GitHub](https://github.com/msdanyg/smart-connections-mcp)
- [How to Create Your Own Knowledge Base with Obsidian](https://medium.com/@shehab2003magdy/how-to-create-your-own-knowledge-base-with-obsidian-cc16e2e206f5)
- [Build Your Own AI-Powered Knowledge Base with LLMs and Obsidian](https://dev.to/zaferdace/build-your-own-ai-powered-knowledge-base-with-llms-and-obsidian-18po)
- [Obsidian Developer Documentation](https://docs.obsidian.md/)
- [Claude Code Inside Obsidian: The Setup That 10x'd My Thinking](https://dev.to/numbpill3d/claude-code-inside-obsidian-the-setup-that-10xd-my-thinking-20e8)
- [I put Claude Code inside Obsidian, and it was awesome](https://www.xda-developers.com/claude-code-inside-obsidian-and-it-was-eye-opening/)
- [Build Your Second Brain With Claude Code & Obsidian](https://www.whytryai.com/p/claude-code-obsidian)
- [Tutorial: Obsidian Knowledge Base with Claude Code](https://marketingagent.blog/2026/03/28/tutorial-obsidian-knowledge-base-with-claude-code/)
- [3 Ways to Use Obsidian with Claude Code](https://awesomeclaude.ai/how-to/use-obsidian-with-claude)
