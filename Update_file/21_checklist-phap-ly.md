# docs/21 — Checklist pháp lý (G5: mỗi câu trả lời được trong 60 giây) ✅ v2.0

Rà văn bản 15/11/2026. Hai cột tình huống: **[DEMO]** = 31 ngày, dữ liệu mô phỏng · **[THẬT]** = điều kiện khi triển khai với dữ liệu thật.

---

**1. Hệ thống có xử lý dữ liệu cá nhân không?**
**[DEMO]** Không. 100% dữ liệu sinh máy theo seed, không có chủ thể dữ liệu thật (Data Card docs/17) → không thuộc phạm vi Luật Bảo vệ dữ liệu cá nhân 91/2025/QH15.
**[THẬT]** Có. Dữ liệu sức khỏe là **dữ liệu cá nhân nhạy cảm**; khi xử lý phải có phân quyền, giới hạn truy cập, quy trình xử lý và biện pháp bảo mật chặt chẽ (khoản 2 Điều 4 NĐ 356/2025/NĐ-CP). Kiến trúc hiện tại đã có RBAC theo bệnh nhân được gán (P0-1) và nhật ký append-only (P0-9).

**2. Có chuyển dữ liệu ra nước ngoài không?**
**[DEMO]** Có gọi LLM cloud (Anthropic) nhưng chỉ với dữ liệu mô phỏng → không phát sinh nghĩa vụ.
**[THẬT]** Gọi LLM cloud nước ngoài với dữ liệu thật là **chuyển dữ liệu cá nhân xuyên biên giới** → phải lập hồ sơ đánh giá tác động chuyển dữ liệu xuyên biên giới và thông báo cơ quan chuyên trách (Điều 18 NĐ 356/2025). **Đường tuân thủ đã thiết kế sẵn:** adapter OpenAI-compatible chạy model nội bộ (P1-9) → dữ liệu không ra khỏi hạ tầng cơ sở.

**3. Hệ thống có "tự ra quyết định" theo Luật Trí tuệ nhân tạo không?**
Không. Luật AI 134/2025/QH15 (thông qua 10/12/2025, hiệu lực 1/3/2026) đặt nguyên tắc AI phục vụ con người, không thay thế thẩm quyền và trách nhiệm của con người, con người giữ quyền giám sát; và nghiêm cấm hành vi cản trở hoặc vô hiệu hóa cơ chế giám sát của con người.
Thiết kế tương ứng: LLM chỉ viết lời từ fact do code dựng; cờ do quy tắc xác định; mọi câu cờ kết thúc "— cần bác sĩ xem xét."; không chẩn đoán/kê đơn/triage; chỉ bản bác sĩ duyệt có giá trị; bác sĩ xem được nguồn và sửa từng câu; hệ thống lưu diff AI/bác sĩ. UI ghi rõ nội dung do AI soạn. Hồ sơ tự phân loại rủi ro: **docs/24**.

**4. Bản tóm tắt đã duyệt có phải bệnh án không?**
**[DEMO]** Không — dữ liệu mô phỏng, không ký số.
**[THẬT]** Muốn trở thành một phần hồ sơ bệnh án (Điều 69 Luật Khám bệnh, chữa bệnh 15/2023) thì phải: nội dung khớp Chương X TT 32/2023/TT-BYT; ký hoặc xác nhận điện tử theo Điều 3 TT 13/2025/TT-BYT; hệ thống tiếp nhận đáp ứng yêu cầu phân quyền xem/nhập/sửa/hủy/khôi phục và kết xuất XML. Bảng đối chiếu: **docs/26**. Hệ thống đã chừa trường `attester`/`signature` trong Composition.

**5. Vì sao không có đầu ra cho bệnh nhân?**
Phạm vi 31 ngày, và Điều 26 Luật 91/2025 đặt yêu cầu riêng về bảo vệ dữ liệu cá nhân đối với thông tin sức khỏe. Ngoài ra nội dung dành cho bệnh nhân cần lớp kiểm soát an toàn khác hẳn (dễ bị hiểu như tư vấn y tế). Để ở P2 (US-23).

**6. Căn cứ chuyên môn của cờ an toàn là gì?**
17 quy tắc, trong đó **11 quy tắc có ngưỡng trích từ QĐ 5481/QĐ-BYT** (đã đối chiếu toàn văn bản gốc lưu trong `refs/` kèm SHA-256; QĐ 1353/QĐ-BYT 2021 sửa 2 điểm, không điểm nào ảnh hưởng các quy tắc này) và **6 quy tắc thuộc nguyên tắc an toàn thuốc / chất lượng dữ liệu của hệ thống**. Mỗi cờ hiện rõ **loại nguồn** trên giao diện, không trình bày quy tắc hệ thống như trích dẫn văn bản (Nguyên tắc 3, PRD mục 1). Chi tiết: docs/13, ánh xạ trích dẫn: docs/14.

