# docs/18 — Bảng mã nội bộ v1.0 🟡

## 1. LOINC (xét nghiệm) — cột "XM" = đã xác minh 15/11/2026
| Chỉ số | LOINC | Đơn vị chuẩn (UCUM) | XM |
|---|---|---|---|
| eGFR (CKD-EPI 2021, creatinine) | **98979-8** | mL/min/{1.73_m2} | ✅ loinc.org |
| eGFR (CKD-EPI 2021, cr+cysC) — phụ | 94677-2 | mL/min/{1.73_m2} | ✅ |
| eGFR (CKD-EPI 2009) — nguồn cũ | 62238-1 | mL/min/{1.73_m2} | ✅ |
| HbA1c | 4548-4 | % | chuẩn phổ biến — đội rà lại trên loinc.org |
| Glucose huyết tương (đói) | 1558-6 | mmol/L | rà lại |
| Glucose huyết tương/máu | 2345-7 / 2339-0 | mmol/L | rà lại |
| Creatinin huyết thanh | 2160-0 | µmol/L | rà lại |
| UACR | 9318-7 | mg/g | rà lại |
| LDL-C đo trực tiếp | 2089-1 | mmol/L | rà lại |
| LDL-C tính (Friedewald) | 13457-7 | mmol/L | rà lại |
| Cholesterol TP / HDL / TG | 2093-3 / 2085-9 / 2571-8 | mmol/L | rà lại |
| Huyết áp tâm thu/trương | 8480-6 / 8462-4 | mm[Hg] | rà lại |
| Cân nặng | 29463-7 | kg | rà lại |
Quy đổi: glucose mg/dL→mmol/L ÷18,016; creatinin mg/dL→µmol/L ×88,4; cholesterol mg/dL→mmol/L ÷38,67. Hiển thị dấu phẩy thập phân.

## 2. ATC (nhóm thuốc dùng trong quy tắc & sinh dữ liệu)
| Hoạt chất | ATC | Dùng ở quy tắc |
|---|---|---|
| Metformin | A10BA02 | R-MED-01/02 (match prefix A10BA) |
| Glibenclamid | A10BB01 | R-MED-03 |
| Gliclazid / Glimepirid | A10BB09 / A10BB12 | R-MED-07 (SU = A10BB*) |
| Pioglitazon | A10BG03 | R-MED-04 (TZD = A10BG*) |
| Sitagliptin / Linagliptin | A10BH01 / A10BH05 | — |
| Empagliflozin / Dapagliflozin | A10BK03 / A10BK01 | — |
| Insulin glargine / NPH / aspart | A10AE04 / A10AC01 / A10AB05 | R-MED-07 (insulin = A10A*) |
| Atorvastatin / Rosuvastatin | C10AA05 / C10AA07 | bộ lipid, DDI P1 |
| Losartan / Amlodipin / Enalapril | C09CA01 / C08CA01 / C09AA02 | THA kèm |
(Mã ATC theo WHO ATC Index — đội đối chiếu whocc.no khi nhập bảng; coi đây là bản khởi tạo.)

## 3. ICD-10 (Condition)
E11* ĐTĐ típ 2 · I10–I15 THA · E78* rối loạn lipid · N18.1–N18.3 CKD gđ 1–3 · I50* suy tim (R-MED-04) · E16.0/E16.1/E16.2 hạ đường huyết (R-MED-07 §0.3) · I20–I25, I60–I69, J44 (bệnh đồng mắc §0.5).

## 4. Biệt dược VN ↔ hoạt chất ↔ số đăng ký (~40 dòng) 🔴 CẦN TRA
Nguồn: drugbank.vn / dichvucong.dav.gov.vn. Đội D tra và điền `data/drug_map.csv`: (biệt dược, hoạt chất, ATC, hàm lượng, số đăng ký, nguồn URL, ngày tra). Gợi ý phủ: 5–6 biệt dược metformin (Glucophage, Glumeform, Panfor…), 3–4 SU, 2–3 TZD/DPP-4/SGLT2i, 3–4 insulin, 3–4 statin, 3–4 thuốc HA. **Không bịa số đăng ký** — thiếu thì để trống cột và ghi chú.

## 5. Nhóm dị ứng chéo (R-MED-05)
Bảng đóng khởi tạo: penicillin ↔ amoxicillin/ampicillin (beta-lactam); sulfonamid kháng khuẩn ↔ (KHÔNG tự động chéo với SU — ghi chú tranh cãi, không kích); aspirin/NSAID nhóm. [Bác sĩ cố vấn xác nhận 25/11.]
