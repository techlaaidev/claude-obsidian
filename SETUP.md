# SETUP.md — Hướng dẫn Claude tự động setup vault

> **Cách dùng:** Clone repo → `cd claude-obsidian` → chạy `claude` → gõ: *"Setup vault theo SETUP.md"*
>
> Claude sẽ đọc file này và thực hiện các bước tự động. Những bước manual sẽ được Claude hỏi bạn trực tiếp.

---

## Instructions for Claude

Khi user yêu cầu "setup theo SETUP.md", thực hiện TUẦN TỰ các bước sau. Tại mỗi **MANUAL STEP**, dừng lại hỏi user và chờ confirm trước khi tiếp tục.

### Step 1: Detect vault path (AUTO)

- Chạy `pwd` để lấy CWD tuyệt đối
- Lưu biến `VAULT_PATH` = CWD hiện tại
- Verify: file `CLAUDE.md` và folder `.obsidian/` phải tồn tại ở CWD. Nếu không → báo user họ đang đứng sai folder.

### Step 2: Install Obsidian (MANUAL STEP)

Báo user:
```
Bạn cần cài Obsidian nếu chưa có:
- Windows: winget install Obsidian.Obsidian
- Hoặc tải: https://obsidian.md/download

Sau khi cài xong, mở Obsidian → "Open folder as vault" → chọn: <VAULT_PATH>

Xong chưa?
```

Chờ user confirm.

### Step 3: Install required plugins (MANUAL STEP)

Báo user:
```
Trong Obsidian, vào Settings → Community Plugins → Turn on community plugins
Sau đó Browse và cài các plugin:

1. Local REST API (BẮT BUỘC)
2. Dataview (recommended)
3. Templater (recommended)

Enable cả 3 plugin sau khi cài.

Xong chưa?
```

Chờ user confirm.

### Step 4: Get API key (MANUAL STEP)

Báo user:
```
Vào Settings → Local REST API trong Obsidian
- Copy API Key
- Note lại port (mặc định: 27124 cho HTTPS)

Paste API key vào đây:
```

Chờ user paste. Lưu vào biến `API_KEY`.

Hỏi thêm:
```
Port có phải 27124 không? (Enter = yes, hoặc gõ số port khác)
```

Lưu vào biến `PORT` (default 27124).

### Step 5: Register MCP at user scope (AUTO)

**Quan trọng:** Đăng ký MCP ở **user scope** để hoạt động từ bất kỳ CWD nào (không phải project scope qua `.mcp.json`).

Chạy lệnh:
```bash
claude mcp add-json --scope user obsidian '{"command":"npx","args":["-y","obsidian-mcp-server"],"env":{"OBSIDIAN_API_KEY":"<API_KEY>","OBSIDIAN_BASE_URL":"https://127.0.0.1:<PORT>","OBSIDIAN_VERIFY_SSL":"false"}}'
```

**Env vars bắt buộc:**
- `OBSIDIAN_API_KEY` — API key từ plugin
- `OBSIDIAN_BASE_URL` — URL Obsidian REST API (chú ý: `BASE_URL`, không phải `API_URL`)
- `OBSIDIAN_VERIFY_SSL=false` — tắt verify SSL do Local REST API dùng self-signed cert

Verify:
```bash
claude mcp list
```
Expected: `obsidian: ... - ✓ Connected`

Nếu fail → báo user check lại Obsidian đang chạy + plugin enable.

### Step 6: Update global CLAUDE.md with vault auto-capture (AUTO)

- Path cross-platform:
  - **Windows:** `%USERPROFILE%/.claude/CLAUDE.md` (expand to `C:/Users/<user>/.claude/CLAUDE.md`)
  - **macOS/Linux:** `~/.claude/CLAUDE.md` (expand to `/home/<user>/.claude/CLAUDE.md`)
- Đọc file này, check xem đã có section `## TechLa Knowledge Vault — Auto-Capture` chưa
- Nếu **chưa có** → append nội dung sau (thay `<VAULT_PATH>` bằng path đã detect):

```markdown

---

## TechLa Knowledge Vault — Auto-Capture (GLOBAL)

**Vault path:** `<VAULT_PATH>`

Bất kể CWD nào, Claude tự động ghi nhận context, decisions, session history vào vault này qua MCP server `obsidian` (nếu available).

### Skip Rule

- **Nếu CWD là vault root** (`<VAULT_PATH>`) → SKIP auto-capture
- **Nếu MCP `obsidian` không available** → SKIP silently
- **Nếu user yêu cầu không log** → SKIP session đó

### Project Detection

- **Project name** = CWD folder basename
- Map tới `projects/<project-name>/` trong vault
- Nếu chưa có → tự tạo `_index.md`

### MANDATORY Auto-Capture Behavior

**Session Start:** Qua MCP đọc `projects/<project>/_index.md` + 3 session gần nhất + `decisions-log.md`

**During Work:** Auto-append decisions vào `memory/decisions-log.md`, tạo wiki articles cho patterns mới

**Session End:** Tự viết `memory/sessions/YYYY-MM-DD-<slug>.md`

### Rules

1. Never ask permission before auto-capture
2. Never duplicate decisions
3. Never write secrets
4. Silent failure nếu MCP chưa chạy
```

- Nếu **đã có** → update `Vault path:` value cho khớp với `<VAULT_PATH>` hiện tại

### Step 7: Verify MCP connection (AUTO)

- Test REST API trước: `curl -k -H "Authorization: Bearer <API_KEY>" https://127.0.0.1:<PORT>/`
  - 200 = OK
  - 000 (exit 7) = Obsidian chưa mở hoặc plugin chưa enable
  - 401 = API key sai
- Test MCP server: `claude mcp list`
  - Expected: `obsidian: ... - ✓ Connected`
  - Nếu `✗ Failed to connect` → env vars sai, check lại `OBSIDIAN_BASE_URL` (không phải `OBSIDIAN_API_URL`)

### Step 8: Install slash commands (AUTO)

Tạo 2 slash commands user-scope để trigger vault capture explicitly:

- `~/.claude/commands/vault-init.md` — init project trong vault (user gõ `/vault-init` đầu session)
- `~/.claude/commands/vault-summary.md` — viết session summary (user gõ `/vault-summary` cuối session)

Template cho `vault-init.md` và `vault-summary.md` xem trong repo `claude-obsidian/docs/slash-commands/` (Claude có thể copy từ đó qua MCP hoặc Write trực tiếp nếu CWD là vault).

### Step 9: Final summary

Báo user:
```
✓ Setup hoàn tất

Đã làm:
- Register MCP obsidian ở user scope (work từ mọi CWD)
- Update ~/.claude/CLAUDE.md với auto-capture rules
- Verify MCP connection: ✓ Connected

Bước cuối cùng (MANUAL): Restart Claude Code để MCP tools + rules global được load.

Sau khi restart, bạn có thể cd vào bất kỳ project nào và chạy `claude` —
vault sẽ tự động capture context/decisions/sessions.
```

---

## Unresolved edge cases

- Nếu MCP `obsidian-mcp-server` package chưa cài → `npx -y` sẽ auto-install lần đầu chạy (chậm ~30s, cần network)
- Nếu user đã register `obsidian` MCP trước đó → chạy `claude mcp remove --scope user obsidian` trước khi re-register
- Nếu user dùng port khác 27124 (custom trong plugin settings) → update `OBSIDIAN_BASE_URL`
- Nếu Claude Code CLI chưa cài → hướng dẫn cài trước khi chạy Step 5
