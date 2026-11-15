# Brief v1.1 (đề xuất) — Trợ lý tóm tắt hồ sơ trước lượt tái khám

Mã đề: VMEC-03 · Đội: P-XXX · Ngày: [điền] · Demo Day: 13/12/2026
**Thay đổi so với Brief 1 trang hiện tại (các chỗ [đổi]):**

## Bài toán
(Giữ nguyên) + [đổi] bổ sung: bệnh viện phải hoàn thành bệnh án điện tử chậm nhất 30/9/2025, cơ sở KCB khác chậm nhất 31/12/2026 (TT 13/2025/TT-BYT) → người dùng mục tiêu là bệnh viện **đã có** bệnh án điện tử là giả định có căn cứ pháp lý.

## Người dùng mục tiêu
(Giữ nguyên bảng) + [đổi] ghi chú: persona chính trong PRD hiện là bác sĩ Nội tiết; nếu giữ "Nội tổng quát" ở Brief thì thêm persona phụ tương ứng trong PRD, hoặc bỏ "Nội tổng quát" khỏi Brief. → đề xuất: giữ cả hai ở Brief, thêm 1 dòng ghi chú trong PRD mục 2.

## Giải pháp
(Giữ nguyên 4 gạch đầu dòng) + [đổi] câu quy tắc: "Chạy 17 quy tắc an toàn xác định: 10 quy tắc trích từ QĐ 5481/QĐ-BYT (đã sửa đổi bởi QĐ 1353/QĐ-BYT 2021) và 7 quy tắc an toàn thuốc/chất lượng dữ liệu có nguồn hệ thống được ghi rõ." (Sửa vì 7/17 quy tắc không nằm trong QĐ 5481 — xem 13_rule-spec.md.)

## Vì sao cần AI
[đổi] "8 trong 9 bước còn lại" → "9 trong 10 bước của hệ thống là phần mềm xác định (chuẩn hóa dữ liệu, dòng thời gian, quy tắc cờ, kiểm tra nguồn và số, phân quyền, nhật ký); LLM chỉ đảm nhiệm 1 bước: viết bản tóm tắt." (Bỏ cách đếm "8/9" vì phân quyền và nhật ký không thuộc 9 bước pipeline.)

## Không làm (Non-goals)
(Giữ nguyên.)

## Đo thành công bằng gì
[đổi] để khớp PRD Phụ lục D:
- Trung thực: **câu không nguồn hiển thị như sự thật = 0 (A4 = 100%)** · câu không được nguồn hỗ trợ ≤ 2% (A1) · độ chính xác trích dẫn ≥ 95% (A3) · bỏ sót ≤ 10% (A2).
- Cờ an toàn: recall = 100% (B1) · precision ≥ 90% (B2) · trung bình ≤ 3 cờ/bệnh nhân (B3).
- Chạy thật: p95 ≤ 60 s · URL công khai end-to-end trên 30 bệnh nhân mô phỏng.
(Bỏ thuật ngữ "citation recall" không định nghĩa trong PRD.)

## Chân trang
Toàn bộ dữ liệu là mô phỏng · Scope A duyệt 12/11/2026 · Chi tiết: PRD, Wireframe/UI Flow, SRS, Thiết kế hệ thống · [đổi] thêm: "Hạn hạ tầng demo (máy ảo + tên miền): 1/12/2026" — để PRD mục 9 viện dẫn được ngày 1/12 từ Brief.
