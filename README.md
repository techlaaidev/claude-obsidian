# TechLa Knowledge Vault

Obsidian vault làm knowledge base cho Claude Code — lưu decisions, docs, wiki, session memory xuyên suốt các dự án.

## Sơ đồ hoạt động

```mermaid
flowchart TB
    User([User])
    CC[Claude Code CLI]
    MCP[MCP Server<br/>obsidian-mcp-server]
    REST[Obsidian<br/>Local REST API :27124]
    Vault[(Obsidian Vault)]
    CLAUDE[CLAUDE.md<br/>Auto-capture rules]

    subgraph VaultContent[Vault Content]
        direction LR
        Projects[projects/<br/>per-project context]
        Memory[memory/<br/>decisions + sessions]
        Wiki[wiki/<br/>reusable knowledge]
        Docs[docs/<br/>standards]
    end

    User -->|cd project<br/>claude| CC
    CC -->|load rules| CLAUDE
    CC <-->|stdio| MCP
    MCP <-->|HTTPS| REST
    REST <-->|read/write| Vault
    Vault --- VaultContent

    CC -.->|1. Read project index<br/>+ recent sessions<br/>+ decisions log| Vault
    CC -.->|2. Auto-append decisions<br/>+ patterns during work| Vault
    CC -.->|3. Auto-write session<br/>summary on end| Vault

    style CC fill:#e1f5ff
    style Vault fill:#fff4e1
    style CLAUDE fill:#ffe1e1
    style VaultContent fill:#f0f0f0
```

**Luồng xử lý:**

1. **Session start** — User `cd` vào project → chạy `claude` → Claude Code load `CLAUDE.md` → qua MCP đọc `projects/<name>/_index.md` + 3 sessions gần nhất + `decisions-log.md`
2. **Working** — User code/chat bình thường → Claude tự append decisions vào `memory/decisions-log.md`, tự tạo wiki articles khi gặp pattern mới
3. **Session end** — Claude tự viết `memory/sessions/YYYY-MM-DD-<slug>.md` gồm tasks completed, decisions, blockers

## Prerequisites

