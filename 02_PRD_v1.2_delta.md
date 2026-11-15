# PRD 1.2 — Danh sách sửa đổi so với PRD 1.1 (delta, chờ PO duyệt)

Mỗi mục: [vị trí] → nội dung sửa. Nguồn phát hiện: rà soát chéo Brief/PRD 15/11/2026 + rà soát pháp lý (xem 21_checklist-phap-ly.md).

## A. Pháp lý (bắt buộc)
1. [Mục 1, Bối cảnh] "Hạn để mọi cơ sở KCB có bệnh án điện tử là 31/12/2026 (TT 13/2025)" → "Bệnh viện phải hoàn thành bệnh án điện tử chậm nhất 30/9/2025; cơ sở KCB khác chậm nhất 31/12/2026 (TT 13/2025/TT-BYT)."
2. [Toàn bộ] Mọi viện dẫn "QĐ 5481/QĐ-BYT" → "QĐ 5481/QĐ-BYT (sửa đổi, bổ sung bởi QĐ 1353/QĐ-BYT ngày 23/02/2021)".
3. [Mục 7, Nguồn tham chiếu] Thêm dòng: NĐ 102/2025/NĐ-CP (quản lý dữ liệu y tế; mã định danh y tế = số định danh cá nhân, Điều 6) — ưu tiên P2, ảnh hưởng US-22.
4. [Phụ lục E, Nguồn chính] Thêm: Luật 91/2025/QH15; NĐ 356/2025/NĐ-CP; Luật AI 134/2025/QH15 (hiệu lực 1/3/2026); TT 13/2025/TT-BYT; TT 32/2023/TT-BYT (Chương X).
5. [Mục mới hoặc Phụ lục D] Bảng ánh xạ tuân thủ: P0-1, P0-9 ↔ Đ4.2 NĐ 356/2025 · P1-9 ↔ Đ18 NĐ 356/2025 · HITL + "không triage" ↔ Luật AI 134/2025 · Composition ↔ TT 13/2025 + Chương X TT 32/2023.
6. [Mục 4, bước 1 / Wireframe] Thêm dòng minh bạch AI trên UI: "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" (nguyên tắc minh bạch, Luật AI 134/2025).

## B. Nhất quán nội bộ (bắt buộc)
7. [Phụ lục B, tiêu đề] "Ngưỡng đã xác minh từ PDF gốc QĐ 5481" → "10 quy tắc trích từ QĐ 5481 (đã sửa đổi 2021); 7 quy tắc an toàn thuốc/chất lượng dữ liệu theo nguồn hệ thống (xem rule spec)". Đồng thời sửa Nguyên tắc 3: "Cờ do quy tắc, quy tắc do văn bản **hoặc nguồn hệ thống được ghi rõ và bác sĩ cố vấn xác nhận**; ngoại lệ ý kiến chuyên gia ghi 'Ý kiến chuyên gia: BS…, ngày…'".
8. [Phụ lục C, Quy tắc viết] Cụm cấm chỉ áp cho **câu do LLM sinh ở thì khuyến nghị**; câu tường thuật sự kiện do code dựng (vd "ngừng metformin 01/02/2025" trong Timeline, "khám cấp cứu" trong ghi chú gốc) không bị chặn. Định nghĩa kỹ thuật: chặn theo mẫu "nên/cần/đề nghị + hành động" và cụm chỉ định đứng ngoài trích dẫn sự kiện; chi tiết trong 10_SRS.md §KT-3.
9. [Phụ lục C] "Mọi con số phải có trong tài nguyên nguồn" → "…hoặc suy ra được bằng phép tính xác định từ fact do code dựng (tuổi, thời gian mắc, số cờ, xu hướng); các số suy ra ghi nguồn là fact tương ứng."
10. [Mục 6, dòng 2] Chốt một đường xử lý: LLM trả **sai định dạng toàn cục** (không parse được 8 mục) = lỗi pipeline → Thất bại; **từng câu** thiếu/sai nguồn = chuyển mục "chưa xác minh được". Xóa cách diễn đạt gộp "thiếu trỏ nguồn" ở dòng lỗi pipeline.
11. [Phụ lục D, B3] Ghi rõ: B3 đếm **cờ Đỏ + Vàng** (Xám không tính); "tối đa 7" là giới hạn hiển thị mặc định, không phải giới hạn sinh cờ; một sự kiện kích nhiều quy tắc thì gộp hiển thị theo sự kiện (dedup), đếm B3 theo nhóm sau dedup. Chi tiết 13_rule-spec.md §0.4.
12. [Mục 5/P0-3] Timeout: "quá 60 giây là Thất bại" → "quá **90 giây** (timeout cứng) là Thất bại; chỉ tiêu chất lượng p95 ≤ 60 giây đo trên benchmark". (Nếu đội muốn giữ 60 s cứng thì bỏ chỉ tiêu p95, chọn một.) [QUYẾT ĐỊNH — đội chốt 25/11]
13. [Mục 5, Duyệt] Điều kiện bấm Duyệt: đã mở tab Cờ **và mọi cờ Đỏ đã được đánh dấu "Đã xem xét"**. [QUYẾT ĐỊNH — khuyến nghị thêm; nếu bác sĩ phản đối qua phỏng vấn thì giữ như 1.1]
14. [Mục 2 persona] Thêm ghi chú: Brief nhắm cả Nội tổng quát; persona Nội tổng quát sẽ bổ sung sau phỏng vấn (docs/06).
15. [Mục 9, #9] Đóng: LOINC eGFR (CKD-EPI 2021) = **98979-8** (eGFRcr); phương án phụ 77147-7/62238-1 (CKD-EPI 2009) cho nguồn cũ; LDL: 2089-1 (đo trực tiếp), 13457-7 (tính). Cập nhật 18_bang-ma.md.
16. [Mục 7 / P1-4] DDInter 2.0: giấy phép **CC BY-NC-SA 4.0, truy cập mở** (đã xác nhận 15/11/2026) → bỏ chữ "cần xác nhận"; ghi vào 20_LICENSES.md; dùng phi thương mại trong đồ án là phù hợp, ghi attribution.

## C. Định nghĩa còn thiếu (đưa vào SRS, PRD chỉ trỏ)
17. "Đang dùng/đang hiệu lực" (thuốc); tuổi dữ liệu eGFR cho R-MED-01/02; định nghĩa "có hạ đường huyết" cho R-MED-07; "tăng ≥ 2 kỳ liên tiếp"; "≥ 2 bệnh kèm"; GAP khi chưa từng có xét nghiệm; mốc so sánh lệch 20% (R-DATA-01); chiều ngược đơn vị (R-DATA-02); phạm vi R-DATA-03 → tất cả chốt tại 13_rule-spec.md, PRD thêm câu "Định nghĩa vận hành của mọi thuật ngữ quy tắc: xem Rule Spec (docs/13)".
