# PRD — Trợ lý tóm tắt hồ sơ trước lượt tái khám

Mã đề: VMEC-03 · Đội: P-XXX · Ngày: 15/11/2026 · **Phiên bản: 1.2** (từ 1.1 ngày 15/11/2026)

| | |
|---|---|
| **Chủ sở hữu** | Product Owner: E |
| **Người duyệt** | Người điều hành dự án; mentor AI for Healthcare |
| **Trạng thái** | Đã duyệt Scope A (12/11/2026) và ADR-12. **Không còn mục chờ chốt** — mọi quyết định treo của 1.1 đã được quyết trong 1.2 (xem mục 9) |
| **Tài liệu gốc** | docs/00 scope · docs/09 nghiệp vụ · docs/10 SRS · docs/11 thiết kế · docs/13 rule spec · docs/05 benchmark · docs/14 trích lục QĐ 5481 |

**Thay đổi chính của 1.2:** đối chiếu toàn văn QĐ 5481/QĐ-BYT và QĐ 1353/QĐ-BYT → sửa 3 quy tắc (R-MED-03, R-GAP-01, R-GAP-03) và chốt nguồn cho cả 17 quy tắc; bổ sung khung pháp lý hiện hành (Luật 91/2025, NĐ 356/2025, NĐ 102/2025, Luật AI 134/2025, TT 13/2025); giải quyết toàn bộ mâu thuẫn nội bộ của 1.1.

---

## 1. Bối cảnh & Mục tiêu

**Bối cảnh.** Bác sĩ ngoại trú phải nắm diễn biến của bệnh nhân mạn tính qua nhiều lần khám và nhiều cơ sở trong thời gian ngắn. Đọc lại toàn bộ thì không khả thi, còn đọc vội dẫn tới bỏ sót xu hướng xấu, chống chỉ định theo chức năng thận, dị ứng ghi ở nơi khác và xét nghiệm định kỳ quá hạn. Việt Nam có khoảng 8 triệu người lớn mắc ĐTĐ, chủ yếu típ 2, và chỉ khoảng 36% bệnh nhân típ 2 đạt mục tiêu HbA1c. Nghiên cứu Stanford 2026 cho thấy lỗi chính của tóm tắt AI là bỏ sót (25%), không phải bịa đặt (2%).

Về hạ tầng: TT 13/2025/TT-BYT quy định bệnh viện hoàn thành hồ sơ bệnh án điện tử chậm nhất **30/9/2025**, cơ sở khám chữa bệnh khác chậm nhất **31/12/2026**. Nội dung hồ sơ theo Chương X TT 32/2023/TT-BYT. Vì vậy giả định "bệnh viện tỉnh đã có bệnh án điện tử" có căn cứ pháp lý.

*Chưa có số liệu Việt Nam có nguồn về số bệnh nhân mỗi buổi khám, phút mỗi bệnh nhân và thời gian đọc hồ sơ; các số này thu qua phỏng vấn (docs/06). Con số "65 bệnh nhân/ngày" hay được nhắc không tìm thấy văn bản gốc nên không dùng.*

**Giải pháp.** Agent đọc toàn bộ hồ sơ FHIR đa nguồn của một bệnh nhân, dựng dòng thời gian, chuẩn hóa thuốc và xét nghiệm, chạy bộ quy tắc an toàn xác định trích từ QĐ 5481/QĐ-BYT, rồi viết bản tóm tắt tiếng Việt 8 mục. Mọi câu đều trích nguồn bấm vào được. Bác sĩ xem, sửa, duyệt; bản duyệt được lưu và xuất được thành FHIR Composition.

**Không phải:** công cụ chẩn đoán, công cụ kê đơn, công cụ triage cấp cứu, ứng dụng cho bệnh nhân.

### Nguyên tắc sản phẩm (thứ tự ưu tiên khi phải đánh đổi)
1. **Xác định trước, LLM sau.** Dòng thời gian, thuốc, xét nghiệm, cờ và danh sách sự kiện đều do code sinh. LLM chỉ viết lời.
2. **Không có nguồn thì không hiển thị như sự thật.**
3. **Cờ do quy tắc; quy tắc do văn bản hoặc nguồn hệ thống được ghi rõ.** Không có nguồn thì không có quy tắc. Mỗi quy tắc phải ghi nguồn thuộc một trong ba loại: (a) văn bản chuyên môn (QĐ 5481), (b) nguyên tắc an toàn thuốc / chất lượng dữ liệu của hệ thống, (c) ý kiến chuyên gia ghi danh ("Ý kiến chuyên gia: BS. …, ngày …"). Loại (b) và (c) phải hiện rõ loại nguồn trên panel, không được trình bày như trích dẫn văn bản.
4. **Bác sĩ quyết định.** Chỉ bản đã duyệt được coi là một phần hồ sơ.
5. **"Không có dữ liệu" là câu trả lời hợp lệ và bắt buộc.**

### Mục tiêu và chỉ số
| ID | Mục tiêu | Chỉ số |
|---|---|---|
| G1 | Bác sĩ nắm 5 điểm chính trong 10 giây đọc và kiểm chứng được từng điểm | A4 = 100%; thời gian đọc (C3) |
| G2 | Giảm bỏ sót thông tin bắt buộc | A2 ≤ 10% trên fact bắt buộc |
| G3 | Phát hiện đủ tình huống an toàn suy ra được từ hồ sơ, với số cờ đủ ít | B1 = 100%; B2 ≥ 90%; B3 trung bình ≤ 3, tối đa 7 nhóm hiển thị |
| G4 | Bác sĩ giữ toàn quyền quyết định | Không có đường lưu bản chưa duyệt (test tự động) |
| G5 | Sẵn sàng cho triển khai thật tại Việt Nam | FHIR R4 bám VN Core; checklist pháp lý (docs/21) trả lời được trong 60 giây |

