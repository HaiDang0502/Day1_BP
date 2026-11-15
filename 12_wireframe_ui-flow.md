# docs/12 — Wireframe / UI Flow v1.0 🟡 (mô tả chữ, vẽ hi-fi theo đây)

Chung mọi trang sau đăng nhập: banner vàng cố định "⚠ DỮ LIỆU MÔ PHỎNG — Bác sĩ rà soát trước khi sử dụng" + dòng nhỏ "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" ở vùng nội dung AI. Desktop ≥ 1280 px. Tiếng Việt 100%. WCAG AA: tương phản, focus ring, điều hướng bàn phím, icon cờ kèm chữ (không chỉ màu).

## S1. Đăng nhập
Form email + mật khẩu. Lỗi: thông báo chung "Sai thông tin đăng nhập".

## S2. Danh sách bệnh nhân (doctor)
Bảng: Họ tên | Năm sinh | Lần khám gần nhất | Tái khám dự kiến (as_of) | Trạng thái tóm tắt (— / Nháp / Đã duyệt / Thất bại). Chỉ bệnh nhân được gán. Hàng → S3.

## S3. Trang bệnh nhân
Nút chính: [Tạo bản tóm tắt] (hoặc [Tạo lại] — không ghi đè bản đã duyệt). Khi chạy: progress 9 bước tiếng Việt ("Đang chuẩn hóa thuốc… 3/9"), hủy được. Lỗi: "Không tạo được tóm tắt" + [Tạo lại]. Danh sách bản cũ (chỉ đọc).

## S4. Trang tóm tắt (màn hình trung tâm)
- Cột trái (70%): 8 mục theo thứ tự Phụ lục C; trên cùng là "5 dòng chính". Mỗi câu = 1 claim có chip nguồn (①②…); câu "chưa xác minh được" nền xám, không chip, gom cuối trang (ẩn khi rỗng). Mục 7 "Cần xác minh / thiếu" luôn hiện.
- Cột phải (30%): 3 tab:
  - **Tab Cờ** (badge số cờ Đỏ): nhóm theo sự kiện (rule-spec §0.4); mỗi cờ: 🔴/🟡/⚪ + mã + câu diễn giải (kết thúc "— cần bác sĩ xem xét.") + [Trích dẫn QĐ 5481] mở đúng đoạn + dữ liệu kích hoạt + nút [Đã xem xét] (lý do tùy chọn). Hiển thị 7, [xem thêm].
  - **Tab Nguồn**: panel chi tiết khi click câu — resource gốc, cơ sở, ngày; ghi chú tô sáng đúng `#char=a-b`; < 300 ms.
  - **Tab Xu hướng (P1)**: chart HbA1c/eGFR/HA/cân nặng/LDL + đường mục tiêu; hover = giá trị, ngày, cơ sở.
- Thanh dưới: [Sửa câu] (inline, draft only) · [Duyệt] — disabled kèm tooltip đến khi: đã mở tab Cờ ∧ mọi cờ Đỏ đã ack. Bấm Duyệt → modal xác nhận hiện: toàn văn (gồm phần vừa sửa), danh sách cờ, mục thiếu, câu chưa xác minh → [Xác nhận duyệt]. Sau duyệt: khóa toàn trang, nhãn "ĐÃ DUYỆT bởi BS… lúc…", [Xuất Composition/PDF] (P1).

## S5. Timeline (P1)
Trục thời gian; filter loại (khám/XN/thuốc/dị ứng/ghi chú) + nguồn (cơ sở); click sự kiện → panel nguồn.

## S6. Admin
Tab Gán bệnh nhân: chọn doctor + patient, modal xác nhận hiện "BS A sẽ thấy BN B" → lưu + audit. Tab Nhật ký: bảng lọc theo người/hành động/bệnh nhân/khoảng ngày; không hiển thị nội dung hồ sơ.

## Luồng lỗi hiển thị
403 → "Bạn không có quyền xem hồ sơ này" (đồng thời audit denied) · 409 → "Bản đã duyệt bị khóa. Tạo bản mới để sửa." · LLM quá tải → "Dịch vụ AI tạm thời không dùng được, thử lại sau" (bản đã duyệt vẫn xem được).
