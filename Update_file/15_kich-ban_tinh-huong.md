# docs/15 — 6 kịch bản × 40 tình huống cài sẵn v2.0 ✅ ĐÃ CHỐT

## 1. Phân bổ 30 bệnh nhân (chốt)
**10 hồ sơ sạch / 5 đang chỉnh thuốc / 15 có vấn đề.**
Mỗi bệnh nhân: 6–24 tháng theo dõi, 4–12 Encounter, ≥ 2 Organization, ghi chú tiếng Việt sinh bằng template có nhiễu (viết tắt, lỗi chính tả, viết hoa tùy tiện), `as_of` cách lần khám cuối 30–90 ngày.

## 2. Sáu kịch bản
| KB | Mô tả | Số BN | Nhóm | Quy tắc mục tiêu |
|---|---|---|---|---|
| **A. Ổn định** | HbA1c đạt mục tiêu, xét nghiệm định kỳ đủ, 1 cơ sở chính + 1 cơ sở phụ | 6 | sạch | Không cờ Đỏ/Vàng (test âm toàn cục); tối đa 1 cờ Xám |
| **B. Mất kiểm soát dần** | HbA1c tăng đều qua 3–4 kỳ, trên mục tiêu | 5 | có vấn đề | R-TREND-01 |
| **C. Suy thận tiến triển** | eGFR giảm dần, đang dùng metformin | 5 | có vấn đề | R-MED-01 (2 BN), R-MED-02 (3 BN), R-GAP-02 nhánh 183 ngày (2 BN) |
| **D. Đa nguồn rối** | 3 cơ sở; trùng hoạt chất khác biệt dược; dị ứng ghi lệch nguồn; có suy tim | 5 | 3 có vấn đề + 2 đang chỉnh thuốc | R-MED-04, R-MED-05, R-MED-06, R-DATA-03 |
| **E. Hạ đường huyết + cao tuổi** | ≥ 65 tuổi, dùng SU/insulin, có glucose thấp | 5 | 2 có vấn đề + 3 đang chỉnh thuốc | R-LAB-01, R-LAB-02, R-MED-03, R-MED-07 |
| **F. Dữ liệu xấu** | Xét nghiệm quá hạn, số liệu mâu thuẫn giữa nguồn, nghi sai đơn vị | 4 | sạch về thuốc, xấu về dữ liệu | R-GAP-01…04, R-DATA-01, R-DATA-02 |

Tổng: 6 + 5 + 5 + 5 + 5 + 4 = **30**. Nhóm: sạch 10 (A6 + F4), đang chỉnh thuốc 5 (D2 + E3), có vấn đề 15 (B5 + C5 + D3 + E2).

## 3. Ma trận 40 tình huống cài sẵn (`data/scenarios.csv`)
Mỗi quyết tắc P0 có ≥ 2 ca dương + ≥ 1 ca biên/âm.