### Mốc phát hành
| Mốc | Ngày | Phạm vi | Điều kiện chấp nhận |
|---|---|---|---|
| Sprint 1 | 18/11 | Tóm tắt thô 1 bệnh nhân; khung mọi lớp | Mỗi câu có nguồn; 4 quy tắc đầu (R-MED-01, 02, R-LAB-01, 02); RBAC |
| MVP | 25/11 | Toàn bộ P0 trên 10 bệnh nhân | A4 = 100%, B1 = 100%, p95 ≤ 60 s; luồng duyệt và nhật ký |
| Beta | 2/12 | 30 bệnh nhân, P1, ablation, Composition | Tag data-v1; báo cáo ablation |
| Release candidate | 9/12 | Deploy công khai, nhóm C, báo cáo cuối | URL sống; tập dượt 2 lần |
| Demo Day | 13/12 | Freeze từ 10/12 | Tag demo-day |

---

## 2. User personas
| Vai trò | Mô tả | Mục tiêu chính | Quyền hạn |
|---|---|---|---|
| **BS. Lan** — Bác sĩ Nội tiết *(persona chính)* | Khám ngoại trú ĐTĐ típ 2 tại bệnh viện tỉnh đã có bệnh án điện tử. Bệnh nhân tái khám 1–3 tháng một lần, nhiều người mang kết quả xét nghiệm từ nơi khác. Số bệnh nhân mỗi buổi: thu qua phỏng vấn | Thấy ngay thuốc đang dùng, HbA1c và eGFR gần nhất, xu hướng, việc còn thiếu. Không muốn bị máy "dạy", bị cảnh báo vặt, phải bấm thêm nhiều bước | `doctor`: xem bệnh nhân được gán; tạo, xem, sửa, duyệt tóm tắt; đánh dấu cờ đã xem xét |
| **BS. Hùng** — Bác sĩ Nội tổng quát *(persona phụ)* | Khám đa bệnh tại phòng khám ngoại trú; ĐTĐ típ 2 là một trong nhiều bệnh phải quản lý; ít thời gian hơn và ít quen thuật ngữ nội tiết chuyên sâu | Cần 5 dòng chính và cờ an toàn đúng; ít quan tâm biểu đồ chi tiết | như `doctor` |
| **Anh Minh** — Quản trị hệ thống *(persona phụ)* | IT bệnh viện, tạo tài khoản và gán bệnh nhân cho bác sĩ | Biết ai đã xem hoặc duyệt hồ sơ nào, lúc nào. Không muốn phải đọc nội dung hồ sơ để tra nhật ký | `admin`: mọi quyền của doctor trên mọi bệnh nhân; gán/bỏ gán; tra nhật ký |
| Đội dự án, giám khảo | Người nạp dữ liệu và chạy đánh giá | Có số đo chất lượng lặp lại được | Nạp dữ liệu và chạy benchmark bằng lệnh, không qua giao diện |

Persona là giả định thiết kế, sẽ hiệu chỉnh theo kết quả phỏng vấn (docs/06). Bệnh nhân là chủ thể dữ liệu, không phải người dùng.

---

## 3. User stories
Định dạng: Là <vai trò>, tôi muốn <hành động>, để <kết quả>. Ưu tiên: P0 (bắt buộc cho MVP) / P1 (làm sau khi P0 xanh) / P2 (roadmap).

| ID | Là | Tôi muốn | Để | Ưu tiên |
|---|---|---|---|---|
| US-01 | bác sĩ | chỉ thấy bệnh nhân được gán cho mình | không truy cập hồ sơ ngoài trách nhiệm | P0 |
| US-02 | bác sĩ | tạo bản tóm tắt bằng một nút và thấy tiến trình | biết hệ thống đang làm gì trong lúc chờ | P0 |
| US-03 | bác sĩ | đọc 5 dòng chính ở đầu trang | nắm bệnh nhân trong 10 giây | P0 |
| US-04 | bác sĩ | bấm vào bất kỳ câu nào để thấy dữ liệu gốc | tự kiểm chứng thay vì tin máy | P0 |
| US-05 | bác sĩ | thấy thuốc đang dùng theo hoạt chất kèm biệt dược | phát hiện trùng thuốc khác tên | P0 |
| US-06 | bác sĩ | thấy cờ an toàn kèm mã quy tắc, loại nguồn và trích dẫn | biết vì sao có cờ và tự quyết định | P0 |
| US-07 | bác sĩ | thấy rõ dữ liệu nào thiếu hoặc mâu thuẫn | chỉ định bổ sung hoặc hỏi lại bệnh nhân | P0 |
| US-08 | bác sĩ | sửa câu trực tiếp và duyệt | bản lưu là của tôi, không phải của máy | P0 |
| US-09 | quản trị | gán bệnh nhân và tra nhật ký | đúng người đúng hồ sơ, truy vết được | P0 |
| US-10 | đội dự án | chạy benchmark bằng một lệnh | có số đo chất lượng lặp lại được | P0 |
| US-11 | bác sĩ | xem biểu đồ xu hướng có đường mục tiêu | thấy đáp ứng điều trị theo thời gian | P1 |
| US-12 | bác sĩ | xem dòng thời gian lọc theo loại và nguồn | tìm nhanh một sự kiện | P1 |
| US-13 | bác sĩ | xuất bản duyệt thành FHIR Composition hoặc PDF | lưu vào hồ sơ hoặc bàn giao | P1 |
| US-14 | bác sĩ | thấy mâu thuẫn dữ liệu giữa các nguồn cạnh nhau | không dựa nhầm vào một con số lệch | P1 |
| US-15 | bác sĩ | thấy tương tác thuốc mức "major" có nguồn DDInter | không bỏ sót tương tác nguy hiểm | P1 |
| US-16 | quản trị | chạy mô hình ngôn ngữ nội bộ thay cho cloud | tránh chuyển dữ liệu ra nước ngoài (Đ18 NĐ 356/2025) | P1 |
| US-17 | đội dự án | chấm bằng PDSQI-9 tiếng Việt và chạy ablation 3 cấu hình | có bằng chứng giá trị với bác sĩ, kèm khoảng tin cậy | P1 |

