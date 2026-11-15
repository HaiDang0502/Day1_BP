# docs/00 — Scope A (KHUNG TÁI DỰNG — chờ bản gốc đã duyệt 12/11/2026)

> 🔴 File này tái dựng từ Brief + PRD 1.1. Khi có bản Scope A gốc, thay thế toàn bộ và xóa ghi chú này.

## Phạm vi ĐƯỢC duyệt (Scope A)
- Trợ lý tóm tắt hồ sơ trước lượt tái khám cho bệnh nhân **ĐTĐ típ 2 ngoại trú** (có thể kèm THA, rối loạn lipid, CKD giai đoạn 1–3).
- Nguồn dữ liệu: **FHIR R4 đa nguồn, 100% mô phỏng** (ADR-12: sinh 3 lớp theo seed).
- Đầu ra: bản tóm tắt tiếng Việt 8 mục, mọi câu có nguồn bấm được; cờ an toàn từ 17 quy tắc xác định; bác sĩ sửa và duyệt; xuất FHIR Composition (P1).
- Người dùng: bác sĩ điều trị, quản trị IT. Bệnh nhân là chủ thể dữ liệu, không phải người dùng.

## Ngoài phạm vi trong 31 ngày (chốt, không mở lại trước Demo Day 13/12)
- Chẩn đoán, kê đơn, đề xuất thuốc, triage.
- Đầu ra cho bệnh nhân; dữ liệu thật; tích hợp HIS thật; tải hồ sơ lên.
- Hỏi đáp tự do; ghi nhớ theo bệnh nhân.
- Típ 1, thai kỳ, nhi khoa, nội trú, cấp cứu.
- Hình ảnh, ECG, thiết bị đeo, OCR.

## Ràng buộc đã duyệt
- Kiến trúc "xác định trước, LLM sau"; LLM chỉ viết lời ở 1/10 bước.
- p95 ≤ 60 giây/bản; URL công khai; 30 bệnh nhân mô phỏng (mục tiêu 50).

## Chữ ký duyệt
| Vai trò | Tên | Ngày |
|---|---|---|
| Người điều hành dự án | [bản gốc] | 12/11/2026 |
| Mentor AI for Healthcare | [bản gốc] | 12/11/2026 |
