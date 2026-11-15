# docs/26 — Bảng đối chiếu TT 13/2025/TT-BYT (và Chương X TT 32/2023) ✅ v1.0

Mục đích: chứng minh FHIR Composition xuất ra (P1-5) tương thích khung bệnh án điện tử — điều kiện để "bản duyệt trở thành một phần hồ sơ" khi triển khai thật.

| Yêu cầu (TT 13/2025 & TT 32/2023) | Hệ thống hiện tại | Trạng thái |
|---|---|---|
| Giá trị pháp lý hồ sơ bệnh án điện tử tương đương bản giấy (Đ1 TT 13; Đ69 Luật KBCB) | Composition chỉ là tài liệu đính kèm quy trình; [THẬT] cần cơ sở KCB tiếp nhận vào EMR | 🟡 ngoài phạm vi demo, ghi rõ |
| Nội dung hồ sơ đầy đủ theo Chương X TT 32/2023 | 8 mục của Composition ánh xạ: hành chính (Patient) · chẩn đoán (mục 2) · thuốc (mục 3) · dị ứng (mục 4) · cận lâm sàng (mục 5) · diễn biến (mục 8 Timeline) — bảng ánh xạ chi tiết lập khi code P1-5 | 🟡 |
| Ký/xác nhận điện tử (Đ3 TT 13: 3 hình thức) | Chưa có ký số ở demo; Composition chừa trường attester/signature | 🟡 thiết kế sẵn (docs/11 §7) |
| Phân quyền xem, nhập mới, chỉnh sửa, hủy, khôi phục | RBAC doctor/admin; bản duyệt khóa (409); không hủy cờ; audit đầy đủ | ✅ (demo) — "hủy/khôi phục" theo nghĩa TT: [THẬT] bổ sung quy trình hủy có kiểm soát |
| Kết xuất được theo XML | FHIR R4 xuất được XML (serializer chuẩn FHIR) — thêm `?_format=xml` cho endpoint export | ✅ dễ, đưa vào P1-5 |
| Lộ trình: BV chậm nhất 30/9/2025; cơ sở khác 31/12/2026 | Hệ thống nhắm BV đã có EMR (persona) — phù hợp giai đoạn sau lộ trình | ✅ lập luận thị trường |
| Bảo mật, an toàn thông tin hệ thống EMR | HTTPS, RBAC, audit, secrets env; [THẬT] cần thẩm định an toàn thông tin theo cấp độ | 🟡 |
| Lưu trữ, dự phòng | make dump + sha256; [THẬT] theo quy định lưu trữ hồ sơ bệnh án | 🟡 |

Kết luận demo: hệ thống KHÔNG tự nhận là EMR/bệnh án điện tử; nó là công cụ hỗ trợ đọc hồ sơ, xuất Composition **tương thích** để EMR tiếp nhận. Câu một dòng cho giám khảo: "Chúng tôi xuất đúng định dạng và chừa chỗ ký số; việc công nhận pháp lý thuộc hệ thống EMR của cơ sở tiếp nhận theo TT 13/2025."
