---
title: TechLa Knowledge Vault — Project Overview & PDR
tags: [pdr, overview, vault]
status: active
created: 2026-04-11
---

# TechLa Knowledge Vault — Project Overview & PDR

## Purpose

Obsidian-based knowledge vault enabling Claude Code to access persistent, structured context across development sessions. Centralizes project documentation, decisions, wiki knowledge, and session logs.

## Key Features

- **MCP Integration**: Claude Code accesses vault notes via obsidian-mcp-server
- **Session Memory**: Preserves conversation history and context across sessions
- **Decision Logging**: Captures architecture decisions, rationales, and alternatives
- **Wiki Knowledge Base**: Reusable patterns, tech guides, and reference material
- **Project Tracking**: Per-project documentation, plans, and decision history
- **Template System**: Standardized formats for decisions, sessions, and articles

## Core Modules

| Module | Purpose |
|--------|---------|
| `docs/` | Standards, architecture, setup guides |
| `wiki/` | Reusable technical knowledge |
| `memory/` | Session logs, decision history |
| `projects/` | Per-project notes and decisions |
| `templates/` | Note templates for consistency |
| `raw/` | Research and scratch notes |
| `plans/` | Implementation plans and reports |

## Success Criteria

- Claude Code can read/search vault via MCP in < 1 second
- All session decisions logged within 24 hours of completion
- Wiki contains 10+ reusable patterns by month-end
- Zero stale docs (all references match current state)

## Non-Functional Requirements

- All plaintext Markdown (no proprietary formats)
- Git-friendly folder structure
- API key management via `.claude/claude.json` (not committed)
- Local-only access (port 27123, localhost)
