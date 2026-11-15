# docs/17 — Data Card & Manifest v2.0 ✅ (điền số liệu khi tag data-v1, hạn 2/12)

## Data Card — VMEC-03 synthetic T2DM outpatient cohort
| Mục | Nội dung |
|---|---|
| **Tên / phiên bản** | `vmec03-t2dm-synthetic` · `data-v1` · ngày tag: _[điền khi sinh xong]_ |
| **Mô tả** | 30 bệnh nhân (mục tiêu 50) ĐTĐ típ 2 ngoại trú mô phỏng, tiếng Việt, FHIR R4 bám VN Core IG 0.10.0; 6–24 tháng theo dõi; 4–12 Encounter; ≥ 2 cơ sở nguồn mỗi bệnh nhân |
| **Phân bổ** | 10 hồ sơ sạch / 5 đang chỉnh thuốc / 15 có vấn đề; 6 kịch bản A–F (docs/15 §2) |
| **Phương pháp sinh** | 3 lớp theo ADR-12: (1) sinh lý → (2) hành vi bác sĩ theo QĐ 5481 có lệch chủ ý → (3) ghi chép có nhiễu. Ghi chú tiếng Việt sinh bằng **template xác định, không dùng LLM**. Tái sinh giống hệt theo seed |
| **Thành phần mỗi bệnh nhân** | Patient, ≥ 2 Organization, Encounter, Condition (ICD-10), Observation (LOINC + UCUM), MedicationRequest (ATC + biệt dược + số đăng ký), AllergyIntolerance, DocumentReference (ghi chú viết tắt; 5 BN có phiếu kết quả dạng văn bản) |
| **Đi kèm** | `gold/`, `truth/`, `scenarios.csv` (44 tình huống), `manifest.json`, báo cáo bất biến |
| **KHÔNG chứa** | Bất kỳ dữ liệu cá nhân thật nào. Toàn bộ tên, định danh, ngày tháng đều sinh máy → không có chủ thể dữ liệu, không thuộc phạm vi Luật 91/2025/QH15 |
| **Phân bố thống kê** | _[điền sau khi sinh: tuổi (TB ± SD), giới, thời gian mắc bệnh, HbA1c, eGFR, số thuốc, số cờ theo kịch bản]_ |
| **Giới hạn đã biết** | Không phản ánh dịch tễ thật của Việt Nam; hành vi kê đơn là mô hình hóa từ văn bản hướng dẫn, không phải quan sát thực tế; ghi chú sinh từ template nên đa dạng ngôn ngữ thấp hơn thực tế; chỉ dùng để phát triển và đánh giá hệ thống này |
| **Giấy phép** | CC BY 4.0 (đề xuất cho bộ dữ liệu mô phỏng do đội sinh ra) |
| **Nguồn tham chiếu khi sinh** | QĐ 5481/QĐ-BYT (sđ QĐ 1353/QĐ-BYT) — ngưỡng và tần suất; Synthea (Apache-2.0) — chỉ tham khảo logic tiến triển bệnh. **Không dùng** MIMIC-IV, ACI-Bench, MTS-Dialog, VietMed-Sum |
| **Người tạo / liên hệ** | Đội P-XXX, owner D |

## Manifest — `data/manifest.json`
```json
{
  "version": "data-v1",
  "created": "<ISO timestamp>",
  "generator_commit": "<git sha>",
  "seed": 20261213,
  "n_patients": 30,
  "distribution": {"clean": 10, "adjusting": 5, "problematic": 15},
  "scenarios": {"A": 6, "B": 5, "C": 5, "D": 5, "E": 5, "F": 4},
  "files": [
    {"path": "bundles/p-001.json", "sha256": "..."},
    {"path": "gold/p-001.json", "sha256": "..."}
  ],
  "invariant_report": "../reports/invariants_data-v1.md",
  "scenario_matrix": "scenarios.csv",
  "drug_map": "drug_map.csv",
  "rules_version_checked": "rules_v1.0.0",
  "licenses_file": "../docs/20_LICENSES.md"
}
```

## Quy tắc quản lý phiên bản dữ liệu
1. Đổi bất kỳ byte nào trong `data/` → tăng version (`data-v2`) và chạy lại toàn bộ benchmark.
2. Báo cáo bất biến (docs/16 §4) phải PASS trước khi tag.
3. Generator chạy lại `rule_engine` trên bundle vừa sinh và so với `gold.expected_flags`; lệch → dừng, không tag.
4. Mỗi bản tóm tắt lưu `bundle_sha256` để biết chính xác đã chạy trên dữ liệu nào.
