---
tags: [phase, clawx, api, key-management]
status: todo
priority: P1
effort: 2-3 ngày
created: 2026-04-11
---

# Phase 3: API Key Management

## Vấn đề
- Lỗi #2: API rate limited → khách không chat được bot
- Lỗi #4: Chưa đăng nhập TK CodeX → quy trình add mail phức tạp
- Khách phải tự đổi API key trong Settings, không biết key nào còn dùng được

## Mục tiêu
Quản lý API key thông minh — cảnh báo sớm, rotate tự động, hiển thị usage.

## Giải pháp

### 1. API Usage Dashboard
- Hiển thị usage/quota realtime cho mỗi API key
- Cảnh báo khi usage > 80% quota
- Hiển thị lịch sử sử dụng (chart)

### 2. Auto Key Rotation
- Khi key bị rate limited → tự switch sang key backup
- Notify khách qua UI + Telegram
- Suggest mua thêm API nếu cần

### 3. CodeX Account Setup tự động
- Tự add mail khách vào workspace
- Status check: đã join chưa, invite pending, etc.

## Related Code Files
- `src/pages/Settings/` — AI Models settings UI
- `server/routes/providers.ts` — API provider management
- `server/routes/settings.ts` — settings API

## Implementation Steps
1. Thêm API usage tracking middleware trong server
2. Tạo `server/services/api-key-manager.ts` — quota check, rotation logic
3. UI: API Usage widget trong Dashboard hoặc Settings
4. Alert system: push notification khi key sắp hết quota
5. CodeX auto-invite flow

## Todo
- [ ] API usage tracking
- [ ] Usage dashboard UI (chart)
- [ ] Auto key rotation khi rate limited
- [ ] Alert notification (UI + Telegram)
- [ ] CodeX auto-invite

## Success Criteria
- Khách thấy rõ API usage, không bị bất ngờ khi hết quota
- Auto-rotate giảm 90% lỗi "API rate limited"
- CodeX setup < 1 phút

## Risk
- Mỗi provider (OpenAI, Anthropic, etc.) có API khác nhau để check quota
- Cần lưu multiple keys → bảo mật key storage
