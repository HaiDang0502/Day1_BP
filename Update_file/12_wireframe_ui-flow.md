# docs/12 — Wireframe / UI Flow v2.0 ✅ ĐÃ CHỐT (mô tả để vẽ hi-fi)

## Quy ước chung
Mọi trang sau đăng nhập có:
- Banner cố định, không đóng được: **"⚠ DỮ LIỆU MÔ PHỎNG — Bác sĩ rà soát trước khi sử dụng"**.
- Ở đầu vùng nội dung AI: **"Bản nháp do AI soạn — bác sĩ xem xét và duyệt"** (minh bạch theo Luật AI 134/2025).
Desktop ≥ 1280 px, không tối ưu di động. Tiếng Việt 100%, thuật ngữ theo docs/19. WCAG AA: tương phản đạt chuẩn, focus ring rõ, điều hướng bàn phím đủ cho luồng xem cờ → ack → duyệt, **icon cờ kèm chữ** (🔴 "An toàn thuốc" / 🟡 "Cần xem xét" / ⚪ "Thiếu dữ liệu") để màu không là kênh thông tin duy nhất.

## S1 — Đăng nhập
Form email + mật khẩu. Lỗi: "Sai thông tin đăng nhập" (không tiết lộ email có tồn tại). Không có đăng ký, không có quên mật khẩu ở MVP (admin cấp lại).

## S2 — Danh sách bệnh nhân (`doctor`)
Bảng: Họ tên · Năm sinh · Lần khám gần nhất · Tái khám dự kiến (`as_of`) · Trạng thái tóm tắt (— / Nháp / Đã duyệt / Thất bại). Chỉ hiện bệnh nhân được gán. Sắp xếp mặc định theo ngày tái khám gần nhất. Click hàng → S3.

## S3 — Trang bệnh nhân
Thông tin hành chính tối thiểu (tên, năm sinh, giới). Nút chính **[Tạo bản tóm tắt]** (hoặc **[Tạo lại]** nếu đã có bản).
Khi đang chạy: thanh tiến trình 9 bước, nhãn tiếng Việt theo bước ("Đang chuẩn hóa thuốc… 3/9"), có nút Hủy.
Khi lỗi: "Không tạo được tóm tắt" + nút [Tạo lại]; nếu lỗi do dịch vụ AI: "Dịch vụ AI tạm thời không dùng được, thử lại sau".
Danh sách bản cũ (chỉ đọc) kèm ngày, người duyệt.

## S4 — Trang tóm tắt (màn hình trung tâm)
**Cột trái (~70%)** — 8 mục theo thứ tự Phụ lục C:
- Trên cùng: khối **"5 dòng chính"** nổi bật, đọc trong 10 giây.
- Mỗi câu là một claim, cuối câu có chip nguồn ①②③ bấm được.
- Mục 7 "Cần xác minh / thiếu" **luôn hiện** (kể cả khi rỗng: "Không phát hiện thiếu dữ liệu theo QĐ 5481").
- Cuối trang: khối **"Chưa xác minh được"** — nền xám, không chip nguồn, kèm một dòng giải thích ngắn; **ẩn khi rỗng**.
- Nút [Sửa] inline trên từng câu (chỉ ở trạng thái Nháp).

**Cột phải (~30%)** — 3 tab:
1. **Tab Cờ** (badge số cờ Đỏ chưa ack): danh sách **nhóm** cờ theo sự kiện (docs/13 §0.8). Mỗi cờ hiện: nhãn + mã quy tắc · **loại nguồn** (Văn bản QĐ 5481 / Quy tắc hệ thống / Ý kiến chuyên gia) · câu diễn giải kết thúc "— cần bác sĩ xem xét." · nút **[Xem trích dẫn]** mở đoạn văn bản được tô sáng · dữ liệu kích hoạt (resource, giá trị, ngày, cơ sở) · nút **[Đã xem xét]** (ô lý do tùy chọn). Hiển thị 7 nhóm, nút [xem thêm]. Cờ Xám thu gọn mặc định.
2. **Tab Nguồn**: panel chi tiết khi click một câu — resource gốc, cơ sở, ngày; với ghi chú thì hiện đoạn `#char=a-b` được tô sáng trong ngữ cảnh. Mục tiêu < 300 ms.
3. **Tab Xu hướng** (P1): biểu đồ HbA1c / eGFR / HA / cân nặng / LDL kèm **đường mục tiêu** theo Bảng 4 và Bảng 5; hover hiện giá trị, ngày, cơ sở nguồn.

**Thanh dưới (sticky):**
**[Duyệt]** — **disabled** kèm tooltip nêu điều kiện còn thiếu cho đến khi: đã mở tab Cờ **và** mọi cờ Đỏ đã ack.
Bấm Duyệt → modal xác nhận hiện: toàn văn (gồm phần bác sĩ vừa sửa), từng câu kèm nguồn, danh sách cờ, mục "cần xác minh / thiếu", các câu "chưa xác minh được" → **[Xác nhận duyệt]**.
Sau duyệt: toàn trang chuyển chế độ đọc, nhãn "ĐÃ DUYỆT bởi BS. … lúc dd/mm/yyyy hh:mm", hiện **[Xuất Composition]** và **[Xuất PDF]** (P1). Mọi nút sửa và ack biến mất (bản khóa toàn phần).

## S5 — Timeline (P1)
Trục thời gian dọc; filter theo loại (khám / xét nghiệm / thuốc / dị ứng / ghi chú) và theo nguồn (cơ sở). Click sự kiện → panel nguồn. Sự kiện ngừng thuốc hiện đúng như dữ liệu (`status = stopped`) — không bị bộ chặn cụm từ (SRS FR-05 miễn trừ).

## S6 — Admin
- **Tab Gán bệnh nhân:** chọn bác sĩ + bệnh nhân → modal xác nhận hiện rõ "BS. A sẽ thấy hồ sơ của bệnh nhân B" → lưu + ghi nhật ký.
- **Tab Nhật ký:** bảng lọc theo người / hành động / bệnh nhân / khoảng ngày. **Không** hiển thị nội dung hồ sơ — chỉ ai, làm gì, lúc nào, trên bản nào.

## Thông báo lỗi chuẩn
| Mã | Nội dung người dùng thấy |
|---|---|
| 403 | "Bạn không có quyền xem hồ sơ này." (đồng thời ghi nhật ký `denied`) |
| 409 | "Bản đã duyệt bị khóa. Tạo bản mới để sửa." |
| 422 (duyệt khi còn cờ Đỏ) | "Còn N cờ an toàn chưa được xem xét." |
| job failed | "Không tạo được tóm tắt." + [Tạo lại] |
| LLM không phản hồi | "Dịch vụ AI tạm thời không dùng được, thử lại sau." |
