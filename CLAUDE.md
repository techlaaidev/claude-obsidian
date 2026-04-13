# CLAUDE.md — TechLa Knowledge Vault

**Vault purpose:** Persistent memory cho Claude Code. Tự động ghi nhận project context, decisions, session history xuyên suốt các dự án.

---

## MANDATORY Auto-Capture Rules

Claude **PHẢI** làm những điều sau **KHÔNG CẦN** user nhắc:

### 1. Session Start (mỗi khi bắt đầu conversation mới)

- Xác định project name từ CWD (folder name)
- Đọc `projects/<project-name>/_index.md` nếu tồn tại
- Nếu chưa tồn tại → **tự tạo ngay** dùng cấu trúc trong `templates/project-index.md`
- Đọc 3 session gần nhất trong `memory/sessions/` để lấy context
- Đọc `memory/decisions-log.md` để tránh re-debate quyết định cũ

### 2. Trong khi làm việc (continuous)

- **Decision kỹ thuật** (chọn library, pattern, architecture, trade-off) → **append ngay** vào `memory/decisions-log.md` với format:
  ```markdown
  ## YYYY-MM-DD — <Title>
  - **Project:** <project-name>
  - **Context:** <1 line>
  - **Choice:** <choice>
  - **Reason:** <reason>
  - **Reversible:** Yes/No
  ```
- **Pattern tái sử dụng** (code pattern, gotcha, best practice) → tạo file trong `wiki/<category>/<slug>.md`
- **Project info update** (tech stack, roadmap change, key file) → update `projects/<project-name>/_index.md`

### 3. Session End (khi user nói "xong", "done", "cảm ơn", hoặc kết thúc task lớn)

- **Tự động viết** `memory/sessions/YYYY-MM-DD-<slug>.md` gồm:
  - Tasks completed
  - Decisions made (link tới decisions-log entries)
  - Blockers gặp phải
  - Next session suggestions
- **Không cần** hỏi user, cứ viết.

---

## Vault Structure

| Folder | Auto-managed | Mục đích |
|--------|--------------|----------|
| `projects/<name>/` | ✅ Claude tự tạo/update | Per-project context + decisions |
| `memory/decisions-log.md` | ✅ Claude tự append | Master decision history |
| `memory/sessions/` | ✅ Claude tự tạo | Daily session logs |
| `wiki/` | ✅ Claude tự tạo khi gặp pattern mới | Reusable knowledge |
| `docs/` | ❌ User own | Standards, guides |
| `templates/` | ❌ Static | Templates reference |
| `plans/` | ✅ Claude tạo via /ck:cook | Implementation plans |

---

## Behavioral Rules

1. **Never ask permission** trước khi auto-capture — cứ làm
2. **Never duplicate** decisions — check log trước khi append
3. **Never write secrets** vào vault (API keys, passwords, tokens)
4. **Always use `[[wiki links]]`** khi reference nội dung khác trong vault
5. **Always include frontmatter** khi tạo file mới: `tags`, `status`, `created`, `project`
6. **Project detection:** CWD folder name = project name. Ví dụ CWD `D:/projects/techla-web` → project `techla-web`

---

## User Workflow (dead simple)

```
1. Mở Obsidian (để MCP chạy)
2. cd vào project folder bất kỳ
3. Chạy `claude`
4. Code bình thường
→ Vault tự động update trong suốt quá trình
```

Không cần update Active Context thủ công. Không cần chạy template. Không cần ghi decision thủ công.

---

## DO NOT

- Hỏi user "có muốn tôi log không?" — cứ log
- Tạo file ngoài structure trên
- Ghi credentials/secrets
- Xóa decisions cũ (archive bằng `status: deprecated` nếu cần)
