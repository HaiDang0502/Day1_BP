# docs/13 — Đặc tả 17 quy tắc an toàn P0 (Rule Spec v2.0) ✅ ĐÃ CHỐT

Owner: D (dữ liệu & quy tắc) + B (backend) · Ngày: 15/11/2026 · Nguồn đã đối chiếu toàn văn QĐ 5481/QĐ-BYT và QĐ 1353/QĐ-BYT (xem docs/14).
Mọi quyết định treo trong PRD 1.1 đã được chốt trong tài liệu này. Không còn mục chờ duyệt.

**Nguyên tắc bất biến:** quy tắc 100% xác định, không gọi mạng, thuần hàm trên dữ liệu đã nạp, tính tại ngày `as_of`. LLM chỉ diễn giải payload cờ, không thêm bớt (B4 = 100%). Mọi câu cờ kết thúc bằng "— cần bác sĩ xem xét."

---

## 0. Định nghĩa vận hành (chốt)

### 0.1. `as_of`
Ngày lượt tái khám sắp tới của bệnh nhân. Mọi quy tắc tính tại ngày này. Nếu không có lịch tái khám, `as_of` = ngày tạo bản tóm tắt.

### 0.2. "Đang dùng" (thuốc)
MedicationRequest thỏa **cả hai**:
- `status = active`, và
- khoảng hiệu lực phủ `as_of`: `authoredOn + supplyDuration ≥ as_of`.

**Chốt:** không có `dispenseRequest.expectedSupplyDuration` → mặc định **90 ngày**. Căn cứ: QĐ 5481 Phần 4 mục 4.2(i) yêu cầu đánh giá lại chiến lược điều trị mỗi 3 tháng, nên đơn ngoại trú mạn tính giả định hiệu lực một kỳ 3 tháng. Đơn `stopped / completed / cancelled` không tính. Hai đơn cùng hoạt chất từ 2 nguồn đều active → cả hai đều "đang dùng" (kích R-MED-06).

### 0.3. "Gần nhất" và tuổi dữ liệu
`X_gần_nhất` = Observation mã X có `effectiveDateTime` lớn nhất và ≤ `as_of`.
**Chốt:** tuổi tối đa của eGFR để dùng trong quy tắc thuốc = **365 ngày** (căn cứ Bảng 2: creatinin/eGFR làm lại mỗi năm). eGFR cũ hơn 365 ngày → không kích R-MED-01/02/03, thay vào đó R-GAP-02 kích cờ Xám "thiếu dữ liệu để kiểm tra chống chỉ định".
**Chốt:** hệ thống **không tự tính eGFR từ creatinin** ở MVP. Lý do: tự sinh chỉ số lâm sàng là bước suy diễn ngoài phạm vi "xác định trước, LLM sau", và công thức CKD-EPI cần chủng tộc/giới/tuổi đúng chuẩn. Chỉ có creatinin mà không có eGFR → coi như thiếu eGFR (R-GAP-02 xử lý).

### 0.4. "Người cao tuổi"
**Chốt: tuổi ≥ 65** tại `as_of`. Căn cứ: QĐ 5481 Phần 4 mục 4.1(i) xếp nhóm người cao tuổi từ trên 65 tuổi; Bảng 5 đặt mục tiêu riêng cho người cao tuổi. Một ngưỡng duy nhất cho mọi quy tắc (xóa mâu thuẫn 60/65 của PRD 1.1).

### 0.5. "Có hạ đường huyết trong 90 ngày"
Một trong hai, trong 90 ngày trước `as_of`:
- (a) Observation glucose < 3,9 mmol/L (sau quy đổi đơn vị), hoặc
- (b) Condition mã ICD-10 E16.0 / E16.1 / E16.2.

**Chốt:** ghi chú tự do (DocumentReference) **không** được dùng để kích quy tắc — quy tắc chỉ chạy trên dữ liệu có cấu trúc. Ghi chú chỉ dùng cho bước LLM viết lời và cho panel nguồn.

### 0.6. Bệnh đồng mắc (đếm cho mục tiêu HbA1c)
Danh sách đóng, Condition `clinicalStatus = active`:
THA (I10–I15) · bệnh mạch vành (I20–I25) · suy tim (I50) · đột quỵ/TIA (I60–I69) · bệnh thận mạn (N18) · COPD (J44) · rối loạn lipid máu (E78).
Căn cứ: Bảng 5 phân nhóm theo tình trạng sức khỏe "nhiều bệnh"; danh sách đóng để quy tắc xác định được.

