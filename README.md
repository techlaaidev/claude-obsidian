# TechLa Knowledge Vault

Obsidian vault làm knowledge base cho Claude Code — lưu decisions, docs, wiki, session memory xuyên suốt các dự án.

## Setup

### 1. Cài Obsidian + Plugins

1. Tải [Obsidian](https://obsidian.md/download) → Open folder as vault → chọn thư mục này
2. Settings → Community Plugins → Turn on → Browse, cài:
   - **Local REST API** (bắt buộc cho MCP)
   - **Dataview** (query frontmatter)
   - **Templater** (template nâng cao)
3. Enable từng plugin sau khi cài

### 2. Cấu hình Local REST API

1. Settings → Local REST API
2. Copy **API Key**
3. Lưu ý **port** (mặc định `27124` cho HTTPS)

### 3. Cấu hình MCP cho Claude Code

File `.mcp.json` đã có sẵn ở root. Paste API key vào:

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "obsidian-mcp-server"],
      "env": {
        "OBSIDIAN_API_KEY": "<paste-key-o-day>",
        "OBSIDIAN_API_URL": "https://127.0.0.1:27124"
      }
    }
  }
}
```

Restart Claude Code → gõ `/mcp` → thấy `obsidian` là OK.

## Cấu trúc

```
├── CLAUDE.md         # System prompt — Claude đọc đầu mỗi session
├── docs/             # Standards, architecture, guides
├── projects/         # Docs per-project (mỗi dự án 1 folder)
├── wiki/             # Knowledge tái sử dụng (concepts, tech, patterns)
├── memory/           # Session logs, decisions-log.md
├── templates/        # 4 templates (session, decision, project, wiki)
├── raw/              # Research notes
└── plans/            # Implementation plans
```

## Cách dùng

### Trước mỗi session Claude Code

1. Mở Obsidian (để Local REST API chạy)
2. Update `CLAUDE.md` → section **Active Context** (project đang làm, focus, blocker)
3. Chạy `claude` trong terminal → Claude tự đọc CLAUDE.md + truy cập vault qua MCP

### Ghi nhận decisions

- Decision quan trọng → append vào `memory/decisions-log.md` (dùng [[templates/decision-log-entry]])
- Pattern tái sử dụng → tạo note trong `wiki/` (dùng [[templates/wiki-article]])
- Project mới → tạo folder `projects/<name>/` + `_index.md` (dùng [[templates/project-index]])

### Quy tắc

- File atomic — 1 concept/file, không viết monolith
- Dùng `[[wiki links]]` thay vì folder hierarchy sâu
- Frontmatter bắt buộc: `tags`, `status`, `created`
- **Không** commit secrets — `.mcp.json` đã trong `.gitignore`

## Troubleshooting

| Lỗi | Fix |
|-----|-----|
| `/mcp` không thấy obsidian | Restart Claude Code sau khi sửa `.mcp.json` |
| Connection refused | Obsidian chưa mở hoặc plugin Local REST API chưa enable |
| 401 Unauthorized | API key trong `.mcp.json` sai — copy lại từ plugin settings |
| Port khác 27124 | Sửa `OBSIDIAN_API_URL` trong `.mcp.json` cho khớp |

Chi tiết MCP: xem `docs/mcp-setup-guide.md`.
