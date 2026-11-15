# docs/24 — Hồ sơ tự phân loại rủi ro AI 🟡 v1.0 (theo Luật Trí tuệ nhân tạo 134/2025/QH15, hiệu lực 1/3/2026)

> Mục đích: trả lời giám khảo/đối tác "hệ thống thuộc nhóm rủi ro nào và kiểm soát ra sao". Mentor pháp lý rà trước 2/12. Lưu ý: văn bản hướng dẫn chi tiết phân loại có thể ban hành sau — rà cập nhật trước Demo Day.

## 1. Mô tả hệ thống
AI Agent dựng bản tóm tắt có cấu trúc từ hồ sơ FHIR mô phỏng của bệnh nhân ĐTĐ típ 2; thành phần AI duy nhất là LLM viết lời từ dữ liệu đã được code xác định dựng sẵn; đầu ra luôn qua bác sĩ sửa/duyệt.

## 2. Lập luận phân loại
Hệ thống chạm lĩnh vực y tế (nhạy cảm) → thận trọng coi như **ứng viên nhóm rủi ro cao khi triển khai thật**; tuy nhiên các đặc điểm sau giữ hệ thống ở vai trò **hỗ trợ thông tin, không ra quyết định lâm sàng**:
1. Không chẩn đoán, không kê đơn, không triage, không khuyến nghị điều trị (Non-goals; bộ chặn cụm chỉ định FR-05).
2. Con người giám sát đầy đủ: bản chỉ có hiệu lực khi bác sĩ duyệt; cờ Đỏ buộc ack trước duyệt; mọi cờ "— cần bác sĩ xem xét." → đáp ứng nguyên tắc "AI không thay thế thẩm quyền, trách nhiệm con người; con người giữ quyền giám sát" (Đ4 Luật AI).
3. Không có cơ chế nào cản trở giám sát con người (điều cấm của Luật AI): bác sĩ xem được nguồn từng câu, sửa từng câu, hệ thống lưu diff.
4. Minh bạch: UI ghi "Bản nháp do AI soạn — bác sĩ xem xét và duyệt"; người dùng luôn biết đang tương tác với đầu ra AI.
5. Khả năng giải trình: mọi bản lưu (prompt, rules, model, data hash); cờ có mã quy tắc + trích dẫn văn bản; quy tắc 100% xác định có unit test.
6. [DEMO] không có người dùng thật ngoài đội + giám khảo, dữ liệu mô phỏng → rủi ro thực tế tối thiểu.

## 3. Biện pháp quản lý rủi ro đang áp dụng (map điều khoản khi có NĐ hướng dẫn)
| Rủi ro | Kiểm soát |
|---|---|
| Bịa đặt nội dung | Verifier chặn câu không nguồn/số lệch → mục "chưa xác minh được"; A4=100%, A5=100% |
| Bỏ sót (lỗi chính theo Stanford 2026) | Gold required_facts, A2 ≤ 10%; mục "Cần xác minh/thiếu" luôn hiện |
| Cảnh báo mỏi (alert fatigue) | B3 ≤ 3 trung bình, dedup theo sự kiện, Xám thu gọn |
| Tự động hóa quá mức | Không auto-approve; không ghi ngược HIS; không đầu ra bệnh nhân |
| Trôi lệch model | Khóa model_id từng bản; đổi model phải chạy lại benchmark |
| Sự cố | Quy trình: ghi nhận, tái tạo bằng 4 phiên bản đã lưu, báo cáo trong repo |

## 4. Việc còn lại khi triển khai thật
Đăng ký/thủ tục theo nghị định hướng dẫn Luật AI (theo dõi); đánh giá sự phù hợp nếu bị xếp rủi ro cao; cân nhắc sandbox AI nếu thử nghiệm tại bệnh viện; gắn nhãn nội dung AI theo quy định hướng dẫn.
