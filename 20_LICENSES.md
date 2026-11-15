# docs/20 — LICENSES & nguồn dữ liệu ngoài ✅ (rà 15/11/2026)

| Nguồn | Giấy phép / điều kiện | Cách dùng trong dự án | Nghĩa vụ |
|---|---|---|---|
| DDInter 2.0 (ddinter2.scbdd.com) | **CC BY-NC-SA 4.0**, truy cập mở (xác nhận 15/11/2026 qua bioregistry.io + trang chủ) | Bảng con ~25 hoạt chất tương tác "major" (P1-4) | Ghi attribution (NAR 2025, Cao et al.); phi thương mại — phù hợp đồ án; nếu thương mại hóa sau này phải thay nguồn |
| QĐ 5481/QĐ-BYT + QĐ 1353/QĐ-BYT | Văn bản QPPL/tài liệu chuyên môn nhà nước — trích dẫn tự do, ghi nguồn | 17 quy tắc, ngưỡng, mục tiêu | Trích đúng, ghi số văn bản + điều khoản |
| LOINC | Bản quyền Regenstrief Institute, dùng miễn phí theo LOINC License | Mã xét nghiệm | Ghi "This material contains content from LOINC (http://loinc.org)…" trong README |
| WHO ATC/DDD | WHOCC — dùng cho mục đích phi thương mại, ghi nguồn | Mã thuốc | Ghi nguồn WHOCC |
| ICD-10 | WHO; bản dịch ICD-10 VN của Bộ Y tế | Mã bệnh | Ghi nguồn |
| VN Core FHIR IG 0.10.0 (fhir.hl7.org.vn) | Theo giấy phép IG công bố (thường CC0/CC-BY — đội xác nhận trên trang IG khi tải gói) | Profile, validator | Ghi phiên bản IG + ngày tải; lưu gói offline (rủi ro registry — Phụ lục E) |
| drugbank.vn / Cục Quản lý Dược | Dữ liệu công bố công khai | Tra biệt dược ↔ số đăng ký | Ghi URL + ngày tra từng dòng trong drug_map.csv |
| Synthea | Apache-2.0 | CHỈ tham khảo logic tiến triển bệnh, không dùng code/dữ liệu trực tiếp | Nếu dùng code: giữ notice Apache-2.0 |
| PDSQI-9 (JAMIA 2025) | Công cụ nghiên cứu công bố | Dịch sang tiếng Việt để chấm nhóm C | Trích dẫn bài gốc; ghi rõ bản dịch chưa chuẩn hóa |
| KHÔNG dùng | MIMIC-IV (DUA cấm gửi API LLM bên thứ 3), ACI-Bench, MTS-Dialog, VietMed-Sum | — | Đã loại từ PRD mục 7 |

Quy tắc chung: mọi nguồn mới phải thêm dòng vào bảng này **trước** khi merge; `make benchmark` in đường dẫn file này vào báo cáo.
