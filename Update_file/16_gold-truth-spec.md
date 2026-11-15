# docs/16 — Đặc tả Gold & Truth v2.0 ✅

Gold và Truth được sinh **cùng lúc** với bundle, cùng seed, bởi generator. Không viết tay sau khi sinh.

## 1. Gold — `data/gold/<patient_id>.json`
```json
{
  "patient_id": "p-007",
  "as_of": "2026-12-01",
  "data_version": "data-v1",
  "scenario": "C",
  "required_facts": [
    {"fact_id": "f-01", "type": "lab_latest", "code": "4548-4", "display": "HbA1c",
     "value": 8.2, "unit": "%", "date": "2026-10-15",
     "source_refs": ["Observation/obs-88"], "section_expected": 1},
    {"fact_id": "f-02", "type": "lab_latest", "code": "98979-8", "display": "eGFR",
     "value": 38, "unit": "mL/min/{1.73_m2}", "date": "2026-10-02",
     "source_refs": ["Observation/obs-90"], "section_expected": 1},
    {"fact_id": "f-03", "type": "med_active", "atc": "A10BA02", "display": "metformin",
     "brand": "Glucophage", "dose": "1000 mg/ngày",
     "source_refs": ["MedicationRequest/m-3"], "section_expected": 3},
    {"fact_id": "f-04", "type": "allergy", "substance": "penicillin",
     "source_refs": ["AllergyIntolerance/a-1"], "section_expected": 4}
  ],
  "expected_flags": [
    {"rule_id": "R-MED-02", "trigger_refs": ["MedicationRequest/m-3", "Observation/obs-90"],
     "group_key": "egfr-2026-10-02"},
    {"rule_id": "R-GAP-03", "trigger_refs": [], "note": "chưa từng có UACR"}
  ],
  "forbidden_flags": ["R-MED-01"],
  "fabrication_trap": {"missing_type": "UACR", "expected_text": "không có dữ liệu"},
  "expected_missing": [
    {"section": 7, "item": "UACR quá hạn theo QĐ 5481 Phần 8 mục 1.2"}
  ],
  "conflicts": [],
  "target_hba1c": {"value": 7.0, "tier": "default", "basis": "Bảng 4"}
}
```

### Giải thích các khối
| Khối | Dùng cho chỉ số | Ghi chú |
|---|---|---|
| `required_facts` | **A2** (bỏ sót) | 10–20 fact mỗi bệnh nhân: thuốc đang dùng, HbA1c và eGFR gần nhất, dị ứng, bệnh kèm active, xét nghiệm quá hạn. `section_expected` kiểm tra fact rơi đúng mục trong 8 mục |
| `expected_flags` | **B1** (recall) | Mọi cờ phải kích; `group_key` để kiểm tra việc gộp nhóm (docs/13 §0.8) |
| `forbidden_flags` | **B2** (precision) | Bắt buộc có ở mọi ca biên: eGFR = 30 → cấm R-MED-01; glucose = 3,0 → cấm R-LAB-01 |
| `fabrication_trap` | **A5** | 10 bệnh nhân có khối này (docs/15 §4) |
| `expected_missing` | **A2** mục 7 | Nội dung bắt buộc của mục "Cần xác minh / thiếu" |
| `conflicts` | **P1-3** | Cặp Observation mâu thuẫn, dùng cho R-DATA-01 và hiển thị cạnh nhau |
| `target_hba1c` | **R-TREND-01** | Bậc mục tiêu đã tính sẵn (docs/13 §0.7) để kiểm tra hệ thống chọn đúng bậc |

## 2. Truth — `data/truth/<patient_id>.json`
Trạng thái sinh lý "thật" tại từng mốc thời gian **trước khi** áp lớp nhiễu ghi chép (lớp 3 của ADR-12): HbA1c thật, eGFR thật, mức tuân thủ thuốc, biến cố thật.

**Chỉ dùng để phân tích lỗi.** Không đưa cho agent, **không dùng để chấm** bất kỳ chỉ số A hoặc B nào — vì hệ thống chỉ được yêu cầu trung thực với *hồ sơ*, không phải với sự thật ẩn.

## 3. Quan hệ ba tầng
```
truth (ẩn)  ──[lớp nhiễu]──▶  bundle (agent thấy)  ──[trích xuất]──▶  gold (đáp án)
```
**Ràng buộc bắt buộc (generator phải assert):**
- Mọi `required_facts` phải **suy ra được từ bundle** — không có fact nào chỉ tồn tại trong truth.
- Bẫy bịa đặt = phần tử có trong truth nhưng **không** có trong bundle → đáp án đúng là "không có dữ liệu".
- `expected_flags` phải tính được bằng cách chạy `rule_engine` trên bundle (kiểm tra chéo: generator gọi luôn rule_engine và so với gold đã ghi; lệch → dừng sinh dữ liệu).

## 4. Bất biến sinh lý (báo cáo tính thực — `reports/invariants_<data_version>.md`)
Generator phải PASS toàn bộ trước khi tag dữ liệu:
1. eGFR và creatinin nghịch chiều, nhất quán theo công thức ± nhiễu đo cho phép.
2. HbA1c không đổi quá 4 điểm phần trăm giữa hai kỳ cách ≤ 3 tháng (trừ bệnh nhân được đánh dấu kịch bản đặc biệt).
3. Thứ tự thời gian: ngày sinh < ngày chẩn đoán < các Encounter tăng dần ≤ `as_of`.
4. Mọi MedicationRequest thuộc một Encounter tồn tại; mọi Observation có `effectiveDateTime` thuộc khoảng theo dõi.
5. Bundle đóng: mọi reference đều trỏ tới resource có trong bundle.
6. Mỗi bệnh nhân ≥ 2 Organization; mỗi Organization ≥ 1 Encounter.
7. Giá trị xét nghiệm nằm trong khoảng sinh lý khả dĩ (trừ các ca cố ý: R-DATA-02 sai đơn vị).
8. Mỗi `fabrication_trap.missing_type` thật sự không xuất hiện dưới bất kỳ mã LOINC tương đương nào.
