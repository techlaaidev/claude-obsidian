---
description: Init current project in TechLa Obsidian Vault — create projects/<name>/_index.md if missing
---

# Vault Init

Initialize current project context in the TechLa Obsidian Vault.

## Instructions

1. **Detect project name:** `pwd` → basename of CWD = project name
2. **Skip if CWD is vault root** (`D:/o_E/Desktop/techla-project/claude-obsidian`) → tell user to use this command in actual project, not vault root
3. **Via MCP `obsidian`:**
   - Check if `projects/<project-name>/_index.md` exists (`obsidian_get_file_contents` or `obsidian_list_notes`)
   - **If exists:** read it, show summary to user, ask if they want to update active focus
   - **If not exists:** create with this frontmatter + skeleton:
     ```markdown
     ---
     tags: [project, moc]
     status: active
     created: <YYYY-MM-DD today>
     ---

     # <project-name>

     ## Overview
     <1-2 sentences — ask user or detect from README.md/package.json in CWD>

     ## Status
     - **Phase:** <ask user>
     - **Last Active:** <today>

     ## Tech Stack
     <detect from package.json / pyproject.toml / go.mod / Cargo.toml in CWD>

     ## Key Files
     <list top-level dirs/files in CWD>

     ## Active Focus
     <ask user what they're working on this session>
     ```
4. **Append decision log entry** to `memory/decisions-log.md`:
   ```markdown
   ## <YYYY-MM-DD> — Project `<name>` initialized
   - **Context:** Started working on <name> in CWD `<absolute path>`
   - **Status:** Active
   ```
5. **Confirm to user:** `✓ Project <name> ready in vault. Active focus: <what they said>`

## Rules

- Use MCP `obsidian` tool calls, not direct file Write (vault is outside CWD)
- Be concise — don't over-engineer the _index.md, keep it minimal
- If MCP unavailable → fail loud: "MCP obsidian not connected. Run `claude mcp list` to check."
