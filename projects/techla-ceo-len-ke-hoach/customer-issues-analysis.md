---
tags: [analysis, customer-feedback, clawx]
status: active
created: 2026-04-11
---

# Phân Tích Lỗi & Phản Hồi Khách Hàng - ClawX

## Tổng quan
- **50 khách hàng** đang sử dụng ClawX Box (miniPC)
- Nguồn: file "Công việc Openclaw.xlsx"
- 2 sheet chính: mã lỗi (13 lỗi) + thắc mắc bán hàng (11 câu hỏi)

---

## Phân loại lỗi theo nhóm

### 1. Kết nối & Gateway (Khẩn cấp)
| # | Lỗi | Mức độ | Giải pháp hiện tại |
|---|------|--------|-------------------|
| 1 | Cổng Gateway không connected | - | F5 reload |
| 8 | Web sập sau update ClawX | Khẩn cấp | SSH vào fix thủ công, git pull + setup.sh |

**Vấn đề gốc:** Update firmware/software gây xung đột, không có rollback mechanism.

### 2. Tích hợp Messaging (Telegram, Zalo)
| # | Lỗi | Giải pháp hiện tại |
|---|------|-------------------|
| 3 | Không kết nối Tele & Zalo | Hướng dẫn thủ công từng bước |
| 9 | Telegram chặn bot cài ứng dụng | Mở quyền elevated thủ công |
| 10 | Cài voice cho bot Telegram | Cài ffmpeg + whisper thủ công |
| 12 | Lỗi Zalo: Unexpected end of JSON | Reset profile openzca |
| 13 | Zalo connected nhưng không trả lời | Restart gateway + config plugin |

**Vấn đề gốc:** Setup messaging quá phức tạp, nhiều bước thủ công, hay lỗi.

### 3. API & AI Models
| # | Lỗi | Giải pháp hiện tại |
|---|------|-------------------|
| 2 | API rate limited | Đổi API key thủ công |
| 4 | Chưa đăng nhập TK CodeX | Add mail thủ công qua farm |

**Vấn đề gốc:** Không có API key management tự động, khách phải tự xử lý.

### 4. UX & Tính năng
| # | Lỗi | Giải pháp hiện tại |
|---|------|-------------------|
| 5 | Lỗi add mail, calendar | Chưa có |
| 6 | Khách muốn vào terminal | Hướng dẫn cài web |
| 7 | Bot đã học nhưng chưa có skill trong chợ | Chưa có |
| 11 | Mở rộng bộ nhớ 27→54GB | Chạy lệnh thủ công |

---

## Thắc mắc bán hàng thường gặp

| Câu hỏi | Tần suất | Có trả lời? |
|---------|----------|-------------|
| Nâng cấp dung lượng được không? | Cao | Không, phải mua thiết bị khác |
| Bảo hành bao lâu? | Cao | 1 năm |
| Khác VPS ở điểm nào? | Cao | Chưa có câu trả lời |
| 1 box cài được mấy skill? | Trung bình | Tối ưu 2 skill |
| Chi phí hàng tháng? | Cao | Box + API 200k/tháng + skill |
| Hướng dẫn sử dụng? | Cao | Google Docs link |

---

## Top 5 vấn đề cần giải quyết (theo mức độ ảnh hưởng)

1. **Web sập sau update** → Ảnh hưởng 100% khách, mất dịch vụ hoàn toàn
2. **Setup messaging phức tạp** → 5/13 lỗi liên quan Tele/Zalo, tốn support time
3. **API key hết/lỗi** → Khách không dùng được AI, giá trị cốt lõi bị mất
4. **Thiếu tài liệu rõ ràng** → Nhiều câu hỏi bán hàng chưa có câu trả lời
5. **Không mở rộng phần cứng** → Khách phải mua mới, churn risk
