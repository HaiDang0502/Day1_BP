# docs/09 — Tài liệu nghiệp vụ v2.0 ✅

## 1. Bối cảnh vận hành
- Khám ngoại trú bệnh mạn tính tại bệnh viện tỉnh/thành phố: bệnh nhân ĐTĐ típ 2 tái khám 1–3 tháng một lần; hồ sơ rải qua nhiều lần khám và nhiều cơ sở; nhiều bệnh nhân mang kết quả xét nghiệm từ nơi khác (bản giấy hoặc hệ thống khác).
- **Hạ tầng số:** TT 13/2025/TT-BYT quy định bệnh viện hoàn thành hồ sơ bệnh án điện tử chậm nhất 30/9/2025; cơ sở khám chữa bệnh khác chậm nhất 31/12/2026. Nội dung hồ sơ theo Chương X TT 32/2023/TT-BYT. Ký/xác nhận điện tử theo Điều 3 TT 13/2025 (ba hình thức). Vì vậy giả định "bệnh viện tỉnh đã có bệnh án điện tử" là có căn cứ.
- Khoảng 8 triệu người lớn mắc ĐTĐ, chủ yếu típ 2; khoảng 36% bệnh nhân típ 2 đạt mục tiêu HbA1c.
- **Theo QĐ 5481:** HbA1c cần làm mỗi 3 tháng (Bảng 2); creatinin/eGFR và albumin niệu ít nhất mỗi năm (Phần 8 mục 1.2(a)), hai lần mỗi năm nếu eGFR < 60 hoặc albumin niệu > 30 mg/g (mục 1.2(b)); bộ lipid ít nhất hàng năm (mục 6.2.1); đánh giá lại chiến lược điều trị mỗi 3 tháng (mục 4.2(i)). Đây là cơ sở cho 4 quy tắc nhắc xét nghiệm quá hạn.

## 2. Quy trình hiện tại (as-is)
Tiếp đón → đo sinh hiệu → bác sĩ mở bệnh án điện tử, đọc nhanh các lần khám trước (vài phút, dễ sót) → khám → chỉ định xét nghiệm, kê đơn → ghi bệnh án → ký.

**Điểm đau:** đọc lại toàn bộ không khả thi; dị ứng, chống chỉ định theo chức năng thận và xét nghiệm quá hạn ghi rải rác nhiều nơi; kết quả ngoài viện không chuẩn hóa đơn vị; trùng hoạt chất khác biệt dược khó phát hiện bằng mắt.

## 3. Quy trình với hệ thống (to-be)
**Trước giờ khám:** bác sĩ bấm "Tạo bản tóm tắt" → đọc 5 dòng chính (mục tiêu 10 giây) → mở tab Cờ, xem và đánh dấu từng cờ (bắt buộc với cờ Đỏ) → xem mục "Cần xác minh / thiếu" → sửa câu nếu cần → Duyệt. Bản duyệt bị khóa, ghi nhật ký; xuất được FHIR Composition (P1).

**Hệ thống KHÔNG thay đổi** bước khám, kê đơn, ghi bệnh án hay ký — chỉ thêm một bước đọc chuẩn bị.

**Giá trị pháp lý:** bản tóm tắt đã duyệt trong demo **không phải** bệnh án (dữ liệu mô phỏng, không ký số). Khi triển khai thật, muốn Composition trở thành một phần hồ sơ bệnh án (Điều 69 Luật Khám bệnh, chữa bệnh 15/2023) thì phải: nội dung khớp Chương X TT 32/2023, ký/xác nhận điện tử theo Điều 3 TT 13/2025, và hệ thống tiếp nhận đáp ứng yêu cầu phân quyền/kết xuất XML của TT 13/2025. Bảng đối chiếu: docs/26.

## 4. Vai trò — trách nhiệm (RACI)
| Việc | Bác sĩ | Admin IT | Đội dự án |
|---|---|---|---|
| Tạo tài khoản | — | R/A | C |
| Gán bệnh nhân cho bác sĩ | I | R/A | — |
| Tạo bản tóm tắt | R/A | — | — |
| Xử lý và đánh dấu cờ | R/A | — | — |
| Sửa và duyệt bản tóm tắt | R/A | — | — |
| Tra nhật ký | I | R/A | — |
| Nạp dữ liệu, chạy benchmark | — | — | R/A |
| Chịu trách nhiệm chuyên môn với bản đã duyệt | **R/A** | — | — |

Bệnh nhân: chủ thể dữ liệu theo Luật 91/2025, không phải người dùng; quyền của chủ thể dữ liệu được thực hiện qua cơ sở khám chữa bệnh khi triển khai thật.

## 5. Ràng buộc nghiệp vụ cứng
1. Không chẩn đoán, không kê đơn, không đề xuất thuốc, không triage. Mọi câu cờ kết thúc "— cần bác sĩ xem xét." (phù hợp nguyên tắc con người giữ quyền giám sát của Luật AI 134/2025).
2. Chỉ bản đã duyệt được coi là sản phẩm; không tồn tại đường lưu bản chưa duyệt (có test tự động).
3. "Không có dữ liệu" là câu trả lời hợp lệ và bắt buộc; không suy đoán.
4. Bác sĩ chỉ thấy bệnh nhân được gán; mọi truy cập sai quyền bị từ chối và ghi nhật ký.
5. Nhật ký không chứa nội dung hồ sơ.
