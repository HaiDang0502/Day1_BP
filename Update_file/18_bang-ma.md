# docs/18 — Bảng mã nội bộ v2.0 ✅ (mã chuẩn đã chốt; cột biệt dược là việc tra của owner D)

## 1. LOINC — xét nghiệm
| Chỉ số | LOINC | Đơn vị chuẩn (UCUM) | Dùng ở quy tắc |
|---|---|---|---|
| **eGFR (CKD-EPI 2021, creatinine)** | **98979-8** | `mL/min/{1.73_m2}` | R-MED-01/02/03, R-GAP-02 |
| eGFR (CKD-EPI 2021, cr + cystatin C) | 94677-2 | `mL/min/{1.73_m2}` | như trên (nguồn phụ) |
| eGFR (CKD-EPI 2009) — hồ sơ cũ | 62238-1 | `mL/min/{1.73_m2}` | như trên (nguồn cũ) |
| HbA1c | 4548-4 | `%` | R-TREND-01, R-GAP-01, R-DATA-01 |
| Glucose huyết tương lúc đói | 1558-6 | `mmol/L` | R-LAB-01/02, R-DATA-02 |
| Glucose huyết tương | 2345-7 | `mmol/L` | R-LAB-01/02, R-DATA-02 |
| Glucose máu | 2339-0 | `mmol/L` | R-LAB-01/02, R-DATA-02 |
| Creatinin huyết thanh | 2160-0 | `umol/L` | R-GAP-02, R-DATA-01 |
| UACR (albumin/creatinin niệu) | 9318-7 | `mg/g` | R-GAP-03, R-GAP-02 (nhánh nguy cơ) |
| **LDL-C đo trực tiếp** | **2089-1** | `mmol/L` | R-GAP-04, R-TARGET-02 |
| **LDL-C tính (Friedewald)** | **13457-7** | `mmol/L` | như trên |
| Cholesterol toàn phần | 2093-3 | `mmol/L` | R-GAP-04, R-DATA-01 |
| HDL-C | 2085-9 | `mmol/L` | R-GAP-04 |
| Triglycerid | 2571-8 | `mmol/L` | R-GAP-04 |
| Huyết áp tâm thu | 8480-6 | `mm[Hg]` | R-TARGET-01 |
| Huyết áp tâm trương | 8462-4 | `mm[Hg]` | R-TARGET-01 |
| Cân nặng | 29463-7 | `kg` | biểu đồ P1-1, R-DATA-01 |
| Kali máu | 2823-3 | `mmol/L` | **không sinh cờ** (QĐ 5481 không quy định ngưỡng ngoại trú) |

### Quy đổi đơn vị (bắt buộc trước mọi so sánh)
glucose mg/dL → mmol/L: **÷ 18,016** · creatinin mg/dL → µmol/L: **× 88,4** · cholesterol/LDL/HDL mg/dL → mmol/L: **÷ 38,67** · triglycerid mg/dL → mmol/L: **÷ 88,57** · HbA1c giữ nguyên `%` (nếu nguồn ghi mmol/mol: `% = (mmol/mol ÷ 10,929) + 2,15`).
Hiển thị: dấu **phẩy** thập phân, ngày **dd/mm/yyyy**.

## 2. ATC — thuốc (bám QĐ 5481 Phụ lục 01 và Phụ lục 02)
| Hoạt chất | ATC | Nhóm | Dùng ở quy tắc |
|---|---|---|---|
| Metformin | A10BA02 | Biguanide | R-MED-01/02 (khớp prefix `A10BA`) |
| **Glibenclamid** (glyburide) | **A10BB01** | SU | **R-MED-03**; R-MED-07 (prefix `A10BB`) |
| Gliclazid | A10BB09 | SU | R-MED-07 |
| Glimepirid | A10BB12 | SU | R-MED-07 |
| Glipizid | A10BB07 | SU | R-MED-07 |
| Repaglinid | A10BX02 | Glinide | — |
| **Pioglitazon** | **A10BG03** | TZD | **R-MED-04** (prefix `A10BG`) |
| Sitagliptin | A10BH01 | DPP-4i | — |
| Linagliptin | A10BH05 | DPP-4i | — |
| Vildagliptin | A10BH02 | DPP-4i | — |
| Empagliflozin | A10BK03 | SGLT2i | — (không sinh cờ theo eGFR: QĐ 5481 mục 4.2(b) yêu cầu theo thông tin kê toa từng hoạt chất) |
| Dapagliflozin | A10BK01 | SGLT2i | — |
| Acarbose | A10BF01 | α-glucosidase | — |
| Liraglutid / Dulaglutid / Semaglutid | A10BJ02 / A10BJ05 / A10BJ06 | GLP-1 RA | — |
| Insulin glargine | A10AE04 | insulin nền | R-MED-07 (prefix `A10A`) |
| Insulin NPH | A10AC01 | insulin trung gian | R-MED-07 |
| Insulin aspart / lispro | A10AB05 / A10AB04 | insulin nhanh | R-MED-07 |
| Insulin trộn 70/30 | A10AD05 | insulin trộn | R-MED-07 |
| Atorvastatin / Rosuvastatin | C10AA05 / C10AA07 | statin | bộ lipid; R-DDI-01 (P1) |
| Losartan / Telmisartan | C09CA01 / C09CA07 | ARB | THA kèm |
| Enalapril / Perindopril | C09AA02 / C09AA04 | ACEi | THA kèm |
| Amlodipin | C08CA01 | CCB | THA kèm |
| Aspirin | B01AC06 | chống kết tập | R-DDI-01 (P1) |