- **Claude Code CLI** — [install](https://docs.claude.com/claude-code/setup)
- **Obsidian** — [download](https://obsidian.md/download)
- **Node.js 18+** — cần cho `npx` chạy `obsidian-mcp-server`

## Setup

### Quick setup (recommended)

```bash
git clone https://github.com/techlaaidev/claude-obsidian.git
cd claude-obsidian
claude
```

Trong Claude Code, gõ: **"Setup vault theo SETUP.md"**

Claude sẽ tự động:
- Detect vault path
- Register MCP `obsidian` ở **user scope** (work từ mọi CWD)
- Update `~/.claude/CLAUDE.md` với auto-capture rules
- Verify MCP connection

Và sẽ hỏi bạn 3 bước manual: cài Obsidian, cài plugins, copy API key.

Chi tiết xem [`SETUP.md`](./SETUP.md).

---

### Manual setup

Nếu bạn muốn làm tay từng bước:

### 1. Clone repo

```bash
git clone https://github.com/techlaaidev/claude-obsidian.git
cd claude-obsidian
```

### 2. Cài Obsidian + Plugins

1. Tải [Obsidian](https://obsidian.md/download) → Open folder as vault → chọn thư mục vừa clone
2. Settings → Community Plugins → Turn on → Browse, cài:
   - **Local REST API** (bắt buộc cho MCP)
   - **Dataview** (query frontmatter)
   - **Templater** (template nâng cao)
3. Enable từng plugin sau khi cài

### 3. Cấu hình Local REST API

1. Settings → Local REST API
2. Copy **API Key**
3. Lưu ý **port** (mặc định `27124` cho HTTPS)

### 4. Install slash commands

Copy 2 slash commands từ repo vào `~/.claude/commands/`:

```bash
# Windows (PowerShell)
Copy-Item docs/slash-commands/vault-init.md $HOME/.claude/commands/
Copy-Item docs/slash-commands/vault-summary.md $HOME/.claude/commands/

# macOS/Linux
cp docs/slash-commands/vault-init.md ~/.claude/commands/
cp docs/slash-commands/vault-summary.md ~/.claude/commands/
```

Verify: trong Claude Code gõ `/` → phải thấy `vault-init` và `vault-summary` trong dropdown.

### 5. Register MCP cho Claude Code (user scope)

Đăng ký ở **user scope** để MCP hoạt động từ mọi CWD:

```bash
claude mcp add-json --scope user obsidian '{"command":"npx","args":["-y","obsidian-mcp-server"],"env":{"OBSIDIAN_API_KEY":"<paste-key-o-day>","OBSIDIAN_BASE_URL":"https://127.0.0.1:27124","OBSIDIAN_VERIFY_SSL":"false"}}'
```

**Lưu ý env vars:**
- `OBSIDIAN_BASE_URL` (không phải `OBSIDIAN_API_URL`)
- `OBSIDIAN_VERIFY_SSL=false` — tắt verify SSL do plugin dùng self-signed cert

Verify: `claude mcp list` → phải thấy `obsidian: ... ✓ Connected`

Restart Claude Code session để MCP tools load vào context.

## Cấu trúc

```
├── CLAUDE.md         # System prompt — vault rules (scoped to vault root)
├── README.md         # File này
├── SETUP.md          # Claude đọc để tự setup
├── docs/             # Standards, architecture, guides
├── projects/         # Docs per-project (mỗi dự án 1 folder)
├── wiki/             # Knowledge tái sử dụng (concepts, tech, patterns)
├── memory/           # Session logs, decisions-log.md
├── templates/        # 4 templates (session, decision, project, wiki)
├── raw/              # Research notes
└── plans/            # Implementation plans
```

**MCP config** nằm ở `~/.claude.json` (user home, không commit vào repo) — đã register qua `claude mcp add-json --scope user`.

## Cách dùng

### Workflow thực tế

```bash
# 1. Mở Obsidian (MCP Local REST API phải chạy)
# 2. cd vào project của bạn
cd D:/projects/my-app

# 3. Chạy claude
claude
```

**Đầu session — gõ:**
```
/vault-init
```
→ Claude tạo `projects/my-app/_index.md` trong vault (nếu chưa có), hỏi bạn về tech stack + active focus.

**Trong lúc làm việc:**
- Code bình thường
- Muốn log decision ngay → bảo Claude *"log decision: chọn X vì Y"*

**Cuối session — gõ:**
```
/vault-summary
```
→ Claude viết `memory/sessions/YYYY-MM-DD-<slug>.md` với tasks, decisions, blockers, next steps. Update `projects/my-app/_index.md` với last active date.

### Vì sao cần slash commands?

Claude Code không tự động đọc/ghi vault khi bạn `cd` vào project. **Rules trong CLAUDE.md không đủ để force Claude proactive** — nó chỉ làm khi có instruction rõ ràng.

Slash commands = **explicit trigger** để vault capture chạy đúng lúc bạn muốn.

### Alternative: in-vault projects

Nếu project **nhỏ/prototype**, bạn có thể tạo trực tiếp trong `claude-obsidian/projects/my-app/`:
- Claude dùng `Read`/`Write` trực tiếp (không cần MCP)
- Vault CLAUDE.md + global rules cùng load
- Trade-off: vault phình to, nested git nếu cần version control

### Quy tắc

- File atomic — 1 concept/file
- Dùng `[[wiki links]]` thay vì folder hierarchy sâu
- Frontmatter bắt buộc: `tags`, `status`, `created`
- **Không** commit secrets — API keys nằm trong `~/.claude.json` (user scope, ngoài repo)
- **Không** xóa decisions cũ — mark `status: deprecated` nếu cần

## Troubleshooting

| Lỗi | Fix |
|-----|-----|
| `claude mcp list` báo `✗ Failed to connect` | Check env vars: phải là `OBSIDIAN_BASE_URL` (không `OBSIDIAN_API_URL`), thêm `OBSIDIAN_VERIFY_SSL=false` |
| `curl` trả về 000 | Obsidian chưa mở hoặc plugin Local REST API chưa enable |
| `curl` trả về 401 | API key sai — copy lại từ plugin settings |
| Port khác 27124 | Sửa `OBSIDIAN_BASE_URL` port cho khớp |
| MCP connect OK nhưng Claude không thấy tools | Restart Claude Code session |

**Re-register MCP sau khi fix:**
```bash
claude mcp remove --scope user obsidian
claude mcp add-json --scope user obsidian '<new-json>'
```

Chi tiết MCP: xem `docs/mcp-setup-guide.md`.

## Contributing & Feedback

Repo public, mọi người có thể:

- **Báo lỗi / đề xuất feature:** tạo issue tại [Issues](https://github.com/techlaaidev/claude-obsidian/issues)
- **Đóng góp code/docs:** fork repo → tạo branch → submit Pull Request
- **Hỏi đáp:** mở issue với label `question`

Khi báo lỗi, include:
- OS + Obsidian version + Claude Code version
- Bước reproduce
- Log lỗi (nếu có)

## License

MIT (hoặc chỉ định license phù hợp — cập nhật sau).