### 0.7. Mục tiêu HbA1c cá thể — 3 bậc (chốt)
| Điều kiện | Mục tiêu | Căn cứ |
|---|---|---|
| Mặc định (< 65 tuổi) | **7,0%** | Bảng 4: HbA1c < 7% |
| ≥ 65 tuổi, ≤ 1 bệnh đồng mắc | **7,5%** | Bảng 5, nhóm "Mạnh khỏe": < 7,5% |
| ≥ 65 tuổi, ≥ 2 bệnh đồng mắc | **8,0%** | Bảng 5, nhóm "Nhiều bệnh, sức khỏe trung bình": < 8,0% |
Mọi câu nhắc mục tiêu phải kèm: "mục tiêu mặc định theo QĐ 5481, bác sĩ có thể chỉnh". Hệ thống **không** dùng bậc < 8,5% (nhóm bệnh nguy kịch/sức khỏe kém) vì đánh giá tình trạng đó cần bác sĩ, không suy ra được từ hồ sơ.

### 0.8. Gộp nhóm và đếm cờ (B3)
- Mỗi quy tắc kích vẫn sinh một cờ riêng (để đo B1 recall chính xác).
- UI **gộp theo sự kiện nguồn**: các cờ dùng chung ≥ 1 `trigger_ref` được hiển thị thành một nhóm, tiêu đề là sự kiện (ví dụ "Hạ đường huyết 12/10/2026" chứa R-LAB-01 + R-MED-07).
- **B3 đếm số nhóm có nhãn Đỏ hoặc Vàng**; nhóm toàn Xám không tính vào B3.
- Hiển thị mặc định tối đa **7 nhóm** (Đỏ trước → Vàng → Xám thu gọn), phần còn lại sau nút "xem thêm". Không có giới hạn số cờ *sinh ra*.

### 0.9. Quy đổi đơn vị (bắt buộc trước mọi so sánh)
glucose mg/dL → mmol/L: ÷ 18,016 · creatinin mg/dL → µmol/L: × 88,4 · cholesterol/LDL/HDL mg/dL → mmol/L: ÷ 38,67 · triglycerid mg/dL → mmol/L: ÷ 88,57 · HbA1c giữ %. Giá trị không có đơn vị → chỉ R-DATA-02 xử lý, các quy tắc khác bỏ qua giá trị đó.

### 0.10. Payload cờ chuẩn
```json
{
  "rule_id": "R-MED-02",
  "severity": "red|yellow|gray",
  "as_of": "2026-12-01",
  "trigger_data": [
    {"ref": "MedicationRequest/m-3", "label": "metformin 1000 mg/ngày",
     "date": "2026-09-10", "org": "Organization/bv-a"},
    {"ref": "Observation/obs-90", "label": "eGFR 38 mL/phút/1,73m²",
     "date": "2026-10-02", "org": "Organization/bv-b"}
  ],
  "citation": {
    "doc": "QĐ 5481/QĐ-BYT (sđ QĐ 1353/QĐ-BYT)",
    "section": "Phụ lục 01, mục 2 (Metformin) — Chống chỉ định",
    "anchor": "PL01-M2-CCD",
    "note": null
  },
  "group_key": "egfr-2026-10-02",
  "status": "open|acknowledged",
  "ack": {"by": null, "at": null, "reason": null}
}
```
`anchor` tra trong `refs/citations.json` để panel nguồn tô sáng đúng đoạn văn bản. `note` dùng cho các quy tắc có dung sai hệ thống (R-GAP-01, R-GAP-03).

---

## 1. Bảy quy tắc an toàn thuốc

### R-MED-01 — Metformin khi eGFR < 30 · 🔴 Đỏ
**Điều kiện:** `∃ med đang dùng có ATC prefix "A10BA"` ∧ `eGFR_gần_nhất < 30` (tuổi ≤ 365 ngày)
**Nguồn:** QĐ 5481, Phụ lục 01 mục 2 (Metformin) — Chống chỉ định: suy thận với eGFR < 30 mL/phút.
**Diễn giải mẫu:** "Đang dùng metformin (<biệt dược>) trong khi eGFR gần nhất là <giá trị> mL/phút/1,73m² ngày <ngày> tại <cơ sở>; QĐ 5481 nêu chống chỉ định metformin khi eGFR dưới 30 — cần bác sĩ xem xét."
**Test:** eGFR 29,9 → kích · eGFR 30,0 → không kích (chuyển R-MED-02) · eGFR 25 nhưng 400 ngày → không kích, R-GAP-02 kích · metformin status=stopped → không kích.