### P2 — chỉ nằm trong roadmap
| ID | Là | Tôi muốn | Lý do để sau |
|---|---|---|---|
| US-18 | bác sĩ | hệ thống ghi nhớ theo bệnh nhân | Cần dữ liệu sử dụng thật; rủi ro trôi lệch |
| US-19 | bác sĩ | hỏi đáp tự do trên hồ sơ | Mở rộng bề mặt lỗi; cần lớp bảo vệ khác |
| US-20 | bác sĩ | dùng cho bệnh mới: THA, COPD, suy tim | Mỗi bệnh cần một bộ quy tắc có nguồn và bộ kịch bản riêng |
| US-21 | bác sĩ | đọc cả kết quả giấy, ảnh (OCR) | Pipeline khác hẳn |
| US-22 | quản trị | tích hợp HIS thật qua FHIR API, SMART on FHIR, DPIA thật | Cần đối tác bệnh viện; khung NĐ 102/2025 về dữ liệu y tế |
| US-23 | bệnh nhân | có đầu ra dành cho mình | Pháp lý (Luật 91/2025 Điều 26) và an toàn |

Owner và tiêu chí chấp nhận của từng yêu cầu chức năng P0/P1: xem Phụ lục A.

---

## 4. Luồng chính (happy path)
1. Bác sĩ mở web và đăng nhập. Mọi trang sau đăng nhập có banner "DỮ LIỆU MÔ PHỎNG" kèm lời nhắc bác sĩ rà soát, và nhãn "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" ở vùng nội dung AI (minh bạch theo Luật AI 134/2025).
2. Hệ thống hiện danh sách bệnh nhân được gán. Bác sĩ chọn bệnh nhân ĐTĐ típ 2 sắp tái khám.
3. Bác sĩ bấm "Tạo bản tóm tắt". Hệ thống chạy 9 bước (đọc hồ sơ → dòng thời gian → chuẩn hóa thuốc → xét nghiệm → quy tắc → sự kiện → LLM soạn → kiểm tra → lưu nháp) và hiện tiến trình bằng tiếng Việt. Mục tiêu p95 ≤ 60 giây; timeout cứng 90 giây.
4. Bác sĩ đọc 5 dòng chính ở đầu trang (mục tiêu dưới 10 giây). Khi cần, bấm vào một câu để mở panel nguồn: tài nguyên gốc, cơ sở, ngày, ghi chú được tô sáng đúng đoạn.
5. Bác sĩ mở tab Cờ, xem từng nhóm cờ (mã quy tắc, loại nguồn, trích dẫn QĐ 5481, dữ liệu kích hoạt) và bấm "Đã xem xét".
6. Bác sĩ xem mục "Cần xác minh / thiếu" và các câu "chưa xác minh được", sửa câu nếu cần, rồi bấm "Duyệt" (chỉ bật sau khi đã mở tab Cờ và đã đánh dấu mọi cờ Đỏ). Bản duyệt bị khóa và được ghi nhật ký.
7. (P1) Bác sĩ xuất FHIR Composition hoặc PDF có mã bản duyệt và mã QR.
8. Bác sĩ khám bệnh nhân như thường lệ. Hệ thống không thay đổi bước khám, kê đơn, ghi chú.

**Kết quả:** một bản tóm tắt đã duyệt, mỗi câu có ít nhất một nguồn bấm được, kèm cờ an toàn và danh sách dữ liệu còn thiếu.

---

## 5. Human-in-the-loop
Hệ thống không gửi email, không thanh toán, không xóa dữ liệu hồ sơ, không ghi ngược vào HIS và không kê đơn. Các hành động còn lại có rủi ro đều cần người dùng xác nhận.

| Hành động rủi ro | Xác nhận ở bước nào | Người dùng thấy gì trước khi bấm |
|---|---|---|
| **Duyệt bản tóm tắt** (ghi và khóa bản) | Bác sĩ bấm "Duyệt"; chỉ bấm được sau khi đã mở tab Cờ **và đã đánh dấu "Đã xem xét" mọi cờ Đỏ**. Sau duyệt, sửa tiếp phải tạo bản mới (API trả 409) | Toàn văn bản nháp gồm phần bác sĩ vừa sửa; từng câu kèm nguồn; danh sách cờ; mục "cần xác minh / thiếu"; các câu "chưa xác minh được". Hệ thống lưu chênh lệch so với bản AI |
| **Đánh dấu cờ "Đã xem xét"** | Bác sĩ bấm cho từng cờ; lý do là tùy chọn. Cờ không bao giờ bị xóa. Sau khi bản đã duyệt thì không ack thêm được (bản khóa toàn phần) | Mã quy tắc, loại nguồn, câu diễn giải (luôn kết thúc "— cần bác sĩ xem xét."), trích dẫn QĐ 5481, dữ liệu kích hoạt |
| **Xuất FHIR Composition / PDF** (P1) | Chỉ xuất được từ bản đã duyệt | Bản đã duyệt, mã bản duyệt, banner "DỮ LIỆU MÔ PHỎNG" |
| **Gán / bỏ gán bệnh nhân** (admin) | Admin xác nhận trước khi lưu; mọi thay đổi được ghi nhật ký | Bác sĩ và bệnh nhân bị ảnh hưởng |
| **Tạo bản tóm tắt mới** (chạy LLM) | Bác sĩ bấm "Tạo bản tóm tắt" hoặc "Tạo lại". Không ghi đè bản đã duyệt | Tiến trình từng bước; nếu lỗi thì thấy "Không tạo được tóm tắt" |

### Vòng đời bản tóm tắt
| Trạng thái | Ý nghĩa | Chuyển tiếp |
|---|---|---|
| Đang tạo | Pipeline đang chạy; chỉ hiện tiến trình | → Nháp khi kiểm tra xong; → Thất bại khi lỗi hoặc quá 90 giây |
| Nháp | Sửa được, đánh dấu cờ được | → Đã duyệt khi bấm Duyệt (sau khi mở tab Cờ và ack mọi cờ Đỏ) |
| Đã duyệt | Khóa hoàn toàn; xuất được | Sửa tiếp hoặc ack thêm phải tạo bản mới |
| Thất bại | Không lưu nội dung dở; hiện "Không tạo được tóm tắt" | Tạo lại |

