# docs/25 — Khung hồ sơ đánh giá tác động xử lý dữ liệu cá nhân (DPIA) v2.0 ✅

Căn cứ **Điều 19 NĐ 356/2025/NĐ-CP**. Cập nhật hồ sơ khi thay đổi hoạt động xử lý (Điều 20).

> **[DEMO] Không phải lập và không phải nộp** — dự án chỉ xử lý dữ liệu mô phỏng, không có chủ thể dữ liệu (Data Card docs/17). File này là khung điền sẵn để chứng minh mức sẵn sàng (mục tiêu G5) và để dùng ngay nếu có cơ sở khám chữa bệnh hợp tác.
> Biểu mẫu chính thức theo Phụ lục NĐ 356/2025 — khi lập thật phải dùng đúng mẫu hiện hành, nội dung dưới đây là phần soạn sẵn để điền vào mẫu đó.

## 1. Bên kiểm soát / bên xử lý
- Bên kiểm soát dữ liệu: _[cơ sở khám chữa bệnh]_.
- Bên xử lý dữ liệu: _[đơn vị vận hành hệ thống]_, theo thỏa thuận xử lý dữ liệu bằng văn bản.
- Nhân sự bảo vệ dữ liệu cá nhân: _[họ tên, vị trí, đã được bồi dưỡng kiến thức theo yêu cầu của NĐ 356/2025]_.

## 2. Dữ liệu được xử lý
**Loại:** dữ liệu cá nhân **nhạy cảm** — tình trạng sức khỏe (Điều 4 NĐ 356/2025): chẩn đoán ICD-10, kết quả xét nghiệm, đơn thuốc, dị ứng, ghi chú lâm sàng; kèm dữ liệu định danh: họ tên, ngày sinh, mã định danh y tế (số định danh cá nhân theo Điều 6 NĐ 102/2025).
**Nguồn:** hồ sơ bệnh án điện tử của cơ sở (không có chức năng tải lên từ người dùng).
**Khối lượng:** _[số bệnh nhân / số bản tóm tắt mỗi tháng]_.
**Thời gian lưu:** theo quy định lưu trữ hồ sơ bệnh án; bản tóm tắt đã duyệt lưu cùng hồ sơ, bản nháp xóa sau _[N]_ ngày.

## 3. Mục đích và căn cứ xử lý
**Mục đích duy nhất:** dựng bản tóm tắt hồ sơ phục vụ chính bác sĩ điều trị của bệnh nhân đó, trước lượt tái khám. Không dùng cho mục đích khác (không huấn luyện mô hình, không phân tích thứ cấp, không chuyển cho bên thứ ba).
**Căn cứ:** phục vụ hoạt động khám bệnh, chữa bệnh theo Luật Khám bệnh, chữa bệnh 15/2023; đối chiếu các căn cứ xử lý dữ liệu nhạy cảm của Luật 91/2025 khi lập hồ sơ thật. Lưu ý Điều 26 Luật 91/2025 về cung cấp thông tin sức khỏe cho bên thứ ba.

## 4. Luồng dữ liệu
```
Bệnh án điện tử (trong hạ tầng cơ sở)
        │ chỉ đọc, theo bệnh nhân được gán
        ▼
Hệ thống VMEC-03 (trong hạ tầng cơ sở)
        │
        ├──▶ [Phương án A — KHUYẾN NGHỊ] Model ngôn ngữ nội bộ
        │       dữ liệu KHÔNG ra khỏi hạ tầng → không phát sinh chuyển xuyên biên giới
        │
        └──▶ [Phương án B] LLM cloud nước ngoài
                = chuyển dữ liệu cá nhân xuyên biên giới
                → phải lập hồ sơ đánh giá tác động chuyển dữ liệu xuyên biên giới
                  và thông báo cơ quan chuyên trách (Điều 18 NĐ 356/2025)
```
Adapter OpenAI-compatible (P1-9) đã có sẵn để chọn Phương án A mà không sửa pipeline.

## 5. Biện pháp bảo vệ (đã hiện hữu trong thiết kế)
| Yêu cầu Điều 4 khoản 2 NĐ 356/2025 | Hiện thực trong hệ thống |
|---|---|
| Phân quyền, giới hạn truy cập | RBAC `doctor`/`admin`; bác sĩ chỉ thấy bệnh nhân được gán; mọi truy cập sai quyền trả 403 và ghi nhật ký (P0-1) |
| Quy trình xử lý | Pipeline 9 bước cố định, có máy trạng thái; chỉ bản duyệt là sản phẩm (P0-3, P0-7) |
| Biện pháp bảo mật | HTTPS; JWT cookie httpOnly; bcrypt; secrets qua biến môi trường; không endpoint upload; CORS khóa domain |
| Truy vết | Nhật ký append-only mọi lượt xem/tạo/sửa/duyệt/bị từ chối; không chứa nội dung hồ sơ (P0-9) |
| Tối thiểu hóa dữ liệu | Pipeline chỉ đọc resource cần cho 8 mục; log không ghi nội dung (`make logcheck`) |
| Quyền của chủ thể dữ liệu | _[THẬT: quy trình tiếp nhận yêu cầu biết/truy cập/sửa/xóa/hạn chế xử lý qua cơ sở khám chữa bệnh, với thời hạn phản hồi theo NĐ 356/2025]_ |

## 6. Đánh giá rủi ro còn lại
| Rủi ro | Mức | Biện pháp giảm thiểu |
|---|---|---|
| Truy cập trái phép từ nội bộ | Trung bình | RBAC + nhật ký + rate limit; rà soát quyền định kỳ; admin không mặc định xem nội dung để tra nhật ký |
| Rò rỉ qua log hoặc qua LLM | Trung bình | `make logcheck` tự động; chọn Phương án A (model nội bộ) |
| Bản tóm tắt sai ảnh hưởng điều trị | Trung bình | HITL bắt buộc, verifier, cờ có trích dẫn, lưu diff AI/bác sĩ (docs/24) |
| Lưu dữ liệu vượt thời hạn | Thấp | Chính sách retention theo quy định hồ sơ bệnh án; job xóa bản nháp cũ |
| Nhân sự bảo vệ dữ liệu chưa đáp ứng điều kiện | Trung bình | _[THẬT: bố trí và bồi dưỡng nhân sự theo yêu cầu NĐ 356/2025 trước khi vận hành]_ |

## 7. Kết luận và cập nhật
_[THẬT: kết luận mức rủi ro tổng thể; người lập; người phê duyệt; ngày. Cập nhật hồ sơ khi đổi phương án LLM, đổi phạm vi dữ liệu, hoặc mở thêm vai trò người dùng — theo Điều 20 NĐ 356/2025.]_