### R-MED-02 — Metformin khi 30 ≤ eGFR ≤ 45 · 🟡 Vàng
**Điều kiện:** `metformin đang dùng` ∧ `30 ≤ eGFR_gần_nhất ≤ 45`
**Nguồn:** Phụ lục 01 mục 2 — giảm liều khi eGFR trong khoảng 30–45 mL/phút.
**Test:** eGFR 30,0 → kích · 45,0 → kích · 45,1 → không kích.

### R-MED-03 — Glibenclamid ở người cao tuổi hoặc suy thận · 🔴 Đỏ
**Điều kiện:** `∃ med đang dùng ATC "A10BB01"` ∧ (`tuổi ≥ 65` ∨ `eGFR_gần_nhất < 60`)
**Nguồn:** Phụ lục 01 mục 1(a) + "Tóm lại" mục 1 — glibenclamid có nguy cơ hạ glucose máu cao, nhất là ở người già, suy gan, suy thận; chống chỉ định suy thận. Định nghĩa người cao tuổi: Phần 4 mục 4.1(i).
**Đã sửa so với PRD 1.1:** bỏ ngưỡng "tuổi > 60" (không có trong văn bản), thay bằng ≥ 65 theo mục 4.1(i) và thêm nhánh eGFR < 60 (là chống chỉ định được nêu rõ). Xem docs/14 §4(a).
**Test:** 64 tuổi + eGFR 80 → không kích · 65 tuổi → kích · 50 tuổi + eGFR 55 → kích · gliclazid 70 tuổi → không kích (khác ATC).

### R-MED-04 — TZD (pioglitazon) khi có suy tim · 🔴 Đỏ
**Điều kiện:** `∃ med đang dùng ATC prefix "A10BG"` ∧ `∃ Condition active mã I50*`
**Nguồn:** Phần 4 mục 4.1(g) — chống chỉ định nhóm TZD do tăng suy tim sung huyết; Phụ lục 01 mục 4 — chống chỉ định suy tim độ III–IV theo NYHA.
**Ghi chú trong cờ:** hồ sơ chỉ có mã I50* nên hệ thống không phân biệt được độ NYHA; câu cờ nêu rõ điều này.
**Test:** pioglitazon + I50.0 → kích · pioglitazon + I50 đã resolved → không kích · pioglitazon không có I50 → không kích.

### R-MED-05 — Thuốc đang dùng trùng chất gây dị ứng · 🔴 Đỏ
**Điều kiện:** `∃ med đang dùng` có hoạt chất ∈ tập hợp dị nguyên lấy từ **mọi** AllergyIntolerance của bệnh nhân (mọi nguồn), so sánh theo hoạt chất đã chuẩn hóa, có áp bảng nhóm chéo §0.11.
**Nguồn:** Phụ lục 01 mục 1(a) nêu dị ứng thuốc là chống chỉ định; quy tắc đối chiếu đa nguồn là quy tắc an toàn của hệ thống (nguồn hỗn hợp — ghi rõ trong citation).
**Test:** dị ứng metformin ghi ở cơ sở B + đơn metformin ở cơ sở A → kích · dị ứng penicillin + amoxicillin đang dùng → kích (nhóm chéo) · dị ứng "hải sản" → không kích (không phải thuốc).

### §0.11. Bảng nhóm dị ứng chéo (đóng)
beta-lactam: penicillin ↔ amoxicillin ↔ ampicillin ↔ cephalosporin (ghi mức "có thể") · NSAID: aspirin ↔ ibuprofen ↔ diclofenac ↔ meloxicam · sulfonamid kháng khuẩn: **không** tự động chéo sang sulfonylurea (bằng chứng không nhất quán) — hệ thống ghi chú thay vì kích cờ.

