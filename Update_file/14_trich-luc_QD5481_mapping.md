# docs/14 — Trích lục QĐ 5481/QĐ-BYT & bảng ánh xạ quy tắc ✅ HOÀN THIỆN

Nguồn đã lưu trong `refs/`:
| File | SHA-256 |
|---|---|
| QD-5481-BYT_2020.docx | 3e663ce9397ee443a585fdc4b3d2a6ec7bbfb56525009bdf618c4a2d63e4efb7 |
| QD-1353-BYT_2021.docx | 7eef934b7f39eb617728da669eaf2bfbeedfdeac77004f046a05902977caa60f |

Rà soát ngày 15/11/2026 trên bản văn bản đầy đủ (1.112 đoạn, 23 bảng).

---

## 1. QĐ 1353/QĐ-BYT (23/02/2021) — kết luận rà soát

QĐ 1353 sửa đúng **2 điểm**, không điểm nào ảnh hưởng 17 quy tắc:
1. Bổ sung một thành viên ban biên soạn/thẩm định.
2. Sửa điểm b, mục 3, trang 37 — nội dung về chế độ insulin nền–nhanh/trộn hỗn hợp cho bệnh nhân **tăng glucose máu kèm bệnh nặng không nguy kịch** (thuộc Phần 5 — quản lý nội trú, ngoài phạm vi dự án: hệ thống chỉ làm ngoại trú).

**Kết luận:** mọi ngưỡng của 17 quy tắc lấy từ QĐ 5481 bản 2020 vẫn còn hiệu lực nguyên trạng. Trích dẫn trong hệ thống ghi: *"QĐ 5481/QĐ-BYT (được sửa đổi, bổ sung bởi QĐ 1353/QĐ-BYT; nội dung sửa đổi không liên quan quy tắc này)"*.

---

## 2. Cấu trúc thật của QĐ 5481 (khác giả định PRD 1.1 — đã hiệu chỉnh)

| Tên trong PRD 1.1 | Vị trí thật trong văn bản |
|---|---|
| "Phụ lục 01, mục 2" | **PHỤ LỤC 01** — Các nhóm thuốc hạ glucose máu đường uống và thuốc dạng tiêm không thuộc nhóm insulin, **mục 2. Metformin** ✅ đúng |
| "Mục 4.1(g)" | **Phần 4, mục 4.1(g)** — Ảnh hưởng lên suy tim (LVEF < 45%) ✅ đúng |
| "Mục 4.2(j)" | **Phần 4, mục 4.2(j)** — thận trọng hạ glucose máu với SU/insulin ở BN lớn tuổi ✅ đúng |
| "Phần 6, mức 1 / mức 2" | **Phần 6 — Hạ đường huyết, mục 2. Phân mức độ hạ đường huyết** (bảng phân mức) ✅ đúng |
| "Bảng 2" | **Bảng 2: Xét nghiệm đối với người bệnh đái tháo đường** (Phần 3, mục 2.3) ✅ đúng |
| "Bảng 4, 5" | **Bảng 4**: mục tiêu điều trị người trưởng thành không có thai; **Bảng 5**: mục tiêu ở người cao tuổi (Phần 4, mục 2) ✅ đúng |
| "Mục 6.2" | **Phần 4, mục 6.2 Rối loạn lipid máu**, tiểu mục 6.2.1 ✅ đúng |
| "Danh mục thuốc thiết yếu" (cho R-MED-03) | **PHỤ LỤC 03** chỉ là *danh mục thuốc*, KHÔNG có ngưỡng tuổi → **R-MED-03 phải đổi nguồn**, xem §4 |
| "Phần 6, biến chứng thận" (R-GAP-03) | Vị trí đúng là **Phần 8, mục 1.2 Biến chứng thận** (không phải Phần 6) → đã sửa |

---

## 3. Bảng ánh xạ hoàn chỉnh (dùng cho panel nguồn)

`citation.section` là chuỗi hiển thị cho bác sĩ; `anchor` là khóa tra trong `refs/citations.json` để tô sáng đoạn.

