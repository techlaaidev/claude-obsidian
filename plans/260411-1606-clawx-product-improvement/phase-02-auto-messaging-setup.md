---
tags: [phase, clawx, messaging, telegram, zalo]
status: todo
priority: P0
effort: 5-7 ngày
created: 2026-04-11
---

# Phase 2: Auto Messaging Setup (Telegram & Zalo)

## Vấn đề
- 5/13 lỗi (38%) liên quan Telegram & Zalo
- Lỗi #3: Không kết nối được → hướng dẫn thủ công 10+ bước
- Lỗi #9: Telegram chặn bot → mở quyền elevated thủ công
- Lỗi #10: Voice cho Telegram → cài ffmpeg + whisper thủ công
- Lỗi #12: Zalo JSON error → reset profile thủ công
- Lỗi #13: Zalo connected nhưng không trả lời → restart gateway + config plugin
- **Chiếm nhiều support time nhất**

## Mục tiêu
Setup Telegram/Zalo qua UI wizard, tự phát hiện và fix lỗi phổ biến.

## Giải pháp

### 1. Setup Wizard cho Channels
- Step-by-step UI wizard thay vì hướng dẫn text
- Telegram: Nhập bot token → auto verify → paired → done
- Zalo: Auto cài openzalo nếu chưa có → hiện QR → scan → done

### 2. Auto-Diagnose & Fix
- Detect lỗi Zalo JSON → tự reset profile + retry
- Detect Zalo connected nhưng không reply → tự restart gateway + enable plugin
- Detect Telegram permission → tự mở elevated quyền
- Hiển thị status rõ ràng: Connected / Error (+ mô tả) / Retrying

### 3. Voice Setup tự động
- Detect ffmpeg/whisper đã cài chưa
- Nếu chưa → 1-click install từ UI
- Status indicator cho voice capability

## Related Code Files
- `src/pages/Channels/` — UI hiện tại
- `server/routes/channels.ts`, `server/routes/pairing.ts` — API
- `server/services/auto-pairing.ts` — logic pairing hiện tại
- `server/services/gateway-manager.ts` — gateway control

## Implementation Steps
1. Tạo Channel Setup Wizard component (React)
2. Refactor `auto-pairing.ts` → thêm auto-diagnose logic
3. Thêm API: `POST /api/channels/diagnose`, `POST /api/channels/auto-fix`
4. Tạo `server/services/voice-installer.ts` cho ffmpeg/whisper
5. Thêm connection health monitor — ping Tele/Zalo mỗi 5 phút, auto-reconnect
6. Test: disconnect Zalo → verify auto-reconnect

## Todo
- [ ] Channel Setup Wizard UI
- [ ] Auto-diagnose service cho Tele/Zalo
- [ ] Auto-fix cho top 5 lỗi messaging
- [ ] Voice 1-click installer
- [ ] Connection health monitor
- [ ] Test auto-reconnect scenarios

## Success Criteria
- Khách setup Tele/Zalo trong < 2 phút qua UI
- 80% lỗi messaging tự fix không cần support
- Voice setup 1-click thay vì chạy commands

## Risk
- Zalo API thay đổi thường xuyên (openzalo dependency)
- Telegram rate limiting khi auto-reconnect quá nhiều
