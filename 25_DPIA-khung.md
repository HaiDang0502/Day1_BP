# docs/25 — DPIA khung (Hồ sơ đánh giá tác động xử lý dữ liệu cá nhân) ✅ bản nháp

> Theo Điều 19 NĐ 356/2025/NĐ-CP. [DEMO] KHÔNG phải nộp (không xử lý dữ liệu cá nhân thật). File này chứng minh mức sẵn sàng (G5) và là khung điền khi có dữ liệu thật. Biểu mẫu chính thức: theo Phụ lục NĐ 356/2025 — đối chiếu mẫu khi lập thật.

## 1. Thông tin bên kiểm soát/xử lý
[THẬT: tên cơ sở KCB (bên kiểm soát), đội vận hành hệ thống (bên xử lý theo hợp đồng), nhân sự bảo vệ DLCN].

## 2. Dữ liệu xử lý
Loại: dữ liệu cá nhân nhạy cảm — tình trạng sức khỏe (Đ4 NĐ 356/2025): chẩn đoán ICD-10, kết quả xét nghiệm, đơn thuốc, dị ứng, ghi chú lâm sàng; định danh: họ tên, ngày sinh, mã định danh y tế. Nguồn: bệnh án điện tử của cơ sở. Khối lượng: [điền]. Thời gian lưu: theo quy định lưu trữ hồ sơ bệnh án.

## 3. Mục đích & căn cứ xử lý
Mục đích duy nhất: dựng bản tóm tắt phục vụ bác sĩ điều trị của chính bệnh nhân trước lượt tái khám. Căn cứ: phục vụ khám chữa bệnh theo Luật KBCB; [THẬT: đối chiếu các căn cứ Đ9 Luật 91/2025 — hoạt động KCB / đồng ý].

## 4. Luồng dữ liệu & bên liên quan
EMR → hệ thống (trong hạ tầng cơ sở) → [chọn 1: (a) model nội bộ — KHÔNG chuyển ra ngoài; (b) LLM cloud nước ngoài — PHÁT SINH chuyển xuyên biên giới → lập thêm hồ sơ CTIA Mẫu 09 + thông báo Bộ Công an theo Đ18 NĐ 356/2025]. Khuyến nghị kiến trúc: (a) — adapter P1-9 đã có.

## 5. Biện pháp bảo vệ (đã hiện hữu trong thiết kế)
Phân quyền doctor/admin theo bệnh nhân được gán (Đ4.2); nhật ký append-only mọi truy cập, không chứa nội dung hồ sơ; HTTPS, JWT httpOnly, bcrypt, secrets qua env; không chức năng tải file; dữ liệu tối thiểu hóa: pipeline chỉ đọc resource cần cho 8 mục; quyền chủ thể dữ liệu: [THẬT: quy trình tiếp nhận yêu cầu truy cập/sửa/xóa qua cơ sở KCB, thời hạn theo NĐ 356/2025].

## 6. Đánh giá rủi ro còn lại
| Rủi ro | Mức | Giảm thiểu |
|---|---|---|
| Truy cập trái phép nội bộ | TB | RBAC + audit + rate limit; rà quyền định kỳ |
| Rò rỉ qua log/LLM | TB | logcheck tự động; chọn kiến trúc (a) model nội bộ |
| Suy diễn sai ảnh hưởng điều trị | TB | HITL bắt buộc, verifier, cờ có trích dẫn (docs/24) |
| Lưu vượt thời hạn | Thấp | Chính sách retention theo quy định hồ sơ bệnh án |

## 7. Kết luận & cập nhật
[THẬT: kết luận mức rủi ro, người phê duyệt, ngày; cập nhật hồ sơ khi thay đổi xử lý theo Đ20 NĐ 356/2025.]
