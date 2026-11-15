# Trợ lý tóm tắt hồ sơ trước lượt tái khám — Brief

Mã đề: VMEC-03 · Đội: P-XXX · Phiên bản: 1.2 (15/11/2026) · Demo Day: 13/12/2026

## Bài toán
Bác sĩ ngoại trú khám nhiều bệnh nhân đái tháo đường (ĐTĐ) típ 2 trong thời gian ngắn, trong khi hồ sơ của mỗi người rải qua nhiều lần khám và nhiều cơ sở. Đọc lại toàn bộ thì không khả thi, còn đọc vội thì dễ bỏ sót xu hướng xấu, chống chỉ định theo chức năng thận, dị ứng ghi ở nơi khác và xét nghiệm định kỳ quá hạn. Kết quả: chỉ khoảng 36% bệnh nhân típ 2 đạt mục tiêu HbA1c (SK&ĐS, 11/2026).

Bối cảnh hạ tầng đã chín: theo Thông tư 13/2025/TT-BYT, bệnh viện phải hoàn thành hồ sơ bệnh án điện tử chậm nhất 30/9/2025, các cơ sở khám chữa bệnh khác chậm nhất 31/12/2026. Dữ liệu để đọc đã có dạng số; vấn đề còn lại là thời gian đọc.

## Người dùng mục tiêu
| Vai trò | Cần gì | Đo bằng gì |
|---|---|---|
| **1. Bác sĩ điều trị** — Nội tiết hoặc Nội tổng quát, khám ngoại trú tại bệnh viện tỉnh/thành phố đã có bệnh án điện tử | Nắm hồ sơ nhanh, kiểm chứng được từng chi tiết, không bị "dạy", không thêm việc | Thời gian đọc hồ sơ; tỷ lệ phát hiện lỗi khi có / không có panel nguồn; 0 lỗi mức nặng |
| **2. Quản trị IT** — bệnh viện hoặc đội dự án | Phân quyền đúng bác sĩ – bệnh nhân, truy vết được mọi thao tác | Nhật ký đủ mọi lượt xem / sửa / duyệt; không có đường lưu bản chưa duyệt |

Bệnh nhân là chủ thể dữ liệu, không phải người dùng; hệ thống không có đầu ra cho bệnh nhân.

## Giải pháp
Một AI Agent dựng bản tóm tắt tiếng Việt có cấu trúc từ hồ sơ FHIR R4 đa nguồn của bệnh nhân ĐTĐ típ 2, mọi câu đều bấm vào được nguồn, để bác sĩ sửa và duyệt trước lượt tái khám.

- Dựng dòng thời gian; chuẩn hóa thuốc về hoạt chất và xét nghiệm về đơn vị chuẩn bằng code xác định.
- Chạy 17 quy tắc an toàn xác định để sinh cờ: 11 quy tắc có ngưỡng trích từ QĐ 5481/QĐ-BYT (đã đối chiếu toàn văn, kèm QĐ 1353/QĐ-BYT 2021), 6 quy tắc an toàn thuốc và chất lượng dữ liệu theo nguồn hệ thống được ghi rõ. Mỗi cờ có mã quy tắc và vị trí trích dẫn; AI không tham gia bước này.
- LLM viết 8 mục tiếng Việt, mỗi câu trỏ về dữ liệu gốc. Bộ kiểm tra chặn câu không nguồn, số không khớp hoặc có cụm từ chỉ định; câu bị chặn chuyển sang mục "chưa xác minh được".
- Bác sĩ xem nguồn, xử lý cờ, sửa và duyệt. Bản duyệt được khóa, ghi nhật ký và xuất được FHIR Composition.

## Vì sao cần AI
**Bắt buộc có LLM: chỉ một bước**, là viết bản tóm tắt tiếng Việt 8 mục từ dữ liệu có cấu trúc và ghi chú tiếng Việt tự do của nhiều lần khám. Quy tắc cố định không đọc và diễn đạt lại được ghi chú lâm sàng.

**Chỉ là phần mềm thường: 9 trong 10 thành phần** — chuẩn hóa dữ liệu, dòng thời gian, 17 quy tắc cờ, kiểm tra nguồn và số, phân quyền, nhật ký, lưu vết phiên bản, xuất Composition, giao diện.

Chủ đích là kiến trúc "xác định trước, LLM sau": bỏ LLM thì hệ thống vẫn dựng được dòng thời gian và cờ, nhưng mất bản tóm tắt, tức là giá trị chính với bác sĩ.

## Không làm (Non-goals) trong 31 ngày
- Không chẩn đoán, kê đơn, đề xuất thuốc hay phân loại khẩn cấp (triage).
- Không có đầu ra cho bệnh nhân; không dùng dữ liệu thật, không tích hợp HIS thật, không có chức năng tải hồ sơ lên.
- Không hỏi đáp tự do trên hồ sơ, không ghi nhớ theo bệnh nhân.
- Ngoài phạm vi ĐTĐ típ 2 ngoại trú: không làm típ 1, thai kỳ, nhi khoa, nội trú, cấp cứu.
- Không xử lý hình ảnh, ECG, thiết bị đeo, OCR giấy/scan.

## Đo thành công bằng gì
- **Trung thực với hồ sơ:** không câu nào thiếu nguồn được hiển thị như sự thật (A4 = 100%) · câu không được nguồn hỗ trợ ≤ 2% (A1) · độ chính xác trích dẫn ≥ 95% (A3) · bỏ sót fact bắt buộc ≤ 10% (A2).
- **Cờ an toàn:** recall = 100% trên tình huống cài sẵn (B1) · precision ≥ 90% (B2) · trung bình ≤ 3 cờ mỗi bệnh nhân (B3).
- **Chạy được thật:** p95 ≤ 60 giây mỗi bản · URL công khai chạy end-to-end trên 30 bệnh nhân mô phỏng.

---
Toàn bộ dữ liệu trong dự án là mô phỏng · Scope A đã duyệt 12/11/2026 · Hạn hạ tầng demo (máy ảo + tên miền): 1/12/2026 · Chi tiết: PRD, Wireframe/UI Flow, SRS, Thiết kế hệ thống, Rule Spec
