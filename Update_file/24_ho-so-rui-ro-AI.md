# docs/24 — Hồ sơ tự phân loại rủi ro AI v2.0 ✅

Theo Luật Trí tuệ nhân tạo **134/2025/QH15** (Quốc hội thông qua 10/12/2025, hiệu lực 1/3/2026).
Mục đích: trả lời được trong một phút câu "hệ thống này thuộc nhóm rủi ro nào và kiểm soát ra sao".
*Lưu ý: văn bản hướng dẫn chi tiết tiêu chí phân loại có thể ban hành sau; rà lại hồ sơ này trước Demo Day.*

## 1. Mô tả hệ thống
AI Agent dựng bản tóm tắt tiếng Việt có cấu trúc từ hồ sơ FHIR **mô phỏng** của bệnh nhân ĐTĐ típ 2 ngoại trú. Thành phần AI duy nhất là một LLM viết lời từ dữ liệu đã được code xác định dựng sẵn (1 trong 10 thành phần). Đầu ra luôn đi qua bác sĩ sửa và duyệt.

## 2. Lập luận phân loại
Hệ thống hoạt động trong lĩnh vực y tế nên **thận trọng coi là ứng viên nhóm rủi ro cao khi triển khai với dữ liệu thật**. Tuy nhiên các đặc điểm sau giữ hệ thống ở vai trò **hỗ trợ thông tin, không ra quyết định lâm sàng**:

1. **Không có chức năng ra quyết định.** Không chẩn đoán, không kê đơn, không đề xuất thuốc, không phân loại khẩn cấp. Bộ chặn cụm chỉ định (SRS FR-05) chặn cả câu có dạng khuyến nghị.
2. **Con người giám sát đầy đủ** — đúng nguyên tắc AI không thay thế thẩm quyền và trách nhiệm của con người: bản chỉ có hiệu lực khi bác sĩ duyệt; nút Duyệt bị chặn đến khi mọi cờ Đỏ được đánh dấu đã xem xét; mọi câu cờ kết thúc "— cần bác sĩ xem xét."; không tồn tại đường lưu bản chưa duyệt (có test tự động).
3. **Không cản trở giám sát** — là hành vi bị nghiêm cấm theo Luật AI: bác sĩ xem được nguồn của từng câu, sửa từng câu, và hệ thống lưu diff giữa bản AI và bản bác sĩ.
4. **Minh bạch:** giao diện ghi rõ "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" và banner dữ liệu mô phỏng trên mọi trang.
5. **Giải trình được:** mỗi bản lưu 4 phiên bản (prompt, bộ quy tắc, mã mô hình, mã băm dữ liệu) → tái tạo được (`make reproduce`); cờ có mã quy tắc, loại nguồn và trích dẫn văn bản; quy tắc 100% xác định, có unit test dương/âm/biên.
6. **Phạm vi rủi ro thực tế ở giai đoạn hiện tại:** dữ liệu mô phỏng, người dùng chỉ gồm đội và giám khảo, không tác động tới bất kỳ bệnh nhân thật nào.

## 3. Biện pháp quản lý rủi ro đang áp dụng
| Rủi ro | Biện pháp | Đo bằng |
|---|---|---|
| Bịa đặt nội dung | Verifier 3 kiểm tra chặn câu không nguồn, số không khớp, cụm chỉ định; câu bị chặn chuyển mục "chưa xác minh được" | A4 = 100%, A5 = 100%, A6 ≥ 99% |
| Bỏ sót (lỗi chính của tóm tắt AI theo Stanford 2026) | Gold `required_facts`; mục "Cần xác minh / thiếu" luôn hiện; 4 quy tắc nhắc xét nghiệm quá hạn | A2 ≤ 10% |
| Cảnh báo mỏi | Gộp nhóm cờ theo sự kiện; B3 trung bình ≤ 3; cờ Xám thu gọn; đã thu hẹp R-DATA-01/03 | B3, B2 ≥ 90% |
| Diễn giải cờ sai lệch | LLM chỉ được diễn giải payload, không thêm bớt; so sánh với template | B4 = 100% |
| Tự động hóa quá mức | Không auto-approve; không ghi ngược HIS; không đầu ra cho bệnh nhân; không hỏi đáp tự do | test G4 |
| Trôi lệch mô hình | `model_id` khóa theo từng bản; đổi model phải chạy lại benchmark và ablation | quy trình docs/22 §3 |
| Nguồn không kiểm chứng được | Nguyên tắc 3: mỗi quy tắc ghi loại nguồn; 11/17 trích văn bản, 6/17 là quy tắc hệ thống và hiện rõ như vậy | rà docs/14 |
| Lệ thuộc vào ngưỡng tự đặt | Không tạo cờ cho tình huống QĐ 5481 không quy định (đã rà toàn văn) | docs/13 §7 |

## 4. Xử lý sự cố
Khi phát hiện bản tóm tắt sai: (1) ghi nhận vào `reports/incidents.md` kèm `summary_id`; (2) `make reproduce` để tái tạo chính xác bằng 4 phiên bản đã lưu; (3) phân loại nguyên nhân (dữ liệu / quy tắc / prompt / verifier); (4) thêm ca đó vào bộ test hồi quy; (5) nếu là lỗi quy tắc → tăng `rules_version` và chạy lại benchmark.

## 5. Việc còn lại khi triển khai với dữ liệu thật
- Theo dõi nghị định/thông tư hướng dẫn Luật AI để biết hệ thống có bị xếp nhóm rủi ro cao và phải làm thủ tục gì (đăng ký, đánh giá sự phù hợp, đại diện pháp lý nếu áp dụng).
- Cân nhắc cơ chế thử nghiệm có kiểm soát (sandbox) nếu thử nghiệm tại bệnh viện.
- Gắn nhãn nội dung do AI tạo theo quy định hướng dẫn (hiện đã có nhãn trên UI).
- Hoàn thiện hồ sơ đánh giá tác động xử lý dữ liệu cá nhân (**docs/25**) và, nếu dùng LLM cloud nước ngoài, hồ sơ chuyển dữ liệu xuyên biên giới (Điều 18 NĐ 356/2025) — hoặc chuyển sang model nội bộ (P1-9).
