# CLAUDE.md - TechLa Knowledge Vault

## Purpose

Knowledge base + development memory for TechLa projects. This vault serves as persistent context for Claude Code sessions — storing decisions, patterns, project docs, and reusable knowledge.

## Folder Guide

| Folder | Purpose |
|--------|---------|
| `docs/` | Standards, architecture, guides (synced with project docs) |
| `projects/` | Per-project documentation, design decisions, references |
| `wiki/` | Reusable knowledge base (concepts, technologies, patterns) |
| `memory/` | Session logs, decision history, blockers |
| `templates/` | Obsidian templates for new notes |
| `raw/` | Unprocessed research notes, dumps |
| `plans/` | Implementation plans and reports |

## Rules for Claude Code

1. Read `projects/<project>/_index.md` at session start for active context
2. Check `memory/decisions-log.md` before re-debating resolved decisions
3. Link to `wiki/` articles when documenting patterns — don't duplicate
4. Log significant decisions in `memory/decisions-log.md`
5. Keep files atomic: one concept per file
6. Use `[[wiki links]]` for cross-referencing

## Conventions

- **Links:** `[[folder/filename]]` (Obsidian wikilinks)
- **Code blocks:** Always include language identifier
- **Frontmatter:** Use `tags`, `status`, `created`, `updated` fields
- **Status values:** `draft`, `active`, `deprecated`, `archived`
- **File naming:** kebab-case with descriptive names

## DO NOT

- Store API keys, credentials, or secrets in vault
- Delete old decisions — archive or mark `status: deprecated`
- Create files without frontmatter tags
- Leave CLAUDE.md stale — refresh Active Context each session

## Active Context (Update Before Each Session)

- **Current Focus:** Vault setup and configuration
- **Last Updated:** 2026-04-11