Mã ATC theo WHO ATC Index — owner D đối chiếu whocc.no khi nhập bảng; bảng trên là bản khởi tạo đủ dùng cho 30 bệnh nhân mô phỏng.

## 3. ICD-10 — Condition
| Nhóm | Mã | Dùng ở |
|---|---|---|
| ĐTĐ típ 2 | E11.x | điều kiện phạm vi (bắt buộc có) |
| Tăng huyết áp | I10–I15 | bệnh đồng mắc (§0.6 rule spec); R-TARGET-01 |
| Rối loạn lipid máu | E78.x | bệnh đồng mắc |
| Bệnh thận mạn gđ 1–3 | N18.1, N18.2, N18.3 | bệnh đồng mắc; R-TARGET-01 ngưỡng chặt |
| **Suy tim** | **I50.x** | **R-MED-04** |
| **Hạ đường huyết** | **E16.0, E16.1, E16.2** | **R-MED-07** (§0.5 rule spec) |
| Bệnh mạch vành | I20–I25 | bệnh đồng mắc; R-TARGET-02 ngưỡng chặt |
| Đột quỵ / TIA | I60–I69 | bệnh đồng mắc; R-TARGET-02 |
| COPD | J44.x | bệnh đồng mắc |

## 4. Biệt dược Việt Nam ↔ hoạt chất ↔ số đăng ký — `data/drug_map.csv`
**Owner D tra và điền.** Nguồn: drugbank.vn và cổng Cục Quản lý Dược. Cột: `brand, ingredient, atc, strength, registration_no, source_url, checked_date`.

Phủ tối thiểu (~40 dòng) để kịch bản D có đủ cặp trùng hoạt chất khác biệt dược:
| Hoạt chất | Số biệt dược cần | Ghi chú |
|---|---|---|
| Metformin | 5–6 | cần ≥ 2 để R-MED-06 kích |
| Gliclazid | 3 | cần ≥ 2 cho ca MED06-b |
| Glibenclamid | 2 | cho R-MED-03 |
| Glimepirid | 2–3 | |
| Pioglitazon | 2 | cho R-MED-04 |
| DPP-4i, SGLT2i | 3–4 | |
| Insulin | 3–4 | glargine, NPH, aspart, trộn |
| Statin | 3–4 | |
| Thuốc huyết áp | 3–4 | ARB/ACEi/CCB |
| Thuốc phối hợp (metformin + DPP-4i) | 2 | tạo ca trùng hoạt chất khó phát hiện |

**Nguyên tắc:** không bịa số đăng ký. Không tra được thì để trống cột `registration_no` và ghi `note = "chưa tra được"`; generator vẫn chạy nhưng Data Card phải ghi rõ số dòng còn thiếu.

## 5. Nhóm dị ứng chéo (R-MED-05) — bảng đóng
| Nhóm | Thành viên | Mức |
|---|---|---|
| Beta-lactam | penicillin, amoxicillin, ampicillin | chéo chắc |
| Beta-lactam mở rộng | + cephalosporin các thế hệ | chéo "có thể" — câu cờ ghi rõ mức |
| NSAID | aspirin, ibuprofen, diclofenac, meloxicam | chéo chắc |
| Sulfonamid kháng khuẩn ↔ sulfonylurea | **KHÔNG chéo tự động** | bằng chứng không nhất quán → hệ thống chỉ ghi chú, không kích cờ |
Dị nguyên không phải thuốc (thực phẩm, phấn hoa) không tham gia quy tắc.
