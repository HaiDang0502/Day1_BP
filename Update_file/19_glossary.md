# docs/19 — Glossary tiếng Việt v2.0 ✅

Thuật ngữ bám QĐ 5481/QĐ-BYT. File này được **nhúng vào prompt** của LLM (docs/22) và dùng cho UI.

## 1. Thuật ngữ bắt buộc
| Dùng | Không dùng | Ghi chú |
|---|---|---|
| đái tháo đường típ 2 | tiểu đường, type 2, ĐTĐ2 | sau lần đầu được viết tắt "ĐTĐ típ 2" |
| glucose huyết tương lúc đói | đường đói, đường huyết đói | đơn vị mmol/L |
| HbA1c | A1c, HbA1C | đơn vị % |
| mức lọc cầu thận ước tính (eGFR) | GFR ước đoán, độ thanh thải | mL/phút/1,73m² |
| hạ đường huyết mức 1 / mức 2 / mức 3 | hạ đường huyết nhẹ / nặng | theo phân mức Phần 6 QĐ 5481 |
| thuốc hạ glucose huyết | thuốc hạ đường, thuốc tiểu đường | |
| sulfonylurea (SU) | thuốc kích thích tiết insulin | |
| chống chỉ định | không được dùng, cấm dùng | chỉ xuất hiện trong diễn giải cờ, kèm trích dẫn |
| biến chứng thận do đái tháo đường | bệnh thận tiểu đường | |
| albumin niệu / UACR | đạm niệu | |
| tăng huyết áp | cao huyết áp | |
| rối loạn lipid máu | mỡ máu, máu nhiễm mỡ | |
| bộ lipid máu | mỡ máu toàn phần | theo mục 6.2.1 |
| hoạt chất (biệt dược) | tên thuốc | định dạng: `metformin (Glucophage)` |
| cơ sở khám bệnh, chữa bệnh | phòng khám, bệnh viện (khi nói chung) | theo Luật Khám bệnh, chữa bệnh |
| người bệnh / bệnh nhân | khách hàng, ca | dùng nhất quán trong một bản |

## 2. Chuỗi cố định (sao chép đúng nguyên văn)
| Tình huống | Chuỗi |
|---|---|
| Thiếu dữ liệu | `không có dữ liệu` |
| Không có vấn đề khác | `Không ghi nhận vấn đề khác` |
| Không có đơn thuốc | `Không có đơn thuốc đang hiệu lực` |
| Không có dị ứng | `Không ghi nhận dị ứng trong hồ sơ` |
| Không có cờ | `Không có cờ` |
| Không thiếu dữ liệu | `Không phát hiện thiếu dữ liệu theo QĐ 5481` |
| Hậu tố mọi câu cờ | `— cần bác sĩ xem xét.` |
| Kèm mọi câu nhắc mục tiêu | `mục tiêu mặc định theo QĐ 5481, bác sĩ có thể chỉnh` |
| Nhãn minh bạch AI | `Bản nháp do AI soạn — bác sĩ xem xét và duyệt` |
| Banner dữ liệu | `DỮ LIỆU MÔ PHỎNG` |

## 3. Cấm trong văn LLM (thì khuyến nghị)
`cấp cứu` · `ngừng thuốc` · `đổi thuốc` · `nên dùng` · `đề nghị` · `chẩn đoán mới` · `khuyến cáo` + hành động · `hãy` + hành động.
**Miễn trừ** (xem SRS FR-05): câu Timeline tường thuật sự kiện có nguồn (ví dụ thuốc có `status = stopped`); đoạn trích nguyên văn từ ghi chú có `#char=a-b`; hậu tố bắt buộc của câu cờ.

## 4. Định dạng
Số: dấu phẩy thập phân — `8,2%`, `38 mL/phút/1,73m²`, `3,0 mmol/L`.
Ngày: `dd/mm/yyyy`.
Khoảng: `từ 3,0 đến dưới 3,9 mmol/L` (không dùng `3.0-3.9`).
Tên cơ sở: ghi đúng `Organization.name`, không viết tắt tự phát.

## 5. Thuật ngữ nội bộ dự án
| | |
|---|---|
| Claim | Một câu trong bản tóm tắt kèm nguồn, sự kiện tham chiếu và số liệu |
| Fact | Sự kiện có cấu trúc do code dựng, có nguồn |
| Gold | Đáp án chấm benchmark, sinh cùng dữ liệu |
| Truth | Sự thật sinh lý ẩn trước lớp nhiễu; chỉ để phân tích |
| HITL | Bác sĩ tham gia vòng quyết định: sửa và duyệt |
| `as_of` | Ngày lượt tái khám sắp tới; mọi quy tắc tính tại ngày này |
| Nhóm cờ | Tập cờ dùng chung dữ liệu kích hoạt, hiển thị gộp (docs/13 §0.8) |
| Loại nguồn | Văn bản QĐ 5481 / Quy tắc hệ thống / Ý kiến chuyên gia |
