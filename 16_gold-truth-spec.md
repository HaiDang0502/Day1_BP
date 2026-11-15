# docs/16 — Đặc tả Gold & Truth v1.0 ✅

Sinh **cùng lúc** với bundle (cùng seed) bởi generator; không viết tay sau.

## 1. Gold (`data/gold/<patient_id>.json`) — đáp án chấm benchmark
```json
{
  "patient_id": "p-001", "as_of": "2026-12-01", "data_version": "data-v1",
  "required_facts": [
    {"fact_id": "f-01", "type": "lab_latest", "code": "4548-4", "value": 8.2, "unit": "%",
     "date": "2026-10-15", "source_refs": ["Observation/obs-88"], "section_expected": 1}
  ],
  "expected_flags": [
    {"rule_id": "R-MED-02", "trigger_refs": ["MedicationRequest/m-3", "Observation/obs-90"]}
  ],
  "forbidden_flags": ["R-MED-01"],
  "fabrication_trap": {"missing_type": "UACR", "expected_text": "không có dữ liệu"},
  "expected_missing": [{"section": 7, "item": "UACR quá hạn theo Bảng 2"}],
  "conflicts": [{"code": "4548-4", "refs": ["obs-12", "obs-13"], "note": "lệch 25% giữa 2 cơ sở"}]
}
```
- `required_facts`: dùng cho A2 (bỏ sót) — mỗi BN 10–20 fact bắt buộc (thuốc đang dùng, HbA1c/eGFR gần nhất + xu hướng, dị ứng, bệnh kèm active, XN quá hạn).
- `expected_flags` + `forbidden_flags`: B1/B2. `forbidden_flags` bắt buộc cho các ca biên (eGFR = 30 → cấm R-MED-01).
- `section_expected`: kiểm tra fact rơi đúng mục trong 8 mục.

## 2. Truth (`data/truth/<patient_id>.json`) — sự thật ẩn trước nhiễu
Trạng thái sinh lý từng mốc thời gian (HbA1c "thật", eGFR "thật", tuân thủ thuốc) trước khi áp toán tử nhiễu (lớp 3 ADR-12). **Chỉ để phân tích lỗi; không đưa cho agent, không dùng để chấm A/B** (chấm trên dữ liệu như hệ thống thấy, tức bundle đã nhiễu).

## 3. Bất biến sinh lý (generator phải assert, ghi vào báo cáo tính thực)
- eGFR và creatinin nghịch chiều nhất quán theo công thức ± nhiễu đo.
- HbA1c không đổi > 4 điểm % giữa 2 kỳ ≤ 3 tháng (trừ kịch bản được đánh dấu).
- Ngày: sinh < khám đầu < các Encounter tăng dần ≤ as_of; thuốc kê trong Encounter tồn tại.
- Mỗi Observation/MedicationRequest thuộc đúng 1 Organization nguồn; mọi resource được tham chiếu đều tồn tại (Bundle đóng).
- BN ≥ 2 nguồn; mỗi nguồn ≥ 1 Encounter.

## 4. Quan hệ ba tầng
bundle (đã nhiễu, agent thấy) ⟂ gold (đáp án đo được từ bundle) ⟂ truth (ẩn). Ràng buộc: mọi `required_facts` phải **suy ra được từ bundle** (không yêu cầu agent biết truth); bẫy bịa đặt là phần tử CÓ trong truth nhưng KHÔNG có trong bundle → đáp án đúng là "không có dữ liệu".
