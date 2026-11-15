# VMEC-03 — Bộ tài liệu dự án (v2.0, 15/11/2026)

Sinh lại sau khi đối chiếu **toàn văn QĐ 5481/QĐ-BYT** và **QĐ 1353/QĐ-BYT** (bản gốc lưu trong `refs/` kèm SHA-256). **Mọi quyết định treo của PRD 1.1 đã được chốt.**

## Đọc theo thứ tự này
| Nếu bạn là | Đọc |
|---|---|
| Giám khảo / mentor | `01_Brief` → `02_PRD` → `21_checklist-phap-ly` → `24_ho-so-rui-ro-AI` |
| Owner D (dữ liệu & quy tắc) | `13_rule-spec` → `14_trich-luc` → `15_kich-ban` → `16_gold-truth` → `18_bang-ma` |
| Owner B (backend) | `10_SRS` → `11_thiet-ke` → `13_rule-spec` → `22_prompt-versioning` → `23_README` |
| Owner F (frontend) | `12_wireframe` → `10_SRS` (FR-05..FR-09) → `19_glossary` |
| Owner E (benchmark & PM) | `05_benchmark-plan` → `16_gold-truth` → `27_ablation-template` → `28_bo-nop-bai` |

## Danh mục đầy đủ
| File | Nội dung | Trạng thái |
|---|---|---|
| `00_INDEX.md` | File này | ✅ |
| `00_scope-A.md` | Phạm vi đã duyệt (tái dựng — thiếu bản gốc) | 🟡 |
| `01_Brief.md` | Brief 1 trang, phiên bản 1.2 | ✅ |
| `02_PRD.md` | PRD 1.2 đầy đủ + Phụ lục A–E | ✅ |
| `05_benchmark-plan.md` | Kế hoạch đo A/B/C/D + ablation + ngân sách | ✅ |
| `06_phong-van_va_phuong-an-D.md` | Phỏng vấn + phương án không có bác sĩ (đã kích hoạt) | ✅ |
| `09_nghiep-vu.md` | Quy trình as-is / to-be, RACI, ràng buộc | ✅ |
| `10_SRS.md` | 21 yêu cầu chức năng, phi chức năng, API, máy trạng thái | ✅ |
| `11_thiet-ke-he-thong.md` | Stack đã chốt, sơ đồ, luồng, bảo mật | ✅ |
| `12_wireframe_ui-flow.md` | 6 màn hình, mô tả đủ để vẽ hi-fi | ✅ |
| `13_rule-spec.md` | **Đặc tả 17 quy tắc** — định nghĩa vận hành, điều kiện hình thức, test | ✅ |
| `14_trich-luc_QD5481_mapping.md` | **Ánh xạ trích dẫn** + 3 điểm phải sửa quy tắc | ✅ |
| `15_kich-ban_tinh-huong.md` | 6 kịch bản, 44 tình huống, 10 bẫy bịa đặt | ✅ |
| `16_gold-truth-spec.md` | Gold/Truth schema + 8 bất biến sinh lý | ✅ |
| `17_data-card_manifest.md` | Data Card + manifest (điền số khi tag data-v1) | ✅ |
| `18_bang-ma.md` | LOINC, ATC, ICD-10, quy đổi đơn vị, nhóm dị ứng chéo | ✅ |
| `19_glossary.md` | Thuật ngữ, chuỗi cố định, cụm cấm | ✅ |
| `20_LICENSES.md` | Giấy phép mọi nguồn ngoài | ✅ |
| `21_checklist-phap-ly.md` | 11 câu hỏi–đáp ≤ 60 giây (mục tiêu G5) | ✅ |
| `22_prompt-versioning.md` | Cấu trúc prompt, SemVer, quy trình đổi | ✅ |
| `23_README-trien-khai.md` | Triển khai một lệnh + checklist công bố URL | ✅ |
| `24_ho-so-rui-ro-AI.md` | Tự phân loại rủi ro theo Luật AI 134/2025 | ✅ |
| `25_DPIA-khung.md` | Khung DPIA theo Điều 19 NĐ 356/2025 | ✅ |
| `26_doi-chieu_TT13-2025.md` | Đối chiếu bệnh án điện tử + ánh xạ 8 mục | ✅ |
| `27_ablation-report_template.md` | Template báo cáo ablation | ✅ |
| `28_bo-nop-bai.md` | Bộ nộp bài Demo Day + phân công | ✅ |

## `refs/` — nguồn gốc
| File | Nội dung |
|---|---|
| `QD-5481-BYT_2020.docx` | Hướng dẫn chẩn đoán và điều trị ĐTĐ típ 2 (bản gốc) |
| `QD-1353-BYT_2021.docx` | Quyết định sửa đổi, bổ sung |
| `citations.json` | 19 anchor ánh xạ mã quy tắc → vị trí trong văn bản (cho panel nguồn) |
| `SHA256SUMS.txt` | Mã băm hai văn bản |

---

## Ba điểm quan trọng nhất từ việc đối chiếu văn bản gốc
1. **QĐ 1353/2021 không ảnh hưởng quy tắc nào.** Chỉ sửa 2 điểm: bổ sung thành viên biên soạn và sửa nội dung về chế độ insulin cho bệnh nhân nặng không nguy kịch (Phần 5 — nội trú, ngoài phạm vi dự án). Mọi ngưỡng vẫn nguyên trạng.
2. **Ba quy tắc đã phải sửa:** R-MED-03 (văn bản không có ngưỡng 60 tuổi → đổi sang ≥ 65 tuổi hoặc eGFR < 60, có nguồn thật), R-GAP-01 (văn bản yêu cầu HbA1c mỗi 3 tháng, chặt hơn ngưỡng cũ → giữ dung sai 183 ngày nhưng ghi rõ trong cờ), R-GAP-03 (hai mốc khác nhau trong cùng văn bản → chọn mốc "ít nhất mỗi năm" và thêm nhánh 183 ngày cho nhóm nguy cơ). Chi tiết: `14_trich-luc` §4.
3. **Đã xác nhận văn bản không quy định** ngưỡng báo động ngoại trú cho tăng huyết áp cấp cứu, kali máu, HbA1c, hay eGFR cho SGLT2i → chủ trương "không tạo cờ cho các tình huống này" của PRD là đúng và nay có bằng chứng rà soát toàn văn.

## Còn thiếu (không sinh được, cần đội bổ sung)
Bản Scope A gốc và ADR-12 gốc · kết quả phỏng vấn bác sĩ · tên thành viên cho các owner D/B/F/E · thông tin máy ảo và tên miền · ~40 dòng biệt dược ↔ số đăng ký trong `data/drug_map.csv` (owner D tra trên drugbank.vn, không được bịa số đăng ký) · xác nhận định dạng nộp bài với ban tổ chức.