**Sự kiện nhật ký:** login, view_patient, generate_summary, view_summary, edit_summary, approve_summary, ack_flag, denied. Mỗi bản ghi có người, thời điểm, bệnh nhân, bản tóm tắt; không chứa nội dung hồ sơ; chỉ cho phép INSERT.

---

## 6. Xử lý lỗi & giới hạn
| Tình huống | Hệ thống xử lý | Người dùng thấy |
|---|---|---|
| Pipeline lỗi hoặc quá 90 giây | Chuyển "Thất bại"; không lưu nội dung dở | "Không tạo được tóm tắt"; nút Tạo lại |
| LLM trả **sai định dạng toàn cục** (không parse được thành 8 mục) | Thử lại **1 lần** với cùng prompt; vẫn lỗi → lỗi pipeline như trên; không đưa nội dung vào bản nháp | Như trên |
| **Từng câu** thiếu nguồn hoặc số không khớp | Tách sang mục "Chưa xác minh được", không xóa (khác với lỗi định dạng toàn cục ở trên) | Câu nền xám, không có chip nguồn; mục ẩn khi rỗng |
| Hết quota hoặc dịch vụ LLM không phản hồi | Thất bại, không lưu nháp. Bản đã duyệt trước đó vẫn xem được | Thông báo dịch vụ AI tạm thời không dùng được; thử lại sau |
| Hồ sơ thiếu dữ liệu | Ghi "không có dữ liệu" đúng ô, đúng mục; không suy đoán | Nội dung theo Phụ lục C |
| Bác sĩ mở bệnh nhân không được gán | Trả 403 và ghi nhật ký "denied" | Thông báo không có quyền |
| Sửa bản đã duyệt | Trả 409 | Yêu cầu tạo bản mới |

### Hệ thống KHÔNG làm được gì — nói thẳng với người dùng
- Không chẩn đoán, không kê đơn, không đề xuất thuốc, không phân loại khẩn cấp (triage). Mọi câu cờ kết thúc bằng "— cần bác sĩ xem xét."
- **Cố ý không có** ngưỡng tăng huyết áp cấp cứu, kali máu, HbA1c "báo động", ngưỡng eGFR cho SGLT2i. Đã rà toàn văn QĐ 5481: các ngưỡng này không được quy định cho ngoại trú (ngưỡng cấp cứu chỉ nằm ở Phần 7 — bối cảnh nội trú), và mục 4.2(b) yêu cầu tham khảo thông tin kê toa từng hoạt chất cho SGLT2i. Nếu bác sĩ cố vấn bổ sung, nguồn ghi là "Ý kiến chuyên gia: BS. …, ngày …".
- Không hỏi đáp tự do trên hồ sơ, không ghi nhớ theo bệnh nhân.
- Chỉ ĐTĐ típ 2 ngoại trú (có thể kèm THA, rối loạn lipid, CKD giai đoạn 1–3). Không áp dụng cho típ 1, thai kỳ, nhi khoa, nội trú, cấp cứu.
- Chỉ dữ liệu mô phỏng; không có chức năng tải hồ sơ lên; không tích hợp HIS thật.
- Giới hạn kỹ thuật: p95 ≤ 60 giây mỗi bản (timeout cứng 90 giây); tương tác giao diện dưới 300 ms; ≥ 2 bản chạy đồng thời trên máy 2 vCPU/4 GB; web desktop ≥ 1280 px, không tối ưu cho di động.

---

## 7. Dữ liệu
**Nguồn và cách lấy.** Không có bộ hồ sơ ngoại trú ĐTĐ típ 2 tiếng Việt, theo dòng thời gian, dạng FHIR công khai. Đội tự sinh 30 bệnh nhân (mục tiêu 50) theo mô hình 3 lớp (ADR-12, duyệt 12/11/2026): sinh lý → hành vi bác sĩ theo QĐ 5481 có lệch chủ ý → ghi chép có nhiễu. Ghi chú tiếng Việt sinh bằng template xác định, không dùng LLM. Sinh lại giống hệt theo seed.

**Định dạng:** FHIR R4 bám VN Core IG 0.10.0; mã ICD-10, LOINC, ATC; 6 kịch bản, 6–24 tháng, 4–12 lần khám, ≥ 2 cơ sở nguồn mỗi bệnh nhân.

**Phân bổ (chốt):** 10 bệnh nhân hồ sơ sạch / 5 đang chỉnh thuốc / 15 có vấn đề.

**Ràng buộc:** chỉ dữ liệu mô phỏng (Luật 91/2025/QH15 xếp dữ liệu sức khỏe là dữ liệu cá nhân nhạy cảm), không có chức năng tải hồ sơ lên.

### Nguồn tham chiếu ngoài
| Nguồn | Dùng cho | Ưu tiên |
|---|---|---|
| QĐ 5481/QĐ-BYT (30/12/2020), sửa đổi bởi QĐ 1353/QĐ-BYT (23/02/2021) | Ngưỡng, chống chỉ định, tần suất xét nghiệm, mục tiêu điều trị | P0 |
| drugbank.vn, cổng Cục Quản lý Dược | Khoảng 40 biệt dược ↔ hoạt chất ↔ số đăng ký | P0 |
| LOINC, ICD-10 VN, WHO ATC | Bảng mã nội bộ (docs/18) | P0 |
| VN Core FHIR IG 0.10.0 | Tên trường, profile; validator trên 5 bệnh nhân | P1 |
| DDInter 2.0 (CC BY-NC-SA 4.0, truy cập mở — đã xác nhận) | Tương tác thuốc mức major | P1 |
| Synthea (Apache-2.0) | Chỉ tham khảo logic tiến triển bệnh | Tham khảo |
| TT 13/2025/TT-BYT, TT 32/2023/TT-BYT Chương X | Đối chiếu Composition với khung bệnh án điện tử (docs/26) | P1 |

Không dùng MIMIC-IV (nội trú, tiếng Anh, DUA cấm gửi lên API LLM bên thứ ba), ACI-Bench, MTS-Dialog, VietMed-Sum (dữ liệu hội thoại, khác tác vụ).

