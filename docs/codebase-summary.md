---
title: Vault Structure & File Reference
tags: [reference, structure, navigation]
status: active
created: 2026-04-11
---

# Vault Structure & File Reference

Obsidian vault organized into distinct knowledge domains. This is a documentation/knowledge vault (no source code).

## Root Level

| File/Folder | Purpose |
|-------------|---------|
| `CLAUDE.md` | System prompt and session rules for Claude Code |
| `README.md` | Vault overview and quick-start guide |
| `.obsidian/` | Obsidian configuration (workspace, plugins, settings) |

## `/docs` — Standards & Guides

| File | Purpose |
|------|---------|
| `project-overview-pdr.md` | This vault's PDR and feature roadmap |
| `codebase-summary.md` | This file — vault structure reference |
| `mcp-setup-guide.md` | MCP server installation and configuration |

## `/wiki` — Reusable Knowledge Base

Technical patterns, architecture concepts, and reference material. Organized by domain (e.g., `api-design/`, `testing-strategies/`, `security-patterns/`).

## `/memory` — Session & Decision History

| File | Purpose |
|------|---------|
| `decisions-log.md` | Architecture decisions, rationales, alternatives |

Session logs follow `session-log.md` template.

## `/projects` — Per-Project Documentation

One folder per active TechLa project. Contains project-specific decisions, meeting notes, and plan references.

## `/templates` — Note Templates

| File | Purpose |
|------|---------|
| `decision-log-entry.md` | ADR template for logging decisions |
| `session-log.md` | Session summary template |
| `project-index.md` | Per-project index template |
| `wiki-article.md` | Wiki knowledge article template |

## `/plans` — Implementation Plans & Reports

Organized by date and initiative (e.g., `260411-1435-obsidian-vault-setup/`). Contains phase breakdowns, research, and completion reports.

## `/raw` — Research & Scratch Notes

Unstructured notes, research spikes, and brainstorms. Cleaned up periodically.

---

**Last Updated:** 2026-04-11  
**Maintainer:** Docs Manager Agent