### R-MED-06 — Trùng hoạt chất, khác biệt dược · 🟡 Vàng
**Điều kiện:** `∃ ≥ 2 MedicationRequest đang dùng` cùng mã ATC đầy đủ, khác `medicationCodeableConcept` (biệt dược)
**Nguồn:** Phần 4 mục 4.2(c) — không lựa chọn các thuốc cùng một nhóm, cùng cơ chế tác dụng; cộng bước chuẩn hóa thuốc của hệ thống.
**Test:** Glucophage + Glumeform đều active → kích · hai đơn metformin cùng biệt dược (tái kê) → không kích · metformin + gliclazid → không kích.

### R-MED-07 — SU/insulin + hạ đường huyết + cao tuổi · 🟡 Vàng
**Điều kiện:** (`∃ med đang dùng ATC prefix "A10BB"` ∨ `ATC prefix "A10A"`) ∧ `có_hạ_đường_huyết_90_ngày` (§0.5) ∧ `tuổi ≥ 65`
**Nguồn:** Phần 4 mục 4.2(j) — thận trọng nguy cơ hạ glucose máu khi điều trị bằng sulfonylurea, insulin, đặc biệt ở bệnh nhân lớn tuổi; Phần 6 mục 1(b) — nhóm thuốc có nguy cơ hạ đường huyết gồm insulin và SU.
**Test:** 70 tuổi + gliclazid + glucose 3,5 cách 60 ngày → kích · cùng thế nhưng 60 tuổi → không kích · 70 tuổi + metformin đơn độc + glucose 3,5 → không kích.

---

## 2. Hai quy tắc xét nghiệm

### R-LAB-01 — Hạ đường huyết mức 2 · 🔴 Đỏ
**Điều kiện:** `∃ Observation glucose < 3,0 mmol/L` trong 90 ngày trước `as_of`
**Nguồn:** Phần 6 mục 2, bảng phân mức — Mức 2: glucose dưới 54 mg/dL (3,0 mmol/L).
**Test:** 2,9 → kích · 3,0 → không kích (sang R-LAB-02) · 2,5 cách 100 ngày → không kích.

### R-LAB-02 — Hạ đường huyết mức 1 · 🟡 Vàng
**Điều kiện:** `∃ glucose ∈ [3,0 ; 3,9)` trong 90 ngày ∧ ¬`∃ glucose < 3,0` trong cùng 90 ngày
**Nguồn:** Phần 6 mục 2 — Mức 1: glucose dưới 70 mg/dL (3,9 mmol/L) và từ 54 mg/dL (3,0 mmol/L) trở lên.
**Test:** 3,0 → kích · 3,89 → kích · 3,9 → không kích · có cả 2,8 và 3,5 → chỉ R-LAB-01 kích.

---

## 3. Một quy tắc xu hướng

### R-TREND-01 — HbA1c tăng liên tiếp và trên mục tiêu · 🟡 Vàng
**Điều kiện:** tồn tại 3 giá trị HbA1c liên tiếp theo thời gian `t1 < t2 < t3 ≤ as_of` với `v1 < v2 < v3` ∧ `v3 > mục_tiêu(§0.7)`
("tăng ≥ 2 kỳ liên tiếp" = 2 lần tăng = cần 3 điểm dữ liệu — chốt cách hiểu này.)
**Nguồn:** ngưỡng mục tiêu từ Bảng 4 và Bảng 5; nhịp đánh giá từ Phần 4 mục 4.2(i) (đánh giá lại mỗi 3 tháng). **Logic xu hướng là suy diễn của hệ thống** — citation ghi rõ phần nào từ văn bản, phần nào do hệ thống suy ra.
**Test:** 6,8 → 7,2 → 7,9 (mục tiêu 7,0) → kích · 7,9 → 7,2 → 6,8 → không kích · 6,0 → 6,4 → 6,9 → không kích (dưới mục tiêu) · 7,5 → 7,8 → 7,9 ở BN 70 tuổi 3 bệnh kèm (mục tiêu 8,0) → không kích · chỉ có 2 giá trị → không kích.

---

## 4. Bốn quy tắc thiếu dữ liệu (⚪ Xám)

### R-GAP-01 — Không có HbA1c > 183 ngày
**Điều kiện:** ¬`∃ HbA1c` trong 183 ngày trước `as_of` (bao gồm trường hợp **chưa từng có** HbA1c)
**Nguồn:** Bảng 2 — HbA1c tái khám mỗi 3 tháng.
**Dung sai hệ thống (bắt buộc ghi trong cờ):** văn bản yêu cầu mỗi 3 tháng; hệ thống chỉ kích cờ khi đã quá **2 kỳ** (183 ngày) để tránh cờ nổ trên gần như mọi bệnh nhân. `citation.note` = "QĐ 5481 Bảng 2 yêu cầu mỗi 3 tháng; hệ thống dung sai 2 kỳ."
**Test:** HbA1c cách 182 ngày → không kích · 184 ngày → kích · chưa từng có → kích.