### Thành phần bộ dữ liệu
| Thành phần | Yêu cầu |
|---|---|
| Bundle mỗi bệnh nhân | Patient, ≥ 2 Organization, 4–12 Encounter, Condition (ICD-10), Observation (LOINC + UCUM), MedicationRequest (ATC + biệt dược + số đăng ký), AllergyIntolerance, DocumentReference (ghi chú tiếng Việt viết tắt) |
| Gold | Fact có nguồn và cờ bắt buộc, tình huống kỳ vọng, bẫy thiếu dữ liệu, cờ bị cấm kích |
| Truth | Sự thật ẩn trước khi áp nhiễu; chỉ để phân tích, không đưa cho agent |
| Manifest, báo cáo tính thực, Data Card | Seed, sha256, kiểm tra bất biến sinh lý, phân bố, giới hạn, giấy phép |
| Tình huống cài sẵn | ≥ 40 ca phủ các quy tắc P0 (mỗi quy tắc ≥ 2 dương + 1 biên); 10 bẫy bịa đặt |

---

## 8. Tech stack (chốt)
| Thành phần | Lựa chọn | Ghi chú |
|---|---|---|
| Backend | Python 3.12 + FastAPI | fhir.resources để đọc Bundle; pydantic để ràng buộc schema đầu ra LLM |
| Frontend | React + Vite + TypeScript | Web desktop ≥ 1280 px |
| Job | BackgroundTasks + bảng `jobs` trong DB (không broker riêng) | Phù hợp 2 vCPU/4 GB, ≤ 2 job đồng thời |
| Cơ sở dữ liệu | PostgreSQL 16 | JSONB cho Bundle; bảng audit chỉ cho INSERT |
| LLM provider | LLM cloud (Anthropic) cho demo với dữ liệu mô phỏng | Hoán đổi được qua interface `LLMClient`. Adapter OpenAI-compatible chạy model nội bộ (P1), vì Đ18 NĐ 356/2025 coi xử lý trên cloud nước ngoài là chuyển dữ liệu xuyên biên giới |
| Vector store | Không dùng | Không hỏi đáp tự do, không vector search |
| Chuẩn dữ liệu | FHIR R4 bám VN Core IG 0.10.0; ICD-10, LOINC, ATC | Xuất FHIR Composition + Bundle document (P1), hỗ trợ cả XML |
| Xác thực, phân quyền | JWT trong cookie httpOnly; mật khẩu bcrypt; RBAC doctor/admin | Bí mật chỉ trong biến môi trường; HTTPS |
| Nơi deploy | Docker Compose trên 1 máy ảo (2 vCPU/4 GB), một lệnh; Caddy tự cấp HTTPS | Cần máy ảo và tên miền trước 1/12 |
| Đánh giá | `make benchmark` xuất báo cáo markdown và JSON | Lưu phiên bản prompt, bộ quy tắc, mã mô hình, mã băm hồ sơ |

---

## 9. Quyết định đã chốt (thay mục "Known Ambiguities" của 1.1)
| # | Vấn đề của 1.1 | Quyết định |
|---|---|---|
| 1 | Công cụ thực thi plan | Theo quyết định của đội tại kickoff Sprint 1; không ảnh hưởng kiến trúc hay tài liệu |
| 2 | Có bác sĩ cố vấn không | Giả định **không có**; nhóm C do mentor và 2 thành viên được huấn luyện chấm, ghi rõ hạn chế (docs/06 §D). Nếu có bác sĩ thì nâng cấp, không đổi kế hoạch |
| 3 | Ngân sách LLM | **75 USD** cho phát triển và ablation; ablation giới hạn 3 cấu hình × 30 BN × 3 lần |
| 4 | Máy ảo và tên miền | Hạn cứng **1/12**; cấu hình 2 vCPU/4 GB; Caddy tự cấp HTTPS |
| 5 | Định dạng nộp bài | **Repo + báo cáo PDF + video + URL demo**; xác nhận lại với BTC trước 25/11 |
| 6 | Tỷ lệ nhóm bệnh nhân | **10 sạch / 5 đang chỉnh thuốc / 15 có vấn đề** |
| 7 | Gắn tình huống vào kịch bản | Ma trận 6 kịch bản A–F, ~40 tình huống (docs/15 §3) |
| 8 | Bảng xét nghiệm CSV | **Bỏ**; dùng phiếu kết quả dạng văn bản trong DocumentReference cho 5 BN kịch bản D/F để test trích đoạn |
| 9 | Mã LOINC eGFR và LDL | eGFR CKD-EPI 2021: **98979-8**; nguồn cũ CKD-EPI 2009: 62238-1; LDL-C đo: 2089-1; LDL-C tính: 13457-7 |
| 10 | Số liệu buổi khám, thứ tự 8 mục | Thu qua phỏng vấn (docs/06); thứ tự 8 mục giữ theo Phụ lục C đến khi có dữ liệu |
| 11 | Số lần thử lại khi LLM lỗi | **1 lần** cho lỗi định dạng toàn cục; lỗi quota/không phản hồi không thử lại |
| 12 | Timeout vs p95 | **Timeout cứng 90 giây**; p95 ≤ 60 giây là chỉ tiêu chất lượng đo trên benchmark |
| 13 | HIS mục tiêu | Chỉ ảnh hưởng P2; theo khung NĐ 102/2025 khi có đối tác bệnh viện |
| 14 | Điều kiện bấm Duyệt | Phải đã mở tab Cờ **và** ack mọi cờ Đỏ |
| 15 | Nguồn của 17 quy tắc | 11 quy tắc có ngưỡng từ QĐ 5481 (đã đối chiếu toàn văn), 6 quy tắc nguồn hệ thống; Nguyên tắc 3 đã mở rộng để ghi loại nguồn (mục 1) |

---

## Phụ lục A. Yêu cầu chức năng và tiêu chí chấp nhận
Owner: D = dữ liệu & quy tắc, B = backend & agent, F = frontend, E = benchmark & PM.

