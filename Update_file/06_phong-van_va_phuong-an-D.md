# docs/06 — Kế hoạch phỏng vấn & Phương án §D v2.0 ✅ ĐÃ CHỐT

## A. Trạng thái
**Giả định đang áp dụng: không có bác sĩ cố vấn dành 2–3 giờ** (quyết định PRD mục 9 #2). Phương án §D đã **kích hoạt** làm mặc định; nếu tìm được bác sĩ thì đây là nâng cấp, không phải đổi kế hoạch.

Phỏng vấn vẫn tiến hành ở dạng nhẹ (30–45 phút, không cần cam kết nhiều giờ) để thu các số liệu còn thiếu.

## B. Mục tiêu phỏng vấn
1. Số bệnh nhân mỗi buổi khám; phút mỗi bệnh nhân; thời gian đọc hồ sơ hiện tại (bù chỗ trống "chưa có số liệu Việt Nam có nguồn" trong PRD mục 1).
2. Thứ tự 8 mục bác sĩ muốn đọc (hiện giữ theo Phụ lục C).
3. Xác nhận các giả định y khoa đã chốt trong rule spec: hiệu lực đơn mạn tính 90 ngày (docs/13 §0.2), danh sách bệnh đồng mắc (§0.6), điều kiện bắt buộc ack cờ Đỏ trước khi duyệt (SRS FR-08), loại glucose khỏi R-DATA-01.

## C. Đối tượng & cách làm
2–3 bác sĩ Nội tiết hoặc Nội tổng quát khám ngoại trú ĐTĐ típ 2 tại bệnh viện tỉnh/thành phố đã có bệnh án điện tử. Online được. Ghi chép có cấu trúc, xin phép ghi âm. **Không thu thập bất kỳ thông tin bệnh nhân thật nào** trong phỏng vấn — chỉ hỏi về quy trình và cảm nhận.

## D. Bộ câu hỏi
1. Một buổi khám ngoại trú của anh/chị có bao nhiêu bệnh nhân ĐTĐ típ 2? Trung bình mỗi người bao nhiêu phút?
2. Trước khi gặp bệnh nhân tái khám, anh/chị xem hồ sơ trong bao lâu? Xem gì đầu tiên?
3. Sắp xếp giúp 8 mục này theo thứ tự muốn đọc *(đưa thẻ 8 mục Phụ lục C)*.
4. Kết quả xét nghiệm bệnh nhân mang từ nơi khác: anh/chị xử lý thế nào? Tin ở mức nào?
5. Cảnh báo thuốc trên phần mềm hiện tại: hữu ích hay phiền? Ví dụ gần nhất anh/chị bỏ qua một cảnh báo?
6. Với bản tóm tắt AI có trích nguồn từng câu: anh/chị cần gì để tin và dùng? Điều gì khiến bỏ không dùng?
7. Trước khi bấm Duyệt, bắt buộc xem hết cờ Đỏ có hợp lý không, hay làm chậm việc?
8. *(Đưa wireframe S4)* 5 phút thao tác thử — nghĩ to.
9. Đơn thuốc mạn tính chỗ anh/chị thường kê mấy tháng một lần?
10. Mục tiêu HbA1c nới lên 8% cho người ≥ 65 tuổi có nhiều bệnh kèm: anh/chị tính những bệnh nào vào "nhiều bệnh"?

## E. Phương án §D — nhóm chấm thay thế (đang áp dụng)
- **Thành phần:** mentor AI for Healthcare + 2 thành viên đội được huấn luyện.
- **Huấn luyện:** 1 buổi 2 giờ — rubric PDSQI-9 tiếng Việt; chấm thử 3 bản; đối chiếu và thống nhất cách cho điểm; chỉ bắt đầu chấm thật khi 3 người lệch ≤ 1 điểm trên mỗi tiêu chí.
- **Giới hạn phải ghi vào báo cáo cuối:** (1) không có đánh giá của bác sĩ đang hành nghề; (2) persona chưa hiệu chỉnh bằng phỏng vấn nếu không thu được; (3) PDSQI-9 dùng bản dịch chưa chuẩn hóa; (4) các giả định y khoa trong rule spec chưa được bác sĩ xác nhận — đã gắn nhãn "giả định chưa kiểm chứng lâm sàng" tại chỗ.
- **Ngôn ngữ:** không dùng cách diễn đạt gợi ý có bác sĩ tham gia đánh giá.

## F. Các giả định y khoa chưa xác nhận (danh sách theo dõi)
| # | Giả định | Nằm ở | Ảnh hưởng nếu sai |
|---|---|---|---|
| 1 | Đơn mạn tính không ghi thời lượng → mặc định 90 ngày | docs/13 §0.2 | Thuốc hết hiệu lực bị coi là đang dùng → cờ sai (B2 giảm) |
| 2 | Danh sách 7 nhóm bệnh đồng mắc | docs/13 §0.6 | Mục tiêu HbA1c chọn sai bậc → R-TREND-01 sai |
| 3 | Dung sai 183 ngày cho HbA1c (văn bản yêu cầu 3 tháng) | docs/13 §4 R-GAP-01 | Bỏ sót nhắc xét nghiệm quá hạn |
| 4 | Loại glucose khỏi R-DATA-01 | docs/13 §5 | Bỏ sót mâu thuẫn glucose thật |
| 5 | Bắt buộc ack cờ Đỏ trước khi duyệt | SRS FR-08 | Thêm bước, có thể gây phiền |
