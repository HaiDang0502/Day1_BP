# docs/17 — Data Card & Manifest (template) ✅ — điền khi tag data-v1 (2/12)

## Data Card — VMEC-03 synthetic T2DM cohort
| Mục | Nội dung |
|---|---|
| Tên / phiên bản | vmec03-t2dm-synthetic · data-v1 · ngày tag: [điền] |
| Mô tả | 30 (mục tiêu 50) bệnh nhân ĐTĐ típ 2 ngoại trú mô phỏng, tiếng Việt, FHIR R4 bám VN Core IG 0.10.0, 6–24 tháng, 4–12 Encounter, ≥ 2 cơ sở/BN |
| Phương pháp sinh | 3 lớp (ADR-12): sinh lý → hành vi bác sĩ theo QĐ 5481 (sđ 2021) có lệch chủ ý → ghi chép nhiễu. Ghi chú template xác định, KHÔNG dùng LLM. Tái sinh giống hệt theo seed |
| Thành phần | Patient, ≥2 Organization, Encounter, Condition (ICD-10), Observation (LOINC+UCUM), MedicationRequest (ATC+biệt dược+số đăng ký), AllergyIntolerance, DocumentReference (ghi chú viết tắt) + gold/ + truth/ + scenarios.csv |
| KHÔNG chứa | Bất kỳ dữ liệu cá nhân thật nào. Toàn bộ tên, định danh, ngày đều sinh máy → không thuộc phạm vi dữ liệu cá nhân Luật 91/2025 |
| Phân bố | [điền sau khi sinh: tuổi, giới, HbA1c, eGFR theo kịch bản A–F] |
| Giới hạn | Không phản ánh dịch tễ thật VN; hành vi kê đơn là mô hình hóa; chỉ dùng cho phát triển/đánh giá hệ thống này |
| Giấy phép | [đề xuất CC BY 4.0 cho bộ dữ liệu — đội quyết] |
| Nguồn tham chiếu khi sinh | QĐ 5481 (sđ 2021); Synthea (chỉ logic tiến triển); KHÔNG dùng MIMIC-IV/ACI-Bench/MTS-Dialog/VietMed-Sum |

## Manifest (`data/manifest.json`)
```json
{
  "version": "data-v1", "created": "<ts>", "generator_commit": "<sha>",
  "seed": 20261213, "n_patients": 30,
  "files": [{"path": "bundles/p-001.json", "sha256": "..."}],
  "invariant_report": "reports/invariants_data-v1.md",
  "scenario_matrix": "scenarios.csv",
  "licenses_file": "../docs/20_LICENSES.md"
}
```
Quy tắc: đổi bất kỳ byte dữ liệu nào → tăng version (data-v2) + chạy lại benchmark; báo cáo bất biến (16_gold §3) phải PASS trước khi tag.