### P0 (bắt buộc cho MVP)
| ID | Chức năng | Tiêu chí chấp nhận | Owner |
|---|---|---|---|
| P0-1 | Đăng nhập bác sĩ/admin, phân quyền theo bệnh nhân được gán | Bác sĩ A không mở được bệnh nhân của bác sĩ B: trả 403 và vẫn ghi nhật ký "denied" (test tự động). Admin gán được bệnh nhân | B |
| P0-2 | Bộ dữ liệu 30 bệnh nhân mô phỏng tiếng Việt, FHIR R4 bám VN Core, ≥ 2 nguồn mỗi bệnh nhân | Sinh lại giống hệt theo seed; gold cho 30 bệnh nhân; mọi tình huống cài sẵn kích đúng quy tắc; báo cáo bất biến sinh lý PASS | D |
| P0-3 | Pipeline 9 bước | 30/30 bệnh nhân chạy không lỗi; p95 ≤ 60 giây; timeout cứng 90 giây | B |
| P0-4 | Tóm tắt 8 mục thứ tự cố định (Phụ lục C) | Mỗi câu có ≥ 1 nguồn; câu không nguồn bị chặn (A4 = 100%) | B |
| P0-5 | Panel nguồn: bấm câu → tài nguyên gốc, cơ sở, ngày; ghi chú được tô sáng đúng đoạn | 100% câu bấm được; hiển thị < 300 ms sau khi có cache | F |
| P0-6 | Cờ an toàn xác định (17 quy tắc, docs/13) | B1 = 100% trên tình huống cài sẵn; mỗi cờ có mã, loại nguồn, trích dẫn, dữ liệu kích hoạt; gộp nhóm đúng §0.8 | D+B |
| P0-7 | Bác sĩ sửa, duyệt; lưu chênh lệch; khóa sau duyệt | Không có đường lưu bản chưa duyệt; sửa sau duyệt trả 409; Duyệt bị chặn khi còn cờ Đỏ chưa ack | F+B |
| P0-8 | Banner "DỮ LIỆU MÔ PHỎNG" + nhãn minh bạch AI | Hiện trên mọi trang sau đăng nhập | F |
| P0-9 | Nhật ký vận hành | Ghi đăng nhập, xem, tạo, sửa, duyệt, đánh dấu cờ, bị từ chối; màn hình admin lọc được; không chứa nội dung hồ sơ; không UPDATE/DELETE được | B |
| P0-10 | Benchmark nhóm A, B, D | `make benchmark` xuất báo cáo markdown và JSON kèm commit, phiên bản prompt, bộ quy tắc, mã mô hình, mã băm dữ liệu | E |
| P0-11 | Triển khai một lệnh | Docker Compose trên 1 máy ảo; URL công khai có HTTPS | B |

### P1 (làm sau khi P0 xanh)
| ID | Chức năng | Tiêu chí | Owner |
|---|---|---|---|
| P1-1 | Biểu đồ xu hướng HbA1c, eGFR, HA, cân nặng, LDL với đường mục tiêu (Bảng 4, 5) | Di chuột hiện giá trị, ngày, cơ sở nguồn | F |
| P1-2 | Dòng thời gian tương tác, lọc theo loại và nguồn | Bấm sự kiện → panel nguồn | F |
| P1-3 | Hiển thị mâu thuẫn dữ liệu giữa nguồn cạnh nhau | Recall 100% trên 5 bệnh nhân kịch bản F | D+B |
| P1-4 | Tương tác thuốc mức "major" từ DDInter (~25 hoạt chất) | Cờ có nguồn DDInter; giấy phép ghi trong LICENSES | D |
| P1-5 | Xuất FHIR Composition + Bundle document, mỗi section trỏ về nguồn | Chạy được FHIR validator; ghi số lỗi; xuất được XML | B |
| P1-6 | Xuất PDF bản đã duyệt có mã bản duyệt và mã QR | In được, đủ 8 mục | F |
| P1-7 | Benchmark nhóm C: PDSQI-9 tiếng Việt và error-detection test | ≥ 2 người chấm; báo cáo ICC | E |
| P1-8 | Ablation 3 cấu hình | Bảng so sánh có khoảng tin cậy 95% | E |
| P1-9 | Adapter LLM chạy model nội bộ (OpenAI-compatible) | Chạy được 1 bệnh nhân | B |

---

## Phụ lục B. 17 quy tắc an toàn P0 (bảng tóm tắt)
Đặc tả đầy đủ, định nghĩa vận hành và test: **docs/13**. Ánh xạ trích dẫn: **docs/14**.

Nhãn: 🔴 Đỏ = an toàn thuốc, chống chỉ định (luôn mở, xếp đầu); 🟡 Vàng = cần bác sĩ xem xét (luôn mở); ⚪ Xám = thiếu hoặc mâu thuẫn dữ liệu (thu gọn mặc định). Tối đa 7 nhóm hiển thị, có nút "xem thêm".