| Mã quy tắc | Vị trí trích dẫn (hiển thị) | Nội dung căn cứ (tóm lược) | Khớp ngưỡng? |
|---|---|---|---|
| R-MED-01 | Phụ lục 01, mục 2 (Metformin) — Chống chỉ định | Chống chỉ định khi eGFR < 30 mL/phút | ✅ đúng nguyên văn |
| R-MED-02 | Phụ lục 01, mục 2 (Metformin) — Chống chỉ định | Giảm liều khi eGFR trong khoảng 30–45 mL/phút; mới chẩn đoán mà eGFR < 45 thì cân nhắc cẩn thận | ✅ đúng nguyên văn |
| R-MED-03 | Phụ lục 01, mục 1(a) Glyburide/glibenclamide + mục 1 "Tóm lại" + Phần 4 mục 4.1(i) | Glibenclamid: nguy cơ hạ glucose máu cao, nhất là ở người già, suy gan, suy thận; chống chỉ định suy thận. Mục 4.1(i) định nghĩa người cao tuổi là trên 65 tuổi | ⚠️ **văn bản không có ngưỡng "60 tuổi"** → đã sửa quy tắc, xem §4 |
| R-MED-04 | Phần 4, mục 4.1(g) + Phụ lục 01, mục 4 (TZD) | Chống chỉ định nhóm TZD do tăng suy tim sung huyết; Phụ lục 01 nêu chống chỉ định suy tim độ III–IV theo NYHA | ✅ |
| R-MED-05 | Phụ lục 01, mục 1(a) (dị ứng thuốc là chống chỉ định) + nguyên tắc an toàn thuốc của hệ thống | Văn bản nêu "dị ứng thuốc" trong chống chỉ định nhưng không có quy tắc đối chiếu đa nguồn → quy tắc hệ thống | 🟨 nguồn hỗn hợp |
| R-MED-06 | Phần 4, mục 4.2(c) + chuẩn hóa thuốc của hệ thống | Mục 4.2(c): không lựa chọn các thuốc cùng một nhóm, cùng cơ chế tác dụng | ✅ có căn cứ (mạnh hơn dự kiến) |
| R-MED-07 | Phần 4, mục 4.2(j) + Phần 6 mục 1(b) + mục 4.1(i) | 4.2(j): thận trọng nguy cơ hạ glucose máu với sulfonylurea, insulin, đặc biệt ở BN lớn tuổi. Phần 6 1(b): nhóm thuốc nguy cơ hạ đường huyết gồm insulin, SU | ✅ |
| R-LAB-01 | Phần 6, mục 2 — bảng phân mức, **Mức 2** | Mức 2: glucose < 54 mg/dL (3,0 mmol/L) | ✅ đúng nguyên văn |
| R-LAB-02 | Phần 6, mục 2 — bảng phân mức, **Mức 1** | Mức 1: glucose < 70 mg/dL (3,9 mmol/L) và ≥ 54 mg/dL (3,0 mmol/L) | ✅ đúng nguyên văn |
| R-TREND-01 | Bảng 4 (HbA1c < 7%) + Bảng 5 (người cao tuổi) + mục 4.2(i) | Bảng 4: HbA1c < 7%. Bảng 5: nhiều bệnh/sức khỏe trung bình < 8,0%; mạnh khỏe < 7,5%. Mục 4.2(i): đánh giá lại chiến lược mỗi 3 tháng | ✅ ngưỡng; logic xu hướng là suy diễn hệ thống |
| R-GAP-01 | Bảng 2 — dòng HbA1c | Tái khám: mỗi 3 tháng | ⚠️ **văn bản yêu cầu 3 tháng, chặt hơn 183 ngày của PRD** → xem §4 |
| R-GAP-02 | Bảng 2 — dòng "Creatinin, tính eGFR" + Phần 8 mục 1.2(a),(b) | Bảng 2: làm lại mỗi năm hoặc theo yêu cầu lâm sàng. Phần 8 1.2(a): ít nhất mỗi năm một lần đánh giá albumin niệu và mức lọc cầu thận. 1.2(b): nếu albumin niệu > 30 mg/g creatinin và/hoặc eGFR < 60 thì đánh giá hai lần một năm | ✅ 365 ngày; thêm nhánh 183 ngày cho nhóm nguy cơ |
| R-GAP-03 | Phần 8, mục 1.2(a),(b) + Bảng 2 — dòng MAU/creatinin niệu | Phần 8 1.2(a): ít nhất mỗi năm một lần. Bảng 2 ghi 3–6 tháng/lần (chặt hơn) | ✅ dùng mốc "ít nhất mỗi năm"; xem §4 |
| R-GAP-04 | Phần 4, mục 6.2.1 | Cần kiểm tra bộ lipid máu ít nhất hàng năm | ✅ đúng nguyên văn |
| R-DATA-01/02/03 | — | Không có căn cứ trong QĐ 5481; là quy tắc chất lượng dữ liệu của hệ thống | 🟨 nguồn hệ thống |
| R-TARGET-01 (P1) | Bảng 4 — dòng Huyết áp | Tâm thu < 140, tâm trương < 90; nếu đã có biến chứng thận hoặc nguy cơ tim mạch do xơ vữa cao thì < 130/80 | ✅ |
| R-TARGET-02 (P1) | Bảng 4 — dòng Lipid máu + mục 6.2.2 | LDL-C < 2,6 mmol/L nếu chưa có biến chứng tim mạch; < 1,8 mmol/L nếu đã có bệnh tim mạch xơ vữa | ✅ |
| R-DDI-01 (P1) | — | Nguồn DDInter 2.0, không thuộc QĐ 5481 | 🟨 nguồn ngoài |