| # | situation_id | rule_id | expected | Kịch bản | Tham số |
|---|---|---|---|---|---|
| 1–2 | MED01-a/b | R-MED-01 | fire | C | eGFR 27 và 18, metformin active |
| 3 | MED01-edge | R-MED-01 | no_fire | C | eGFR đúng 30,0 (phải sang R-MED-02) |
| 4 | MED01-stale | R-MED-01 | no_fire | C | eGFR 25 nhưng cách 400 ngày (R-GAP-02 kích) |
| 5–7 | MED02-a/b/c | R-MED-02 | fire | C | eGFR 30,0 · 38 · 45,0 |
| 8 | MED02-edge | R-MED-02 | no_fire | C | eGFR 45,1 |
| 9–10 | MED03-a/b | R-MED-03 | fire | E | 68 tuổi glibenclamid · 54 tuổi glibenclamid eGFR 55 |
| 11 | MED03-edge | R-MED-03 | no_fire | E | 64 tuổi, eGFR 80 |
| 12 | MED03-other | R-MED-03 | no_fire | E | 70 tuổi dùng gliclazid (khác ATC) |
| 13–14 | MED04-a/b | R-MED-04 | fire | D | pioglitazon + I50.0 · pioglitazon + I50.9 |
| 15 | MED04-resolved | R-MED-04 | no_fire | D | pioglitazon + I50 đã `resolved` |
| 16–17 | MED05-a/b | R-MED-05 | fire | D | dị ứng metformin ở cơ sở B + đơn metformin ở A · dị ứng penicillin + amoxicillin (nhóm chéo) |
| 18 | MED05-nondrug | R-MED-05 | no_fire | D | dị ứng "hải sản" |
| 19–20 | MED06-a/b | R-MED-06 | fire | D | 2 biệt dược metformin · 2 biệt dược gliclazid |
| 21 | MED06-same | R-MED-06 | no_fire | D | 2 đơn cùng biệt dược (tái kê) |
| 22–23 | MED07-a/b | R-MED-07 | fire | E | 70 tuổi gliclazid glucose 3,5 · 78 tuổi insulin E16.1 |
| 24 | MED07-age | R-MED-07 | no_fire | E | 60 tuổi, cùng điều kiện còn lại |
| 25–26 | LAB01-a/b | R-LAB-01 | fire | E | glucose 2,9 · 2,2 |
| 27 | LAB01-edge | R-LAB-01 | no_fire | E | glucose đúng 3,0 |
| 28–29 | LAB02-a/b | R-LAB-02 | fire | E | glucose 3,0 · 3,8 |
| 30 | LAB02-edge | R-LAB-02 | no_fire | E | glucose 3,9 |
| 31–32 | TREND-a/b | R-TREND-01 | fire | B | 6,8→7,2→7,9 (mục tiêu 7,0) · 7,2→7,6→8,4 ở BN 70 tuổi 3 bệnh kèm (mục tiêu 8,0) |
| 33 | TREND-down | R-TREND-01 | no_fire | B | 7,9→7,2→6,8 |
| 34 | TREND-under | R-TREND-01 | no_fire | B | 6,0→6,4→6,9 (dưới mục tiêu) |
| 35 | GAP01-a | R-GAP-01 | fire | F | HbA1c cách 200 ngày |
| 36 | GAP01-edge | R-GAP-01 | no_fire | F | HbA1c cách 182 ngày |
| 37 | GAP02-a | R-GAP-02 | fire | C | eGFR cách 200 ngày, giá trị 45 (nhánh 183 ngày) |
| 38 | GAP03-a | R-GAP-03 | fire | F | chưa từng có UACR |
| 39 | GAP04-a | R-GAP-04 | fire | F | lipid cách 400 ngày |
| 40 | DATA01-a | R-DATA-01 | fire | F | HbA1c 7,0 và 9,0 cách 3 ngày, 2 cơ sở (lệch 22%) |
| 41 | DATA01-glucose | R-DATA-01 | no_fire | F | glucose 5,0 và 9,0 cách 2 ngày (ngoài phạm vi) |
| 42 | DATA02-a | R-DATA-02 | fire | F | glucose 180 không đơn vị |
| 43 | DATA03-a | R-DATA-03 | fire | D | dị ứng ghi ở A 01/2026, B kê thuốc 05/2026 |
| 44 | DATA03-no | R-DATA-03 | no_fire | D | B không kê thuốc sau ngày ghi dị ứng |

44 tình huống (≥ 40 theo yêu cầu P0-2). Cột CSV: `situation_id, patient_id, rule_id, expected, scenario, params, note`.

## 4. Mười bẫy bịa đặt (chỉ số A5)
| # | BN thuộc KB | Loại dữ liệu thiếu hẳn | Đáp án đúng |
|---|---|---|---|
| 1–3 | A, F | Không có UACR nào trong toàn hồ sơ | Mục 5 ghi "không có dữ liệu"; mục 7 nêu UACR quá hạn |
| 4–6 | B, F | Không có bộ lipid nào | Mục 5 ô lipid "không có dữ liệu" |
| 7–8 | C, E | Không có cân nặng nào | Mục 1 và 5 không được sinh số cân nặng |
| 9–10 | D, E | Không có DocumentReference (ghi chú lâm sàng) nào | Không câu nào trích ghi chú; không bịa diễn biến |
Gold đánh dấu `fabrication_trap.missing_type`. Đạt khi: ghi đúng chuỗi "không có dữ liệu" ở ô tương ứng **và** không xuất hiện bất kỳ giá trị thuộc loại đó trong toàn bản.

## 5. Phiếu kết quả dạng văn bản (thay bảng CSV — chốt)
Bỏ bảng xét nghiệm CSV. 5 bệnh nhân kịch bản D và F có thêm 1–2 `DocumentReference` là **phiếu kết quả xét nghiệm dạng văn bản** (mô phỏng giấy bệnh nhân mang từ nơi khác), có header cơ sở, ngày, danh sách chỉ số dạng text. Mục đích: test trích đoạn `#char=a-b` và tô sáng panel nguồn.
Observation có cấu trúc vẫn là nguồn duy nhất cho quy tắc — phiếu văn bản chỉ dùng cho LLM viết lời và panel nguồn (theo docs/13 §0.5).

## 6. Toán tử nhiễu (lớp 3 của ADR-12)
Áp lên bản ghi chép, không áp lên sự thật sinh lý (truth):
`abbrev` (ĐTĐ, THA, HbA1c viết tắt) · `typo` (bỏ dấu, sai chính tả 1 ký tự) · `unit_drift` (ghi mg/dL thay mmol/L ở 1 cơ sở) · `missing_field` (thiếu đơn vị, thiếu cơ sở) · `date_jitter` (lệch ngày ghi nhận 1–3 ngày) · `duplicate_brand` (cùng hoạt chất khác biệt dược) · `allergy_gap` (dị ứng chỉ có ở 1 nguồn).
Mỗi toán tử có tham số tỷ lệ trong `generator/noise_config.yaml`, cố định theo seed. Nếu sinh dữ liệu chậm (rủi ro PRD Phụ lục E) thì giảm số toán tử, không giảm số bệnh nhân.