### R-GAP-02 — Không có creatinin/eGFR quá hạn
**Điều kiện:** ngưỡng `T` = **183 ngày** nếu (`UACR_gần_nhất > 30 mg/g` ∨ `eGFR_gần_nhất < 60`), ngược lại `T` = **365 ngày**; kích khi ¬`∃ creatinin hoặc eGFR` trong `T` ngày (bao gồm chưa từng có).
**Nguồn:** Bảng 2 — creatinin/eGFR làm lại mỗi năm; Phần 8 mục 1.2(a) — ít nhất mỗi năm một lần; mục 1.2(b) — nếu albumin niệu trên 30 mg/g creatinin và/hoặc eGFR dưới 60 thì đánh giá hai lần một năm.
**Test:** eGFR cách 300 ngày, eGFR trước đó 75 → không kích · eGFR cách 200 ngày, giá trị 45 → kích (nhánh 183) · chưa từng có → kích.

### R-GAP-03 — Không có UACR quá hạn
**Điều kiện:** cùng cấu trúc hai ngưỡng như R-GAP-02 (183 ngày cho nhóm nguy cơ, 365 ngày cho còn lại), áp cho UACR.
**Nguồn:** Phần 8 mục 1.2(a) và 1.2(b).
**Ghi chú trong cờ:** Bảng 2 ghi MAU/creatinin niệu 3–6 tháng/lần (chặt hơn); hệ thống dùng mốc "ít nhất mỗi năm" của Phần 8 mục 1.2(a) cho theo dõi định kỳ ngoại trú. Xem docs/14 §4(c).

### R-GAP-04 — Không có bộ lipid > 365 ngày
**Điều kiện:** ¬`∃ LDL-C hoặc bộ lipid` trong 365 ngày (bao gồm chưa từng có)
**Nguồn:** Phần 4 mục 6.2.1 — cần kiểm tra bộ lipid máu ít nhất hàng năm.
**Ghi chú:** Bảng 2 ghi lipid máu 1–2 tháng/lần; hệ thống dùng mốc hàng năm của mục 6.2.1 (đúng ngữ cảnh theo dõi định kỳ).

---

## 5. Ba quy tắc chất lượng dữ liệu (⚪ Xám, nguồn hệ thống)

### R-DATA-01 — Mâu thuẫn giữa hai nguồn
**Điều kiện:** ∃ 2 Observation cùng mã LOINC, thuộc 2 Organization khác nhau, `|t1 − t2| ≤ 7 ngày`, và `|v1 − v2| / max(v1,v2) > 20%` sau quy đổi đơn vị.
**Phạm vi áp dụng (chốt):** chỉ HbA1c, creatinin, eGFR, LDL-C, cholesterol toàn phần, cân nặng. **Loại trừ glucose** (dao động sinh lý trong ngày vượt 20% là bình thường → sẽ phá chỉ tiêu B2 ≥ 90%) và loại trừ huyết áp.
**Nguồn:** quy tắc chất lượng dữ liệu của hệ thống (không có trong QĐ 5481).
**Test:** HbA1c 7,0 và 9,0 cách 3 ngày, 2 cơ sở → kích (lệch 22%) · HbA1c 7,0 và 8,0 → không kích (12,5%) · glucose 5,0 và 9,0 → không kích (ngoài phạm vi) · cùng một cơ sở → không kích.

### R-DATA-02 — Nghi sai đơn vị glucose
**Điều kiện:** (a) `glucose > 30` ∧ (không có đơn vị ∨ đơn vị mmol/L) → nghi giá trị thực là mg/dL; hoặc (b) `glucose < 1,5` ∧ đơn vị mg/dL → nghi nhập nhầm.
Hệ thống **không tự quy đổi**, chỉ nêu nghi ngờ và không dùng giá trị đó cho R-LAB-01/02.
**Nguồn:** quy tắc chất lượng dữ liệu của hệ thống.
**Ghi chú:** giá trị trên 30 mmol/L vẫn có thể là thật (tăng áp lực thẩm thấu — QĐ 5481 Phần 7 nêu mức trên 33,3 mmol/L); vì vậy nhãn là Xám "cần xác minh", không phải cờ an toàn.
**Test:** 180 không đơn vị → kích · 180 mg/dL → không kích · 0,9 mg/dL → kích · 28 mmol/L → không kích.

