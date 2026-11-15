# docs/26 — Bảng đối chiếu TT 13/2025/TT-BYT và Chương X TT 32/2023/TT-BYT v2.0 ✅

Mục đích: chứng minh FHIR Composition xuất ra (P1-5) **tương thích** khung bệnh án điện tử, để khi triển khai thật hệ thống EMR của cơ sở tiếp nhận được.

## 1. Đối chiếu yêu cầu
| Yêu cầu | Hệ thống hiện tại | Trạng thái |
|---|---|---|
| Hồ sơ bệnh án điện tử có giá trị pháp lý tương đương bản giấy (Điều 1 TT 13/2025; Điều 69 Luật KBCB 15/2023) | Composition là tài liệu đính kèm quy trình, không tự nhận là bệnh án. Việc công nhận thuộc hệ thống EMR tiếp nhận | 🟡 ngoài phạm vi demo, nêu rõ khi demo |
| Nội dung hồ sơ đầy đủ theo Chương X TT 32/2023 | Ánh xạ 8 mục → §2 dưới đây | 🟡 ánh xạ đã thiết kế, hoàn thiện khi code P1-5 |
| Ký hoặc xác nhận điện tử, ba hình thức (Điều 3 TT 13/2025) | Chưa ký số ở demo; Composition đã chừa `attester` + `signature` | 🟡 chừa sẵn (docs/11 §6) |
| Phân quyền xem, nhập mới, chỉnh sửa, hủy, khôi phục | Có: RBAC theo bệnh nhân được gán; bản duyệt khóa (409); cờ không xóa được; mọi thao tác vào nhật ký. "Hủy / khôi phục" theo nghĩa của TT cần quy trình hủy có kiểm soát — chưa có ở demo | 🟡 đủ cho demo, cần bổ sung khi thật |
| Kết xuất được theo định dạng XML | FHIR R4 hỗ trợ XML; endpoint export nhận `?_format=xml` (SRS FR-17) | ✅ |
| Lộ trình: bệnh viện chậm nhất 30/9/2025; cơ sở khác chậm nhất 31/12/2026 (Điều 4 TT 13/2025) | Hệ thống nhắm bệnh viện đã có EMR → đúng giai đoạn sau lộ trình | ✅ lập luận thị trường |
| Bảo mật, an toàn thông tin của hệ thống | HTTPS, RBAC, nhật ký append-only, secrets qua env; [THẬT] cần thẩm định an toàn thông tin theo cấp độ | 🟡 |
| Lưu trữ, dự phòng | `make dump` + sha256; [THẬT] theo quy định lưu trữ hồ sơ bệnh án | 🟡 |

## 2. Ánh xạ 8 mục → cấu trúc bệnh án (Chương X TT 32/2023)
| Mục trong bản tóm tắt | Phần tương ứng trong hồ sơ bệnh án | Composition section |
|---|---|---|
| (hành chính — từ Patient) | Thông tin hành chính người bệnh | `subject` + section "Hành chính" |
| 1. 5 dòng chính | Tóm tắt bệnh sử / tình trạng hiện tại | section "Tóm tắt" |
| 2. Vấn đề đang theo dõi | Chẩn đoán | section "Chẩn đoán" (Condition refs) |
| 3. Thuốc đang dùng | Điều trị / đơn thuốc | section "Thuốc" (MedicationRequest refs) |
| 4. Dị ứng | Tiền sử dị ứng | section "Dị ứng" (AllergyIntolerance refs) |
| 5. Xu hướng xét nghiệm | Kết quả cận lâm sàng | section "Cận lâm sàng" (Observation refs) |
| 6. Cờ an toàn | *(không có mục tương ứng — là nội dung do hệ thống sinh)* | section "Lưu ý an toàn" + ghi rõ nguồn từng cờ |
| 7. Cần xác minh / thiếu | *(không có mục tương ứng)* | section "Dữ liệu còn thiếu" |
| 8. Timeline | Diễn biến bệnh | section "Diễn biến" (Encounter refs) |
Mỗi section có `entry` trỏ về resource gốc → giữ được tính truy nguyên khi EMR tiếp nhận.

## 3. Câu trả lời một dòng cho giám khảo
"Chúng tôi không tự nhận là bệnh án điện tử. Hệ thống xuất FHIR Composition đúng định dạng, có XML, mỗi mục trỏ về tài nguyên gốc, và đã chừa trường ký số; việc công nhận giá trị pháp lý thuộc hệ thống bệnh án điện tử của cơ sở tiếp nhận theo TT 13/2025."

## 4. Việc cần làm khi triển khai thật
1. Hoàn thiện ánh xạ chi tiết từng trường với Chương X TT 32/2023 cùng bộ phận chuyên môn của cơ sở.
2. Tích hợp ký số theo một trong ba hình thức của Điều 3 TT 13/2025.
3. Bổ sung quy trình hủy / khôi phục có kiểm soát và ghi nhật ký.
4. Thẩm định an toàn thông tin theo cấp độ của hệ thống.
5. Thống nhất với cơ sở: bản tóm tắt đã duyệt được lưu như loại tài liệu nào trong hồ sơ.
