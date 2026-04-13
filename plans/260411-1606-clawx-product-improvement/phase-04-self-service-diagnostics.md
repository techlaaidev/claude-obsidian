---
tags: [phase, clawx, diagnostics, self-service]
status: todo
priority: P1
effort: 3-4 ngày
created: 2026-04-11
---

# Phase 4: Self-Service Diagnostics

## Vấn đề
- Lỗi #5: Add mail/calendar lỗi → chưa có giải pháp
- Lỗi #6: Khách muốn vào terminal → phải hướng dẫn riêng
- Lỗi #11: Mở rộng bộ nhớ → chạy lệnh thủ công
- Khách phải liên hệ support cho mọi vấn đề kỹ thuật

## Mục tiêu
Khách tự chẩn đoán và fix vấn đề phổ biến qua UI, giảm tải support.

## Giải pháp

### 1. System Health Dashboard
- Hiển thị trạng thái tổng quan: CPU, RAM, Disk, Network
- Cảnh báo disk sắp đầy
- 1-click mở rộng bộ nhớ (nếu có free space trong LVM)

### 2. Diagnostic Toolkit trong UI
- "Run Diagnostics" button → check tất cả services
- Kết quả: danh sách services + trạng thái (OK/Warning/Error)
- Mỗi error → suggest fix hoặc 1-click fix

### 3. Web Terminal (có kiểm soát)
- Terminal trong browser cho advanced users
- Giới hạn commands cho an toàn (whitelist)
- Hoặc dùng existing terminal page (`/terminal`)

## Related Code Files
- `src/pages/Dashboard/` — dashboard hiện tại
- `server/services/system-monitor.ts` — đã có monitoring
- `server/routes/system.ts` — system APIs

## Implementation Steps
1. Mở rộng `system-monitor.ts` → thêm disk/LVM check, service health
2. Tạo `server/services/diagnostic-runner.ts` — chạy health checks
3. UI: System Health widget trên Dashboard
4. UI: Diagnostic Results page với fix suggestions
5. 1-click LVM extend (nếu free space available)

## Todo
- [ ] Mở rộng system-monitor với disk/LVM info
- [ ] Diagnostic runner service
- [ ] System Health UI widget
- [ ] 1-click fixes cho common issues
- [ ] Web terminal access (controlled)

## Success Criteria
- Khách thấy disk usage và cảnh báo sớm
- 50% vấn đề kỹ thuật tự fix qua diagnostic tool
- Giảm 50% ticket "bộ nhớ đầy"

## Risk
- 1-click LVM extend chỉ hoạt động nếu có unallocated space
- Web terminal: security risk nếu không whitelist commands đúng
