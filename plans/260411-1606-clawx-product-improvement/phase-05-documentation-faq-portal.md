---
tags: [phase, clawx, documentation, faq]
status: todo
priority: P2
effort: 2-3 ngày
created: 2026-04-11
---

# Phase 5: Documentation & FAQ Portal

## Vấn đề
- Nhiều câu hỏi bán hàng chưa có câu trả lời chuẩn
- "Khác VPS ở điểm nào?" → chưa trả lời được
- Tài liệu nằm rải rác (Google Docs, Excel, docs website)
- Khách hỏi lặp lại cùng câu hỏi → tốn support time

## Mục tiêu
Tập trung tài liệu + FAQ, khách tự tìm câu trả lời trước khi liên hệ support.

## Giải pháp

### 1. In-App Help Center
- FAQ page trong ClawX Dashboard
- Searchable, categorized
- Embed từ docs website hoặc built-in

### 2. Chuẩn hóa FAQ bán hàng
- Soạn câu trả lời chuẩn cho tất cả thắc mắc
- Đặc biệt: "Khác VPS ở điểm nào?" — cần messaging rõ ràng
- Bảng so sánh ClawX vs VPS vs Cloud

### 3. Troubleshooting Guide tích hợp
- Khi gặp lỗi → hiển thị link đến hướng dẫn fix
- Error code → mapping đến giải pháp (từ sheet mã lỗi)

## Nội dung cần soạn

### FAQ Bán hàng (cần CEO review)
| Câu hỏi | Trạng thái |
|---------|------------|
| Dung lượng nâng cấp được không? | Cần soạn rõ hơn |
| Khác VPS ở điểm nào? | **Chưa có** — cần soạn |
| Chi phí hàng tháng? | Có, cần format lại |
| Bảo hành bao lâu? | Có |
| 1 box cài mấy skill? | Có |

### FAQ Kỹ thuật (từ mã lỗi)
- 13 lỗi đã có mã + cách fix → chuyển thành searchable docs

## Implementation Steps
1. Tạo `/help` page trong ClawX Dashboard
2. Import error codes → structured FAQ data
3. Soạn nội dung FAQ bán hàng (cần CEO input)
4. Tạo bảng so sánh ClawX vs VPS
5. Contextual help: link error codes → FAQ entries

## Todo
- [ ] Help Center page UI
- [ ] Import 13 mã lỗi vào FAQ
- [ ] Soạn FAQ bán hàng (CEO review)
- [ ] Bảng so sánh ClawX vs VPS
- [ ] Contextual error → FAQ linking

## Success Criteria
- 100% câu hỏi bán hàng có câu trả lời chuẩn
- Khách tìm được giải pháp trong < 1 phút
- Giảm 30% câu hỏi lặp lại từ khách

## Risk
- FAQ cần update thường xuyên khi có lỗi mới
- Nội dung bán hàng cần CEO approve
