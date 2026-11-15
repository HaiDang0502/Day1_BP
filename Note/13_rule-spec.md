# docs/13 — Đặc tả 17 quy tắc an toàn P0 (Rule Spec v1.0)

Owner: D + B · Trạng thái: 🟡 đề xuất — cần đối chiếu số trang/mục trên PDF gốc QĐ 5481 (sửa đổi bởi QĐ 1353/2021) trước 18/11.
Mọi quy tắc đều: xác định (deterministic), tính tại ngày `as_of`, có unit test dương/âm/biên, sinh payload chuẩn. LLM chỉ diễn giải payload, không thêm bớt (B4 = 100%). Mọi câu cờ kết thúc "— cần bác sĩ xem xét."

## 0. Định nghĩa vận hành chung (đóng các lỗ hổng PRD 1.1)

### 0.1. "Đang dùng" (active medication)
MedicationRequest có `status = active` **và** khoảng hiệu lực đơn phủ `as_of`:
`authoredOn + dispenseRequest.expectedSupplyDuration ≥ as_of` (không có supplyDuration thì mặc định 90 ngày — chuẩn kê ngoại trú mạn tính, [QUYẾT ĐỊNH] đội xác nhận với bác sĩ cố vấn). Đơn `stopped/completed/cancelled` không tính. Hai đơn cùng hoạt chất từ 2 nguồn đều active → cả hai đều "đang dùng" (và kích R-MED-06).

### 0.2. "Gần nhất" + tuổi dữ liệu
"X gần nhất" = Observation X có `effectiveDateTime` lớn nhất ≤ `as_of`. **Tuổi tối đa để dùng trong quy tắc MED theo eGFR: 365 ngày**; nếu eGFR gần nhất cũ hơn 365 ngày → không kích R-MED-01/02, thay vào đó R-GAP-02 đã phủ. Không tự tính eGFR từ creatinin ở bản MVP (tránh hệ thống "tự suy ra chỉ số"); nếu hồ sơ chỉ có creatinin → coi như thiếu eGFR (R-GAP-02 xử lý). [QUYẾT ĐỊNH 19/11]

### 0.3. "Có hạ đường huyết trong 90 ngày" (cho R-MED-07)
Một trong: (a) Observation glucose < 3,9 mmol/L trong 90 ngày trước as_of; (b) Condition mã ICD-10 E16.0/E16.1/E16.2 ghi nhận trong 90 ngày. Ghi chú tự do KHÔNG dùng để kích quy tắc (quy tắc chỉ chạy trên dữ liệu cấu trúc).

### 0.4. Dedup và đếm cờ (cho B3)
- Một sự kiện dữ liệu kích nhiều quy tắc → mỗi quy tắc vẫn sinh cờ riêng (để recall đo được), nhưng UI **gộp theo sự kiện nguồn** (vd 1 kết quả glucose 2,8 kích R-LAB-01; nếu bệnh nhân ≥65 dùng SU thì thêm R-MED-07: hiển thị thành 1 nhóm "Hạ đường huyết 12/10" chứa 2 cờ).
- B3 đếm **số cờ Đỏ + Vàng sau khi gộp nhóm**; Xám không tính vào B3.
- Hiển thị mặc định tối đa 7 (Đỏ trước, Vàng sau, Xám thu gọn), còn lại sau nút "xem thêm".

### 0.5. Mục tiêu HbA1c cá thể (cho R-TREND-01)
Mặc định 7,0%; **≥ 65 tuổi VÀ ≥ 2 bệnh đồng mắc** → 8,0%. "Bệnh đồng mắc" đếm trên Condition active thuộc danh sách đóng: THA (I10–I15), bệnh mạch vành (I20–I25), suy tim (I50), đột quỵ/TIA (I60–I69), CKD (N18), COPD (J44). Luôn kèm câu "mục tiêu mặc định theo QĐ 5481, bác sĩ có thể chỉnh". [QUYẾT ĐỊNH: danh sách bệnh đếm — xác nhận với bác sĩ cố vấn 25/11]

