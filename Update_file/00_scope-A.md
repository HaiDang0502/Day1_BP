# docs/00 — Scope A (bản tái dựng từ Brief + PRD) 🟡

> Bản Scope A gốc đã duyệt 12/11/2026 không có trong tay. File này tái dựng phạm vi từ Brief và PRD để bộ tài liệu tự đứng được. Nếu tìm lại được bản gốc, thay thế toàn bộ file này.

## 1. Phạm vi được duyệt
**Sản phẩm:** trợ lý tóm tắt hồ sơ trước lượt tái khám cho bệnh nhân **đái tháo đường típ 2 ngoại trú** (có thể kèm tăng huyết áp, rối loạn lipid máu, bệnh thận mạn giai đoạn 1–3).

**Dữ liệu:** FHIR R4 đa nguồn, **100% mô phỏng**, sinh theo mô hình 3 lớp của ADR-12 (duyệt cùng ngày), tái sinh giống hệt theo seed.

**Đầu ra:** bản tóm tắt tiếng Việt 8 mục, mọi câu có nguồn bấm được; cờ an toàn từ 17 quy tắc xác định; bác sĩ sửa và duyệt; xuất FHIR Composition (P1).

**Người dùng:** bác sĩ điều trị (`doctor`), quản trị hệ thống (`admin`). Bệnh nhân là chủ thể dữ liệu, không phải người dùng.

**Kiến trúc nền:** "xác định trước, LLM sau" — LLM chỉ đảm nhiệm bước viết lời (1 trong 10 thành phần).

## 2. Ngoài phạm vi trong 31 ngày (chốt, không mở lại trước Demo Day 13/12)
- Chẩn đoán, kê đơn, đề xuất thuốc, phân loại khẩn cấp (triage).
- Đầu ra cho bệnh nhân; dữ liệu thật; tích hợp HIS thật; chức năng tải hồ sơ lên.
- Hỏi đáp tự do trên hồ sơ; ghi nhớ theo bệnh nhân.
- Đái tháo đường típ 1, thai kỳ, nhi khoa, nội trú, cấp cứu.
- Hình ảnh, ECG, thiết bị đeo, OCR giấy/scan.

## 3. Ràng buộc đã duyệt
| Ràng buộc | Giá trị |
|---|---|
| Số bệnh nhân mô phỏng | 30 (mục tiêu 50) |
| Hiệu năng | p95 ≤ 60 giây mỗi bản (timeout cứng 90 giây) |
| Hạ tầng | 1 máy ảo 2 vCPU/4 GB; URL công khai có HTTPS |
| Thời gian | 31 ngày, Demo Day 13/12/2026, freeze từ 10/12 |
| Ngân sách LLM | 75 USD |
| Dữ liệu | Chỉ mô phỏng (Luật 91/2025 xếp dữ liệu sức khỏe là dữ liệu nhạy cảm) |

## 4. Tiêu chí thành công của Scope A
A4 = 100% (không câu nào thiếu nguồn hiển thị như sự thật) · B1 = 100% (cờ không bỏ sót trên tình huống cài sẵn) · p95 ≤ 60 giây · URL công khai chạy end-to-end trên 30 bệnh nhân · không có đường lưu bản chưa duyệt.

## 5. Phê duyệt
| Vai trò | Ngày |
|---|---|
| Người điều hành dự án | 12/11/2026 |
| Mentor AI for Healthcare | 12/11/2026 |
| ADR-12 (mô hình sinh dữ liệu 3 lớp) | 12/11/2026 |
