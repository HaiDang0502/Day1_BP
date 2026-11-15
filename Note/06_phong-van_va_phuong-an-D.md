# docs/06 — Kế hoạch phỏng vấn & Phương án dự phòng §D v1.0 🟡

## A. Mục tiêu (đóng Ambiguity #2, #10 — hạn 25/11 và 2/12)
Thu số liệu có nguồn: (1) số bệnh nhân/buổi khám; (2) phút/bệnh nhân; (3) thời gian đọc hồ sơ hiện tại; (4) thứ tự 8 mục bác sĩ muốn đọc; (5) xác nhận các [QUYẾT ĐỊNH] trong rule-spec (hiệu lực đơn 90 ngày, bệnh đồng mắc, miễn glucose R-DATA-01, điều kiện Duyệt).

## B. Đối tượng & cách làm
2–3 bác sĩ Nội tiết/Nội tổng quát khám ngoại trú ĐTĐ (bệnh viện tỉnh/TP có EMR); 30–45 phút/người, online được; ghi chép có cấu trúc, xin phép ghi âm; **không** thu thập thông tin bệnh nhân thật nào trong phỏng vấn.

## C. Bộ câu hỏi
1. Một buổi khám ngoại trú của anh/chị có bao nhiêu bệnh nhân ĐTĐ típ 2? Trung bình mỗi người bao nhiêu phút?
2. Trước khi gặp bệnh nhân tái khám, anh/chị xem hồ sơ trong bao lâu? Xem gì đầu tiên?
3. Sắp xếp giúp 8 mục này theo thứ tự muốn đọc: [đưa thẻ 8 mục Phụ lục C].
4. Kết quả xét nghiệm bệnh nhân mang từ nơi khác: anh/chị xử lý thế nào? Tin ở mức nào?
5. Cảnh báo thuốc trên phần mềm hiện tại: hữu ích hay phiền? Ví dụ gần nhất bỏ qua cảnh báo?
6. Với bản tóm tắt AI có trích nguồn từng câu: anh/chị cần gì để tin và dùng? Điều gì khiến bỏ không dùng?
7. Trước khi bấm Duyệt, bắt buộc xem hết cờ Đỏ có hợp lý không?
8. (Đưa wireframe S4) 5 phút thao tác thử — nghĩ to.
9. Đơn mạn tính chỗ anh chị thường kê mấy tháng? (chốt §0.1 rule-spec)
10. Mục tiêu HbA1c nới 8% cho người ≥65 có ≥2 bệnh kèm: danh sách bệnh nào anh/chị tính?

## D. Phương án không có bác sĩ cố vấn (fallback — kích hoạt 25/11 nếu Ambiguity #2 không đóng)
- Nhóm C do mentor AI for Healthcare + 2 thành viên được huấn luyện chấm (buổi huấn luyện 2 giờ: rubric PDSQI-9 VN, 3 bản chấm mẫu đối chiếu).
- Mọi [QUYẾT ĐỊNH] y khoa chưa xác nhận: giữ đề xuất mặc định, gắn nhãn "giả định chưa kiểm chứng lâm sàng" trong rule-spec và báo cáo cuối.
- Báo cáo cuối có mục "Hạn chế": không có đánh giá của bác sĩ hành nghề; persona chưa hiệu chỉnh; PDSQI-9 bản dịch chưa chuẩn hóa.
- Tuyệt đối không nâng cấp ngôn ngữ kết quả ("được bác sĩ đánh giá") khi chỉ có nhóm fallback chấm.