### R-DATA-03 — Dị ứng lệch giữa các nguồn
**Điều kiện:** ∃ AllergyIntolerance ghi ở Organization A, không có bản ghi tương ứng ở Organization B, **và** B có ≥ 1 MedicationRequest được kê sau ngày ghi dị ứng ở A.
**Nguồn:** quy tắc chất lượng dữ liệu của hệ thống.
**Đã thu hẹp so với PRD 1.1:** điều kiện "B từng kê thuốc sau ngày biết dị ứng" ngăn cờ nổ trên mọi bệnh nhân có ≥ 2 nguồn (lý do: giữ B3 trung bình ≤ 3).
**Test:** dị ứng ghi ở A 01/2026, B kê thuốc 05/2026 → kích · B không kê thuốc nào sau đó → không kích · cả hai nguồn đều có bản ghi → không kích.

---

## 6. Ba quy tắc P1 (làm sau khi P0 xanh)
| Mã | Điều kiện | Nhãn | Nguồn |
|---|---|---|---|
| R-DDI-01 | ∃ cặp thuốc đang dùng thuộc danh sách "major" trong bảng con DDInter (~25 hoạt chất) | 🟡 Vàng | DDInter 2.0 (CC BY-NC-SA 4.0) |
| R-TARGET-01 | HA tâm thu ≥ 140 ∨ tâm trương ≥ 90 ở ≥ 2 lần đo liên tiếp gần nhất; ngưỡng < 130/80 nếu có N18* hoặc nguy cơ tim mạch xơ vữa cao | 🟡 Vàng | Bảng 4 — dòng Huyết áp |
| R-TARGET-02 | LDL-C gần nhất ≥ 2,6 mmol/L (chưa có biến chứng tim mạch) hoặc ≥ 1,8 mmol/L (đã có bệnh tim mạch xơ vữa: I20–I25, I60–I69) | 🟡 Vàng | Bảng 4 — dòng Lipid máu; mục 6.2.2 |

---

## 7. Vì sao không làm "Red-Flag Triage" như đề gốc
1. Triage là phân loại mức độ khẩn cấp khi gặp bệnh nhân; hệ thống chỉ thấy dữ liệu cũ **trước** khi bác sĩ gặp bệnh nhân, không có sinh hiệu tại thời điểm khám.
2. Tự phân loại khẩn cấp đẩy hệ thống sang vai trò ra quyết định lâm sàng — trái nguyên tắc "AI không thay thế thẩm quyền và trách nhiệm con người" của Luật Trí tuệ nhân tạo 134/2025/QH15 (xem docs/24).
3. Cảnh báo quá nhiều bị bỏ qua trên thực tế (y văn về override cảnh báo tương tác thuốc), làm mất giá trị của cả bộ cờ.
4. **Đã rà toàn văn QĐ 5481:** văn bản không quy định ngưỡng báo động ngoại trú cho tăng huyết áp cấp cứu, kali máu, hay HbA1c; các ngưỡng cấp cứu chỉ nằm ở Phần 7 (biến chứng cấp, bối cảnh nội trú). Nếu LLM hoặc đội tự đặt ngưỡng thì không trích dẫn được — vi phạm Nguyên tắc 3.

---

## 8. Kiểm thử bắt buộc
Mỗi quy tắc ≥ 5 test: dương chuẩn, âm chuẩn, biên đúng ngưỡng, dữ liệu quá tuổi, đơn hết hiệu lực. File: `tests/rules/test_<rule_id>.py`. CI chặn merge nếu: thiếu test cho quy tắc P0, hoặc B1 < 100% trên bộ tình huống cài sẵn.
Kiểm thử bổ sung cấp hệ thống: `test_no_rule_calls_network` (quy tắc không được gọi I/O), `test_flag_payload_schema`, `test_b3_counting` (gộp nhóm đúng §0.8).

## 9. Phiên bản
`rules_v1.0.0` ứng với tài liệu này. Đổi bất kỳ ngưỡng nào → tăng MINOR và chạy lại toàn bộ benchmark nhóm B. Mỗi bản tóm tắt lưu `rules_version` để tái tạo.