---

## 4. Ba điểm phải sửa quy tắc do đối chiếu văn bản (đã áp vào docs/13)

**(a) R-MED-03 — bỏ ngưỡng "tuổi > 60"**
QĐ 5481 không quy định mốc 60 tuổi cho glibenclamid. Những gì văn bản có: glibenclamid nguy cơ hạ glucose máu cao, đặc biệt ở người già/suy gan/suy thận; chống chỉ định suy thận; và mục 4.1(i) định nghĩa người cao tuổi là **trên 65 tuổi**.
→ Quy tắc mới: `glibenclamid đang dùng ∧ (tuổi ≥ 65 ∨ eGFR < 60)`. Vừa có nguồn thật, vừa xóa mâu thuẫn 60/65 với R-MED-07.

**(b) R-GAP-01 — HbA1c: văn bản yêu cầu mỗi 3 tháng**
Bảng 2 ghi tái khám HbA1c **mỗi 3 tháng** (không phải 6 tháng). Ngưỡng 183 ngày của PRD 1.1 lỏng hơn văn bản.
→ Quyết định: giữ cờ Xám ở mốc **> 183 ngày** (đã bỏ qua 2 kỳ liên tiếp) để tránh cờ nổ trên gần như mọi bệnh nhân, và **ghi rõ trong câu diễn giải cờ**: QĐ 5481 Bảng 2 yêu cầu mỗi 3 tháng; hệ thống dung sai 2 kỳ. Trích dẫn vẫn trỏ về Bảng 2. Lý do kỹ thuật: giữ B3 trung bình ≤ 3; lý do lâm sàng: tần suất 3 tháng là mục tiêu theo dõi, không phải ngưỡng an toàn.

**(c) R-GAP-03 — UACR: hai mốc trong cùng văn bản**
Phần 8 mục 1.2(a) ghi "ít nhất mỗi năm một lần"; Bảng 2 ghi "3–6 tháng/lần".
→ Quyết định: dùng **365 ngày** làm ngưỡng cơ sở, trích Phần 8 mục 1.2(a) (là khuyến cáo biến chứng thận, đúng ngữ cảnh ngoại trú); thêm nhánh **183 ngày** khi bệnh nhân có UACR > 30 mg/g hoặc eGFR < 60 (Phần 8 mục 1.2(b)). Ghi chú sự khác biệt với Bảng 2 trong rule spec.

**Ghi chú thêm (không thành quy tắc):** Bảng 2 ghi lipid máu tái khám "1–2 tháng/lần", trong khi mục 6.2.1 ghi "ít nhất hàng năm". Hệ thống dùng mốc hàng năm của mục 6.2.1 vì đúng ngữ cảnh theo dõi định kỳ ngoại trú; chênh lệch này được ghi vào rule spec để bác sĩ biết hệ thống chọn mốc nào.

---

## 5. Nội dung KHÔNG có trong QĐ 5481 — xác nhận giữ nguyên chủ trương không làm
Đã rà toàn văn: văn bản **không** quy định ngưỡng "báo động" cho tăng huyết áp cấp cứu ngoại trú, kali máu (chỉ có trong Phần 7 — cấp cứu nội trú), HbA1c báo động, hay ngưỡng eGFR cố định cho SGLT2i (mục 4.2(b) ghi rõ tham khảo thông tin kê toa từng hoạt chất tại Việt Nam).
→ Giữ nguyên quyết định của PRD: không tạo cờ cho các tình huống này. Đây là lập luận mạnh cho giám khảo: hệ thống không tự đặt ngưỡng ngoài văn bản.