**7. Vì sao không có cờ cho tăng huyết áp cấp cứu, kali máu, HbA1c báo động, eGFR cho SGLT2i?**
Đã rà toàn văn QĐ 5481: văn bản không quy định các ngưỡng này cho ngoại trú — ngưỡng cấp cứu chỉ nằm ở Phần 7 (biến chứng cấp, bối cảnh nội trú), và mục 4.2(b) yêu cầu tham khảo thông tin kê toa của từng hoạt chất SGLT2i tại Việt Nam. Hệ thống không tự đặt ngưỡng ngoài văn bản. Nếu bác sĩ cố vấn bổ sung, nguồn ghi là "Ý kiến chuyên gia: BS. …, ngày …".

**8. Nhật ký có vi phạm riêng tư không?**
Không ghi nội dung hồ sơ — chỉ ai, làm gì, lúc nào, trên bệnh nhân/bản nào (P0-9); bảng append-only ở tầng cơ sở dữ liệu; có kiểm tra tự động `make logcheck`. **[THẬT]** phù hợp nghĩa vụ truy vết của NĐ 356/2025 và yêu cầu phân quyền, truy vết của TT 13/2025.

**9. Tích hợp HIS thật cần gì?**
Khung NĐ 102/2025/NĐ-CP về quản lý dữ liệu y tế (Cơ sở dữ liệu quốc gia về y tế; mã định danh y tế dùng số định danh cá nhân theo Điều 6); kết nối FHIR API hoặc SMART on FHIR; hồ sơ đánh giá tác động xử lý dữ liệu cá nhân thật (**docs/25**); hợp đồng/thỏa thuận xử lý dữ liệu với cơ sở khám chữa bệnh. Tất cả là P2 (US-22); thiết kế đã chừa chỗ (docs/11 §6).

**10. Ai chịu trách nhiệm nếu bản tóm tắt sai?**
Bản nháp chưa phải sản phẩm và không được dùng lâm sàng (banner + nhãn AI + không có đường lưu bản chưa duyệt). Bản đã duyệt: bác sĩ duyệt chịu trách nhiệm chuyên môn như với ghi chép của mình; hệ thống lưu diff giữa bản AI và bản bác sĩ làm bằng chứng kiểm soát. **[THẬT]** trách nhiệm liên quan hệ thống AI theo Luật AI 134/2025 và pháp luật dân sự — cần tư vấn pháp lý trước khi thương mại hóa.

**11. Danh mục văn bản điều chỉnh trực tiếp (một dòng mỗi văn bản)**
- Luật Khám bệnh, chữa bệnh **15/2023/QH15** — hồ sơ bệnh án (Điều 69).
- Luật Bảo vệ dữ liệu cá nhân **91/2025/QH15** (hiệu lực 1/1/2026) — dữ liệu sức khỏe là dữ liệu nhạy cảm; Điều 26 về thông tin sức khỏe.
- Luật Dữ liệu **60/2024/QH15** — nền cho quản lý, chuyển dữ liệu.
- Luật Trí tuệ nhân tạo **134/2025/QH15** (hiệu lực 1/3/2026) — quản lý theo mức độ rủi ro; con người giữ quyền giám sát.
- **NĐ 356/2025/NĐ-CP** (hiệu lực 1/1/2026, thay NĐ 13/2023) — Điều 4 (dữ liệu nhạy cảm), Điều 18 (chuyển xuyên biên giới), Điều 19 (đánh giá tác động xử lý).
- **NĐ 102/2025/NĐ-CP** (hiệu lực 1/7/2025) — quản lý dữ liệu y tế; Điều 6 mã định danh y tế.
- **TT 13/2025/TT-BYT** (hiệu lực 21/7/2025) — hồ sơ bệnh án điện tử; bệnh viện chậm nhất 30/9/2025, cơ sở khác chậm nhất 31/12/2026; ký điện tử (Điều 3).
- **TT 32/2023/TT-BYT** — Chương X: nội dung hồ sơ bệnh án.
- **QĐ 5481/QĐ-BYT** (30/12/2020) + **QĐ 1353/QĐ-BYT** (23/02/2021) — hướng dẫn chẩn đoán và điều trị ĐTĐ típ 2.

---
**Cách dùng khi demo:** mỗi câu trả lời trong 60 giây. Nếu bị hỏi sâu hơn một câu, chỉ sang tài liệu tương ứng (docs/13, 14, 24, 25, 26) thay vì giải thích dài.
