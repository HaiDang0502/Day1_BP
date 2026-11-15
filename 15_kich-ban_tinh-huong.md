# docs/15 — 6 kịch bản × tình huống cài sẵn v1.0 🟡 (đóng Ambiguity #6, #7 — hạn 19/11)

## 1. Phân bổ 30 bệnh nhân (đề xuất, thay 10/10/10)
[QUYẾT ĐỊNH 19/11] **10 sạch / 5 đang chỉnh thuốc / 15 có vấn đề** (theo PRD 1.0 mục 12.2 đề xuất). Mỗi BN: 6–24 tháng, 4–12 Encounter, ≥ 2 Organization, ghi chú tiếng Việt template có nhiễu.

## 2. Sáu kịch bản (A–F) và quy tắc mục tiêu
| KB | Mô tả | BN | Quy tắc phải kích |
|---|---|---|---|
| A. Ổn định | Đạt mục tiêu, đủ XN định kỳ, 1 nguồn chính | 6 | (không cờ Đỏ/Vàng — test âm; có thể 1 Xám) |
| B. Mất kiểm soát dần | HbA1c tăng dần, trên mục tiêu | 5 | R-TREND-01 |
| C. Suy thận tiến triển | eGFR giảm qua các kỳ, đang metformin | 5 | R-MED-01 (2 BN), R-MED-02 (3 BN) |
| D. Đa nguồn rối | 3 cơ sở, trùng hoạt chất khác biệt dược, dị ứng lệch nguồn | 5 | R-MED-05, R-MED-06, R-DATA-03 |
| E. Hạ đường huyết + cao tuổi | ≥65, SU/insulin, glucose thấp | 5 | R-LAB-01/02, R-MED-07, R-MED-03 |
| F. Dữ liệu xấu | XN quá hạn, mâu thuẫn số liệu, nghi sai đơn vị | 4 | R-GAP-01..04, R-DATA-01, R-DATA-02 |

## 3. Ma trận ~40 tình huống cài sẵn (gắn vào kịch bản — đóng Ambiguity #7)
Mỗi quy tắc P0 ≥ 2 tình huống dương + 1 âm-biên:
- 17 quy tắc × 2 dương = 34 tình huống dương (phân bổ vào B–F như bảng trên; R-MED-04: thêm 2 BN kịch bản D có I50).
- 6 tình huống biên: eGFR = 30 và = 45 (C); glucose = 3,0 và 3,9 (E); HbA1c đúng 183 ngày (F); đơn hết hiệu lực đúng as_of (D).
- Tổng ≈ 40. Bảng chi tiết `data/scenarios.csv`: cột (situation_id, patient_id, rule_id, expected=fire|no_fire, mô tả, tham số).

## 4. Mười bẫy bịa đặt (A5)
10 BN (rải đều kịch bản) thiếu hẳn một loại dữ liệu: 3 BN không có UACR nào; 3 không có lipid; 2 không có cân nặng; 2 không có ghi chú lâm sàng. Gold đánh dấu `fabrication_trap` = loại thiếu; đạt khi bản tóm tắt ghi "không có dữ liệu" đúng ô và không xuất hiện giá trị loại đó.

## 5. Bẫy thay thế bảng CSV (Ambiguity #8)
[QUYẾT ĐỊNH 19/11] Đề xuất: bỏ bảng xét nghiệm CSV, dùng phiếu kết quả dạng văn bản trong DocumentReference (giống thực tế bệnh nhân mang giấy) cho 5 BN kịch bản D/F — tạo điều kiện test trích đoạn `#char=a-b`. Observation cấu trúc vẫn là nguồn chính cho quy tắc.
