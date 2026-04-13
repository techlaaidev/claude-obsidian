---
tags: [phase, clawx, update, rollback]
status: todo
priority: P0
effort: 3-5 ngày
created: 2026-04-11
---

# Phase 1: Safe Update & Rollback

## Vấn đề
- Lỗi #1: Gateway không connected sau đăng nhập
- Lỗi #8: Web sập hoàn toàn sau update ClawX (khẩn cấp)
- Hiện tại phải SSH vào box, chạy `git pull && bash setup.sh` thủ công
- Ảnh hưởng 100% khách khi xảy ra

## Mục tiêu
Update ClawX an toàn, tự rollback nếu lỗi — khách không cần biết SSH.

## Giải pháp

### 1. Pre-update Snapshot
- Trước khi update, backup state hiện tại (config, version, db)
- Lưu vào `/opt/clawx-web/.backup/`

### 2. Health Check sau Update
- Sau update, tự động kiểm tra:
  - Web accessible (HTTP 200)?
  - Gateway connected?
  - OpenClaw process running?
- Nếu fail → tự rollback về snapshot

### 3. Update UI trong Dashboard
- Thêm nút "Check for Updates" trong Settings page
- Hiển thị changelog trước khi update
- Progress bar + status log real-time

### Related Code Files
- `server/services/update-checker.ts` — đã có, cần mở rộng
- `server/routes/system.ts` — thêm API endpoints
- `src/pages/Settings/` — thêm Update UI
- `install.sh` / `setup.sh` — refactor thành safe update flow

## Implementation Steps
1. Tạo `server/services/safe-updater.ts` — backup, update, health-check, rollback logic
2. Thêm API: `POST /api/system/update`, `GET /api/system/update-status`
3. Thêm UI component "System Update" trong Settings page
4. Tạo systemd timer cho auto health-check sau mỗi update
5. Test: simulate failed update → verify rollback

## Todo
- [ ] Tạo safe-updater service với backup/rollback
- [ ] API endpoints cho update flow
- [ ] UI trong Settings
- [ ] Health-check script (systemd)
- [ ] Test rollback scenario

## Success Criteria
- Update qua UI, không cần SSH
- Rollback tự động trong < 30 giây nếu health check fail
- Khách thấy trạng thái update real-time

## Risk
- Rollback có thể không cover DB migrations → cần migration versioning
- Disk space trên miniPC hạn chế → giới hạn số backup (giữ 2 bản gần nhất)
