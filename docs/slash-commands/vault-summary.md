---
description: Write session summary to vault memory/sessions/ — run at end of work session
---

# Vault Summary

Summarize current session and write to TechLa Obsidian Vault.

## Instructions

1. **Detect project name:** basename of CWD
2. **Skip if CWD is vault root** → fail with "Use this command in a real project, not vault root"
3. **Generate session summary** from this conversation:
   - **Tasks completed:** what was actually done (file creates/edits, tests run, bugs fixed)
   - **Decisions made:** any technical choices (library pick, architecture, trade-offs)
   - **Blockers:** anything unresolved
   - **Next session:** suggestions for next time
4. **Via MCP `obsidian`:**
   - Write to `memory/sessions/<YYYY-MM-DD>-<project>-<slug>.md` where `<slug>` = 2-3 words describing main focus of session (kebab-case)
   - Content format:
     ```markdown
     ---
     tags: [session-log]
     status: active
     created: <YYYY-MM-DD>
     project: <project-name>
     ---

     # Session <YYYY-MM-DD> — <project>

     ## Context
     - **Project:** <name>
     - **CWD:** <absolute path>
     - **Duration:** <rough estimate if known>

     ## Tasks Completed
     - <bullet list, concrete items>

     ## Decisions Made
     <list decisions with rationale; empty section OK if none>

     ## Blockers
     <list, empty OK>

     ## Next Session
     <suggestions>
     ```
5. **Also append significant decisions** to `memory/decisions-log.md` (only decisions worth tracking globally, not minor ones)
6. **Update `projects/<name>/_index.md`:**
   - Update `Last Active:` date
   - Update `Active Focus:` if it changed
7. **Confirm:** `✓ Session logged: memory/sessions/<filename>.md`

## Rules

- Be **concise**. Tasks = what actually happened, not planned. Decisions = real trade-offs, not trivia.
- Skip empty sections — no "Blockers: None" filler
- If session was just Q&A with no real work → skip creating session file, just update `Last Active:`
- Use MCP `obsidian` — vault is outside CWD
- Fail loud if MCP unavailable
