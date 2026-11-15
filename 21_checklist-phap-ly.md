# docs/21 — Checklist pháp lý (G5: mỗi câu trả lời được trong 60 giây) ✅ v1.0 — rà văn bản 15/11/2026

Định dạng hỏi–đáp để demo/giám khảo. Hai cột trạng thái: [DEMO] = 31 ngày, dữ liệu mô phỏng; [THẬT] = điều kiện khi triển khai thật.

**1. Hệ thống có xử lý dữ liệu cá nhân không?**
[DEMO] Không. 100% dữ liệu sinh máy theo seed (Data Card docs/17), không có chủ thể dữ liệu thật → không thuộc phạm vi Luật 91/2025/QH15. [THẬT] Có — dữ liệu sức khỏe là dữ liệu cá nhân nhạy cảm; phải có căn cứ xử lý, phân quyền, giới hạn truy cập, bảo mật (Đ4.2 NĐ 356/2025) — kiến trúc hiện tại đã có RBAC + audit (P0-1, P0-9).

**2. Có chuyển dữ liệu ra nước ngoài không?**
[DEMO] Có gọi LLM cloud (Anthropic) nhưng chỉ dữ liệu mô phỏng → không phát sinh nghĩa vụ. [THẬT] Gọi LLM cloud nước ngoài với dữ liệu thật = chuyển dữ liệu xuyên biên giới → cần hồ sơ đánh giá tác động chuyển dữ liệu (Mẫu 09) + thông báo Bộ Công an (Đ18 NĐ 356/2025). Đường tuân thủ đã thiết kế: adapter chạy model nội bộ (P1-9) → không chuyển dữ liệu ra ngoài.

**3. Hệ thống có "tự ra quyết định" theo Luật AI không?**
Không. Luật AI 134/2025/QH15 (hiệu lực 1/3/2026): AI không thay thế thẩm quyền và trách nhiệm con người; con người giữ quyền giám sát. Thiết kế: LLM chỉ viết lời từ fact do code dựng; cờ do quy tắc xác định; mọi câu cờ "— cần bác sĩ xem xét."; chỉ bản bác sĩ duyệt có giá trị; không triage (lý do ghi Phụ lục B PRD). UI ghi rõ nội dung do AI soạn (minh bạch). Hồ sơ tự phân loại rủi ro: docs/24.

**4. Bản tóm tắt đã duyệt có phải bệnh án không?**
[DEMO] Không — dữ liệu mô phỏng, không ký số. [THẬT] Muốn là một phần hồ sơ bệnh án (Luật KBCB 15/2023 Đ69) phải: nội dung khớp Chương X TT 32/2023; ký/xác nhận điện tử theo Đ3 TT 13/2025; hệ thống đáp ứng phân quyền xem/nhập/sửa/hủy/khôi phục + kết xuất XML (TT 13/2025). Bảng đối chiếu: docs/26.

**5. Vì sao không có đầu ra cho bệnh nhân?**
Phạm vi 31 ngày + Điều 26 Luật 91/2025 đặt yêu cầu riêng với cung cấp thông tin sức khỏe; ngoài ra nội dung cho bệnh nhân cần kiểm soát an toàn khác hẳn (US-23, P2).

**6. Căn cứ chuyên môn của cờ an toàn?**
10/17 quy tắc: QĐ 5481/QĐ-BYT (sđ QĐ 1353/2021) — mã quy tắc + trích dẫn bấm được từng cờ. 7/17: nguyên tắc an toàn thuốc/chất lượng dữ liệu, nguồn hệ thống ghi rõ, bác sĩ cố vấn xác nhận (rule spec docs/13). Cố ý KHÔNG đặt ngưỡng QĐ 5481 không quy định (HA cấp cứu, kali, eGFR cho SGLT2i); bổ sung nếu có ý kiến chuyên gia ghi danh.

**7. Nhật ký có vi phạm riêng tư không?**
Không ghi nội dung hồ sơ — chỉ ai, làm gì, lúc nào, trên bệnh nhân/bản nào (P0-9); append-only. [THẬT] khớp nghĩa vụ truy vết của NĐ 356/2025 và quy chế bệnh án điện tử TT 13/2025.

**8. Tích hợp HIS thật cần gì?**
Khung NĐ 102/2025 (CSDL y tế, mã định danh y tế = số định danh cá nhân, Đ6); FHIR API/SMART on FHIR; DPIA thật (docs/25); hợp đồng xử lý dữ liệu với bệnh viện. Tất cả là P2 (US-22) — đã chừa thiết kế (docs/11 §7).

**9. Văn bản nào điều chỉnh trực tiếp? (danh mục 1 dòng/văn bản)**
Luật KBCB 15/2023 (Đ69 hồ sơ bệnh án) · Luật 91/2025 (DLCN; Đ26 sức khỏe) · Luật Dữ liệu 60/2024 · Luật AI 134/2025 (hiệu lực 1/3/2026) · NĐ 356/2025 (chi tiết Luật 91; Đ4, Đ18, Đ19) · NĐ 102/2025 (dữ liệu y tế) · TT 13/2025/TT-BYT (bệnh án điện tử; mốc 30/9/2025 BV, 31/12/2026 cơ sở khác) · TT 32/2023/TT-BYT (Chương X) · QĐ 5481/QĐ-BYT + QĐ 1353/QĐ-BYT (chuyên môn ĐTĐ típ 2).

**10. Ai chịu trách nhiệm nếu bản tóm tắt sai?**
Bản nháp: chưa phải sản phẩm, không dùng lâm sàng (banner). Bản duyệt: bác sĩ duyệt chịu trách nhiệm chuyên môn như với ghi chép của mình — hệ thống lưu diff AI/bác sĩ làm bằng chứng kiểm soát; [THẬT] trách nhiệm bồi thường AI theo Luật AI 134/2025 và dân sự — cần tư vấn pháp lý khi thương mại hóa.