| Mã | Điều kiện | Nhãn | Nguồn |
|---|---|---|---|
| R-MED-01 | Metformin đang dùng ∧ eGFR < 30 | 🔴 | QĐ 5481, Phụ lục 01 mục 2 |
| R-MED-02 | Metformin đang dùng ∧ 30 ≤ eGFR ≤ 45 | 🟡 | Phụ lục 01 mục 2 |
| R-MED-03 | Glibenclamid đang dùng ∧ (tuổi ≥ 65 ∨ eGFR < 60) | 🔴 | Phụ lục 01 mục 1(a); định nghĩa người cao tuổi: mục 4.1(i) |
| R-MED-04 | TZD/pioglitazon đang dùng ∧ có suy tim (I50*) | 🔴 | Mục 4.1(g); Phụ lục 01 mục 4 |
| R-MED-05 | Thuốc đang dùng trùng chất gây dị ứng ở bất kỳ nguồn nào | 🔴 | Phụ lục 01 mục 1(a) + nguyên tắc an toàn thuốc của hệ thống |
| R-MED-06 | ≥ 2 đơn đang hiệu lực cùng hoạt chất, khác biệt dược | 🟡 | Mục 4.2(c) + chuẩn hóa thuốc |
| R-MED-07 | SU hoặc insulin ∧ hạ đường huyết trong 90 ngày ∧ tuổi ≥ 65 | 🟡 | Mục 4.2(j); Phần 6 mục 1(b) |
| R-LAB-01 | Glucose < 3,0 mmol/L trong 90 ngày | 🔴 | Phần 6 mục 2, Mức 2 |
| R-LAB-02 | Glucose từ 3,0 đến dưới 3,9 mmol/L trong 90 ngày | 🟡 | Phần 6 mục 2, Mức 1 |
| R-TREND-01 | HbA1c tăng 2 kỳ liên tiếp ∧ trên mục tiêu cá thể | 🟡 | Bảng 4, 5 (ngưỡng); xu hướng là suy diễn hệ thống |
| R-GAP-01 | Không có HbA1c > 183 ngày | ⚪ | Bảng 2 (yêu cầu mỗi 3 tháng; hệ thống dung sai 2 kỳ) |
| R-GAP-02 | Không có creatinin/eGFR > 365 ngày (183 nếu eGFR < 60 hoặc UACR > 30) | ⚪ | Bảng 2; Phần 8 mục 1.2(a),(b) |
| R-GAP-03 | Không có UACR > 365 ngày (183 nếu nhóm nguy cơ) | ⚪ | Phần 8 mục 1.2(a),(b) |
| R-GAP-04 | Không có bộ lipid > 365 ngày | ⚪ | Mục 6.2.1 |
| R-DATA-01 | Cùng chỉ số, 2 nguồn, cách ≤ 7 ngày, lệch > 20% (loại trừ glucose, HA) | ⚪ | Chất lượng dữ liệu (hệ thống) |
| R-DATA-02 | Glucose > 30 không đơn vị/ghi mmol/L, hoặc < 1,5 ghi mg/dL | ⚪ | Chất lượng dữ liệu (hệ thống) |
| R-DATA-03 | Dị ứng có ở nguồn này, không có ở nguồn kia ∧ nguồn kia từng kê thuốc sau đó | ⚪ | Chất lượng dữ liệu (hệ thống) |

**P1:** R-DDI-01 (cặp major trong DDInter), R-TARGET-01 (HA theo Bảng 4), R-TARGET-02 (LDL theo Bảng 4 và mục 6.2.2).

**Vì sao không làm "Red-Flag Triage" như đề gốc:** xem docs/13 §7.

---

## Phụ lục C. Đặc tả nội dung bản tóm tắt
| # | Mục | Nội dung bắt buộc | Khi không có dữ liệu |
|---|---|---|---|
| 1 | 5 dòng chính | Tuổi, giới, thời gian mắc, bệnh kèm; HbA1c gần nhất và xu hướng; eGFR gần nhất; thuốc hạ đường huyết chính; số cờ đỏ nếu có | Ghi "không có dữ liệu" cho dòng tương ứng |
| 2 | Vấn đề đang theo dõi | Condition đang hoạt động, mã ICD-10, ngày ghi nhận | "Không ghi nhận vấn đề khác" |
| 3 | Thuốc đang dùng | Hoạt chất (biệt dược), liều mỗi ngày, ngày kê, cơ sở kê; thuốc đã ngừng không nằm ở đây | "Không có đơn thuốc đang hiệu lực" |
| 4 | Dị ứng | Hợp từ mọi nguồn, ghi cơ sở ghi nhận | "Không ghi nhận dị ứng trong hồ sơ" |
| 5 | Xu hướng xét nghiệm | Bảng 4–6 kỳ: HbA1c, glucose đói, eGFR, creatinin, UACR, lipid; đơn vị đã chuẩn hóa | Ô trống ghi "không có dữ liệu" |
| 6 | Cờ an toàn | Diễn giải đúng payload cờ, kèm mã quy tắc và loại nguồn; chi tiết ở tab Cờ | "Không có cờ" |
| 7 | Cần xác minh / thiếu | Xét nghiệm định kỳ quá hạn theo QĐ 5481 Bảng 2 và Phần 8 mục 1.2; mâu thuẫn giữa nguồn | "Không phát hiện thiếu dữ liệu theo QĐ 5481". Mục luôn hiện |
| 8 | Timeline | Khám, xét nghiệm, bắt đầu và ngừng thuốc, dị ứng, ghi chú theo ngày | — |
| — | Chưa xác minh được | Câu LLM viết nhưng không qua bước kiểm tra; nền xám, không chip nguồn | Ẩn khi rỗng |

### Quy tắc viết
- Mỗi câu có ≥ 1 nguồn dạng `ResourceType/id`; với ghi chú là `DocumentReference/id#char=a-b`.
- **Mọi con số** phải có trong tài nguyên nguồn sau quy đổi đơn vị, **hoặc** suy ra được bằng phép tính xác định từ fact do code dựng (tuổi, thời gian mắc bệnh, số cờ, mức tăng/giảm giữa hai kỳ). Số suy ra ghi nguồn là các fact đầu vào. Hiển thị dấu phẩy thập phân, ngày dd/mm/yyyy.
- **Cụm cấm** (chỉ áp cho câu ở thì khuyến nghị do LLM sinh): "cấp cứu", "ngừng thuốc", "đổi thuốc", "nên dùng", "đề nghị", "chẩn đoán mới". Câu **tường thuật sự kiện có nguồn** không bị chặn (ví dụ dòng Timeline "ngừng metformin 01/02/2025" lấy từ `status = stopped`, hoặc trích ghi chú gốc có chữ "cấp cứu"). Định nghĩa kỹ thuật của bộ chặn: docs/10 §FR-05.
- Mục tiêu HbA1c cá thể 3 bậc theo docs/13 §0.7 (7,0% / 7,5% / 8,0%). Luôn ghi "mục tiêu mặc định theo QĐ 5481, bác sĩ có thể chỉnh".
- Thuật ngữ theo QĐ 5481 (docs/19); tên thuốc theo hoạt chất kèm biệt dược Việt Nam.

---