### 0.6. Payload cờ chuẩn (mọi quy tắc)
```json
{
  "rule_id": "R-MED-01", "severity": "red|yellow|gray",
  "as_of": "2026-12-01",
  "trigger_data": [{"ref": "Observation/obs-123", "value": "eGFR 27 mL/min/1.73m2", "date": "2026-10-02", "org": "Organization/bv-a"}],
  "citation": {"doc": "QĐ 5481/QĐ-BYT (sđ QĐ 1353/2021)", "section": "<mục>", "page": "<trang PDF>", "quote_range": "<a-b>"},
  "status": "open|acknowledged", "ack": {"by": null, "at": null, "reason": null}
}
```

## 1. Bảng 17 quy tắc

Ký hiệu nguồn: [5481] = QĐ 5481 (cần điền trang/mục từ PDF gốc — 14_trich-luc); [HT] = nguồn hệ thống (nguyên tắc an toàn thuốc/chất lượng dữ liệu, đã sửa Nguyên tắc 3 theo PRD 1.2 delta #7); [XM] = ngưỡng đã xác minh chéo với chuẩn quốc tế 15/11/2026.

| Mã | Điều kiện hình thức (tại as_of) | Nhãn | Nguồn | Xác minh |
|---|---|---|---|---|
| R-MED-01 | ∃ thuốc đang dùng có ATC bắt đầu `A10BA` (metformin) ∧ eGFR_gần_nhất < 30 (tuổi ≤ 365 ngày) | Đỏ | [5481] Phụ lục 01 mục 2 | [XM] khớp FDA/ADA: CCĐ eGFR<30 |
| R-MED-02 | metformin đang dùng ∧ 30 ≤ eGFR_gần_nhất ≤ 45 | Vàng | [5481] Phụ lục 01 mục 2 | [XM] khớp FDA/ADA: 30–45 thận trọng, không khởi trị |
| R-MED-03 | ∃ thuốc đang dùng ATC `A10BB01` (glibenclamid) ∧ tuổi > 60 | Đỏ | [5481]/Danh mục thuốc — **cần số văn bản cụ thể**; nếu không tìm được → hạ nguồn thành [HT] + ý kiến chuyên gia | 🔴 chưa có văn bản |
| R-MED-04 | ∃ thuốc đang dùng ATC `A10BG` (TZD/pioglitazon) ∧ ∃ Condition active I50* | Đỏ | [5481] mục 4.1(g) | cần đối chiếu trang |
| R-MED-05 | ∃ thuốc đang dùng có hoạt chất ∈ tập hợp AllergyIntolerance (mọi nguồn, so theo hoạt chất chuẩn hóa, gồm nhóm chéo penicillin↔amoxicillin theo bảng nhóm đóng) | Đỏ | [HT] nguyên tắc an toàn thuốc | định nghĩa tập nhóm chéo trong 18_bang-ma |
| R-MED-06 | ∃ ≥ 2 MedicationRequest đang hiệu lực, cùng hoạt chất (ATC trùng), `medicationCodeableConcept` biệt dược khác nhau | Vàng | [HT] chuẩn hóa thuốc | — |
| R-MED-07 | (∃ thuốc đang dùng ATC `A10BB*` (SU) ∨ `A10A*` (insulin)) ∧ có_hạ_đường_huyết_90_ngày (§0.3) ∧ tuổi ≥ 65 | Vàng | [5481] mục 4.2(j); Phần 6 | ngưỡng tuổi 65 giữ theo PRD; khác R-MED-03 (60) vì hai căn cứ khác nhau — ghi chú trong UI tooltip |
| R-LAB-01 | ∃ Observation glucose (LOINC 2339-0/2345-7, sau quy đổi mmol/L) < 3,0 trong 90 ngày | Đỏ | [5481] Phần 6, mức 2 | [XM] khớp phân mức quốc tế: mức 2 < 3,0 mmol/L |
| R-LAB-02 | ∃ glucose ∈ [3,0 ; 3,9) trong 90 ngày ∧ ¬∃ giá trị < 3,0 cùng sự kiện | Vàng | [5481] Phần 6, mức 1 | [XM] mức 1: 3,0–<3,9 |
| R-TREND-01 | ≥ 3 giá trị HbA1c liên tiếp t1<t2<t3: v1<v2<v3 ∧ v3 > mục_tiêu(§0.5). ("Tăng ≥ 2 kỳ liên tiếp" = 2 lần tăng = 3 điểm dữ liệu.) | Vàng | [5481] Bảng 4,5 (ngưỡng mục tiêu); logic xu hướng = [HT] suy diễn hệ thống, ghi rõ trong citation | — |
| R-GAP-01 | ¬∃ HbA1c trong 183 ngày trước as_of (gồm cả trường hợp **chưa từng có**) | Xám | [5481] Bảng 2 (tần suất theo dõi) | cần đối chiếu tần suất 3–6 tháng trên PDF |
| R-GAP-02 | ¬∃ creatinin/eGFR trong 365 ngày (gồm chưa từng có) | Xám | [5481] Bảng 2 | — |
| R-GAP-03 | ¬∃ UACR trong 365 ngày (gồm chưa từng có) | Xám | [5481] Phần 6 biến chứng thận | — |
| R-GAP-04 | ¬∃ bộ lipid (LDL hoặc panel) trong 365 ngày | Xám | [5481] mục 6.2 | — |
| R-DATA-01 | ∃ 2 Observation cùng LOINC, 2 Organization khác nhau, |t1−t2| ≤ 7 ngày, chênh lệch tương đối > 20% **so với giá trị lớn hơn**, sau quy đổi đơn vị. **Miễn áp dụng cho glucose** (dao động sinh lý) — chỉ áp cho HbA1c, creatinin/eGFR, lipid, cân nặng | Xám | [HT] chất lượng dữ liệu | sửa so với PRD 1.1 để giữ B2 ≥ 90% |
| R-DATA-02 | glucose: (giá trị > 30 ∧ (không đơn vị ∨ đơn vị mmol/L)) → nghi mg/dL; **chiều ngược**: giá trị < 1,5 ∧ đơn vị mg/dL → nghi nhập nhầm. Cờ không quy đổi tự động, chỉ nêu nghi ngờ | Xám | [HT] chất lượng dữ liệu | thêm chiều ngược so với PRD 1.1 |
| R-DATA-03 | AllergyIntolerance xuất hiện ở nguồn A, vắng ở nguồn B **và** nguồn B có ≥ 1 MedicationRequest sau ngày ghi dị ứng (tức nguồn B từng kê thuốc mà không biết dị ứng) | Xám | [HT] chất lượng dữ liệu | thu hẹp so với PRD 1.1 (tránh kích đại trà, giữ B3) |

## 2. Unit test bắt buộc cho mỗi quy tắc
Mỗi quy tắc tối thiểu 5 test: (1) dương chuẩn; (2) âm chuẩn; (3) biên đúng ngưỡng (vd eGFR = 30 → R-MED-02, không R-MED-01; glucose = 3,0 → R-LAB-02? **không**: 3,0 thuộc [3,0;3,9) → Vàng; 2,99 → Đỏ); (4) dữ liệu quá tuổi (eGFR 400 ngày → không kích MED, kích GAP); (5) đơn hết hiệu lực → không kích. File test: `tests/rules/test_<rule_id>.py`, chạy trong CI.

## 3. Việc còn chờ
- [ ] Điền trang/mục chính xác từ PDF gốc QĐ 5481 + QĐ 1353 (→ 14_trich-luc) — **cần user cung cấp PDF**.
- [ ] R-MED-03: tìm văn bản nguồn (Danh mục thuốc thiết yếu / tiêu chí Beers không phải văn bản VN); nếu không có → chuyển nguồn [HT] + ý kiến chuyên gia, hoặc hạ xuống P1.
- [ ] Bác sĩ cố vấn xác nhận: mặc định 90 ngày hiệu lực đơn (§0.1), danh sách bệnh đồng mắc (§0.5), miễn glucose khỏi R-DATA-01.
