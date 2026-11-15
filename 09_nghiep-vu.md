# docs/09 — Tài liệu nghiệp vụ v1.0 🟡 (hiệu chỉnh sau phỏng vấn docs/06)

## 1. Bối cảnh vận hành
- Khám ngoại trú bệnh mạn tính tại bệnh viện tỉnh/TP: bệnh nhân ĐTĐ típ 2 tái khám 1–3 tháng/lần; hồ sơ rải nhiều lần khám, nhiều cơ sở; nhiều bệnh nhân mang kết quả xét nghiệm từ nơi khác (bản giấy hoặc EMR khác).
- Bệnh án điện tử: bệnh viện phải hoàn thành chậm nhất 30/9/2025, cơ sở khác chậm nhất 31/12/2026 (TT 13/2025/TT-BYT) → giả định "đã có EMR" là nền của dự án. Nội dung bệnh án theo Chương X TT 32/2023/TT-BYT; ký xác nhận điện tử theo Điều 3 TT 13/2025.
- Khoảng 8 triệu người lớn mắc ĐTĐ, ~36% bệnh nhân típ 2 đạt mục tiêu HbA1c (nguồn Brief; số liệu buổi khám chưa có nguồn VN — thu qua phỏng vấn).

## 2. Quy trình nghiệp vụ hiện tại (as-is)
Tái khám: tiếp đón → đo sinh hiệu → bác sĩ mở EMR, đọc nhanh các lần khám trước (vài phút, dễ sót) → khám → chỉ định XN/kê đơn → ghi bệnh án → ký. Điểm đau: đọc lại toàn bộ không khả thi; dị ứng/chống chỉ định/XN quá hạn ghi rải rác; kết quả ngoài viện không chuẩn hóa.

## 3. Quy trình với hệ thống (to-be)
Trước giờ khám: bác sĩ (hoặc theo lịch) bấm Tạo bản tóm tắt → đọc 5 dòng chính 10 giây → xử lý cờ → sửa, duyệt → bản duyệt khóa, xuất Composition (P1) để lưu/bàn giao. **Hệ thống không thay đổi bước khám, kê đơn, ghi bệnh án, ký** — chỉ thêm 1 bước đọc chuẩn bị. Bản tóm tắt duyệt ở demo KHÔNG phải bệnh án pháp lý (dữ liệu mô phỏng, không ký số); khi triển khai thật cần gắn ký điện tử theo TT 13/2025 để Composition trở thành một phần hồ sơ (Luật KBCB 15/2023 Đ69).

## 4. Vai trò — trách nhiệm (RACI rút gọn)
| Việc | Bác sĩ | Admin IT | Đội dự án |
|---|---|---|---|
| Gán bệnh nhân | I | R/A | — |
| Tạo/sửa/duyệt tóm tắt | R/A | — | — |
| Xử lý cờ | R/A | — | — |
| Tra nhật ký | — | R/A | — |
| Nạp dữ liệu, benchmark | — | — | R/A |
Bệnh nhân: chủ thể dữ liệu (Luật 91/2025), không dùng hệ thống; quyền chủ thể dữ liệu thực hiện qua cơ sở KCB khi triển khai thật.

## 5. Ràng buộc nghiệp vụ cứng
- Không chẩn đoán/kê đơn/triage; mọi cờ kết thúc "— cần bác sĩ xem xét." (Luật AI 134/2025: con người giữ quyền giám sát).
- Chỉ bản đã duyệt được coi là sản phẩm; không đường lưu bản chưa duyệt.
- "Không có dữ liệu" là câu trả lời hợp lệ và bắt buộc; không suy đoán.