## Phụ lục D. Yêu cầu phi chức năng và benchmark
| Nhóm | Yêu cầu |
|---|---|
| Hiệu năng | p95 ≤ 60 giây mỗi bản; timeout cứng 90 giây; tương tác giao diện < 300 ms; ≥ 2 bản chạy đồng thời trên máy 2 vCPU/4 GB |
| An toàn | Không câu nào không nguồn hiển thị như sự thật; nội dung chỉ hiện sau khi kiểm tra xong; quá hạn trả thất bại rõ ràng |
| Bảo mật | HTTPS; JWT trong cookie httpOnly; mật khẩu bcrypt; không tải file lên; bí mật chỉ trong biến môi trường |
| Riêng tư | Chỉ dữ liệu mô phỏng; không ghi nội dung hồ sơ vào log (`make logcheck` kiểm tra tự động); LLM hoán đổi được |
| Tái tạo | Mỗi bản lưu phiên bản prompt, phiên bản quy tắc, mã mô hình, mã băm hồ sơ |
| Tin cậy | Quy tắc 100% xác định, có unit test dương/âm/biên; 3 lần chạy cùng đầu vào trùng ≥ 90% fact |
| Ngôn ngữ | Toàn bộ tiếng Việt; thuật ngữ QĐ 5481 |
| Tiếp cận | Tương phản WCAG AA; điều hướng bàn phím; không dùng màu làm kênh thông tin duy nhất |

| Nhóm | Chỉ số | Ngưỡng MVP |
|---|---|---|
| **A. Trung thực** | A1 câu không được nguồn hỗ trợ | ≤ 2% |
| | A2 bỏ sót fact bắt buộc | ≤ 10% |
| | A3 độ chính xác trích dẫn | ≥ 95% |
| | A4 câu có nguồn hợp lệ | 100% |
| | A5 bẫy bịa đặt được xử lý đúng | 100% |
| | A6 số liệu khớp nguồn | ≥ 99% |
| **B. Cờ** | B1 recall trên tình huống cài sẵn | 100% |
| | B2 precision | ≥ 90% |
| | B3 nhóm cờ Đỏ+Vàng mỗi bệnh nhân | Trung bình ≤ 3, hiển thị tối đa 7 |
| | B4 diễn giải cờ đúng payload | 100% |
| **C. Người chấm** | PDSQI-9 tiếng Việt, error-detection có/không panel nguồn, thời gian đọc, mức hại | Báo cáo; 0 lỗi mức nặng |
| **D. Kỹ thuật** | Độ trễ, chi phí, ổn định, bền với nhiễu | p95 ≤ 60 s; ổn định ≥ 90% |

**Ablation:** baseline (không ép nguồn, không quy tắc) · +ép nguồn · +quy tắc (= MVP), trên 30 bệnh nhân × 3 lần, bootstrap 1.000 lần cho khoảng tin cậy 95%. Kết quả không đạt sẽ được báo cáo thật.

---

## Phụ lục E. Rủi ro, giả định, thuật ngữ, nguồn

### Giả định (đổi được)
- Không có bác sĩ cố vấn; nhóm C do mentor và thành viên được huấn luyện chấm.
- LLM cloud (Anthropic) cho demo với dữ liệu mô phỏng; adapter nội bộ chỉ để chứng minh đường tuân thủ.
- Nộp bài gồm repo, báo cáo PDF, video, URL demo.

### Rủi ro
| Rủi ro | Khả năng | Giảm thiểu |
|---|---|---|
| Không có bác sĩ | Trung bình | Phương án dự phòng docs/06 §D; ghi hạn chế vào báo cáo |
| LLM viết tiếng Việt kém | Trung bình | Thử sớm trên 3 bệnh nhân trong tuần 1; glossary docs/19; đổi model qua adapter |
| Sinh dữ liệu chậm | Cao | Bắt đầu ngày 1; 30 bệnh nhân trước 50; giảm toán tử nhiễu nếu chậm |
| Phình phạm vi | Cao | Mọi thứ ngoài P0/P1 vào Icebox; rà phạm vi mỗi Thứ Hai |
| Bộ kiểm tra quá chặt làm tóm tắt rỗng | Trung bình | Câu bị chặn chuyển sang "chưa xác minh được", không xóa; theo dõi tỷ lệ câu bị chặn như một chỉ số |
| Gói VN Core 0.10.0 không tải được từ registry | Trung bình | Trỏ validator vào file gói tải về; nếu không được, chỉ bám tên trường |
| Cờ Xám nổ quá nhiều làm B3 vượt ngưỡng | Trung bình | Đã thu hẹp R-DATA-01/03 và đặt dung sai R-GAP-01; B3 chỉ đếm Đỏ+Vàng |

### Thuật ngữ
| | |
|---|---|
| Claim | Một câu trong tóm tắt kèm nguồn, sự kiện tham chiếu và số liệu |
| Fact | Sự kiện có cấu trúc do code dựng, có nguồn; đơn vị đo bỏ sót |
| Gold | Đáp án chấm benchmark sinh cùng dữ liệu |
| HITL | Bác sĩ tham gia vòng quyết định: sửa và duyệt |
| `as_of` | Ngày lượt tái khám sắp tới; mọi quy tắc tính tại ngày này |

### Nguồn chính
**Chuyên môn:** QĐ 5481/QĐ-BYT (30/12/2020) và QĐ 1353/QĐ-BYT (23/02/2021) — đã lưu bản gốc trong `refs/` kèm SHA-256.
**Pháp lý:** Luật Khám bệnh, chữa bệnh 15/2023/QH15 · Luật Bảo vệ dữ liệu cá nhân 91/2025/QH15 · Luật Dữ liệu 60/2024/QH15 · Luật Trí tuệ nhân tạo 134/2025/QH15 · NĐ 356/2025/NĐ-CP · NĐ 102/2025/NĐ-CP · TT 13/2025/TT-BYT · TT 32/2023/TT-BYT.
**Khoa học:** Stanford MedAgentBrief (JAMA Netw Open 2026) · PDSQI-9 (JAMIA 2025) · ALCE (arXiv 2305.14627) · DDInter 2.0 (NAR 2025) · VN Core FHIR IG v0.10.0.
