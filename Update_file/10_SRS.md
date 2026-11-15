# docs/10 — SRS (Software Requirements Specification) v2.0 ✅ ĐÃ CHỐT

Phạm vi: toàn bộ P0 + P1 của PRD 1.2. Mọi quyết định treo đã chốt. Owner chính: B (backend), F (frontend), D (dữ liệu & quy tắc), E (benchmark).

---

## 1. Tác nhân & phân quyền (RBAC)
| Role | Quyền |
|---|---|
| `doctor` | GET bệnh nhân được gán; POST tạo tóm tắt; GET/PATCH bản nháp của bệnh nhân được gán; POST duyệt; POST ack cờ; GET panel nguồn; GET export (bản đã duyệt, P1) |
| `admin` | Mọi quyền `doctor` trên mọi bệnh nhân; POST/DELETE gán bệnh nhân; GET nhật ký có lọc |
| CLI (đội dự án) | Nạp dữ liệu, `make benchmark` — chạy trực tiếp trên máy chủ, không qua HTTP |

Không có role bệnh nhân. Mọi truy cập ngoài quyền trả **403** và ghi audit `denied`. Kiểm tra quyền thực hiện ở bước 0 của pipeline và ở mọi endpoint (không dựa vào ẩn nút trên UI).

---

## 2. Yêu cầu chức năng

### FR-01 — Đăng nhập (P0-1, owner B)
JWT đặt trong cookie `httpOnly; Secure; SameSite=Lax`; mật khẩu băm bcrypt (cost 12); phiên 8 giờ; không refresh token ở MVP. Rate limit đăng nhập 5 lần/phút/IP. Thông báo lỗi chung "Sai thông tin đăng nhập" (không tiết lộ email tồn tại hay không).

### FR-02 — Gán bệnh nhân (P0-1, owner B+F)
Admin chọn `doctor` + `patient`; modal xác nhận hiện rõ bác sĩ và bệnh nhân bị ảnh hưởng; lưu kèm audit. Bỏ gán cũng qua modal và audit.

### FR-03 — Pipeline 9 bước (P0-3, owner B)
Chạy nền dưới dạng job; trạng thái `queued → running(step i/9) → draft | failed`.
Các bước: (0) kiểm tra quyền → (1) đọc Bundle → (2) dựng dòng thời gian → (3) chuẩn hóa thuốc → (4) chuẩn hóa xét nghiệm → (5) chạy quy tắc (docs/13) → (6) dựng fact & event → (7) LLM soạn 8 mục → (8) verifier → (9) lưu nháp.
**Timeout cứng 90 giây** cho toàn job (chốt); vượt → `failed`, không lưu nội dung dở. Chỉ tiêu chất lượng p95 ≤ 60 giây đo trên benchmark nhóm D.
UI poll `GET /jobs/{id}` mỗi 1 giây; nhãn tiến trình tiếng Việt theo bước.

### FR-04 — Đầu ra LLM có schema (P0-4, owner B)
LLM phải trả JSON đúng schema 8 mục; mỗi claim `{text, sources[], numbers[]}`.
**Xử lý lỗi (chốt):** không parse được (sai định dạng toàn cục) → **thử lại 1 lần** với cùng prompt và cùng input; vẫn lỗi → job `failed`. Lỗi quota hoặc không phản hồi → `failed` ngay, không thử lại. Mọi lần gọi LLM ghi log token và độ trễ (không ghi nội dung hồ sơ).

### FR-05 — Verifier từng câu (P0-4/P0-5, owner B)
Ba kiểm tra độc lập cho mỗi claim:
- **(a) Nguồn tồn tại:** mọi phần tử `sources[]` phải khớp một resource trong Bundle; với `DocumentReference/id#char=a-b`, khoảng `a-b` phải nằm trong độ dài văn bản.
- **(b) Số khớp nguồn:** mọi số xuất hiện trong `text` phải có trong `numbers[]`, và mỗi phần tử `numbers[]` phải (i) khớp giá trị của resource nguồn sau quy đổi đơn vị với dung sai làm tròn 1 chữ số thập phân, **hoặc** (ii) suy ra được bằng phép tính xác định từ fact đầu vào (tuổi, thời gian mắc, số cờ, mức chênh giữa hai kỳ) — khi đó `numbers[].derived_from` phải trỏ các fact đó.
- **(c) Bộ chặn cụm chỉ định:** chặn khi khớp mẫu khuyến nghị, cụ thể: `(nên|cần|đề nghị|khuyến cáo|hãy)\s+\S*\s*(dùng|ngừng|đổi|tăng|giảm|chuyển)` hoặc cụm `chẩn đoán mới`, `cấp cứu`, `ngừng thuốc`, `đổi thuốc` **khi không nằm trong trích dẫn sự kiện có nguồn**. Miễn trừ: (i) câu mục 8 Timeline sinh từ fact `medication.status = stopped`; (ii) đoạn nằm trong dấu ngoặc kép trích từ `DocumentReference` có `#char=a-b` hợp lệ; (iii) hậu tố bắt buộc "— cần bác sĩ xem xét." của câu cờ.
Câu không qua kiểm tra → chuyển mục "Chưa xác minh được", **không xóa**, nền xám, không chip nguồn.

### FR-06 — Panel nguồn (P0-5, owner F)
Click câu → hiện resource gốc (loại, id), cơ sở (`Organization.name`), ngày; với ghi chú hiện đoạn `#char=a-b` được tô sáng trong ngữ cảnh. TTFB < 300 ms sau khi có cache. 100% claim hiển thị như sự thật phải bấm được.

### FR-07 — Cờ an toàn (P0-6, owner D+B)
Sinh theo docs/13; hiển thị gộp nhóm theo §0.8; mỗi cờ hiện mã quy tắc, **loại nguồn** (văn bản / hệ thống / ý kiến chuyên gia), trích dẫn (tra `refs/citations.json`), dữ liệu kích hoạt.
Ack từng cờ, lý do tùy chọn; cờ không xóa được.
**Chốt:** sau khi bản đã duyệt thì **không ack thêm được** — bản khóa toàn phần; muốn ack thêm phải tạo bản mới.

### FR-08 — Sửa và duyệt (P0-7, owner F+B)
Sửa inline từng câu, chỉ ở trạng thái `draft`.
**Điều kiện bật nút Duyệt (chốt):** đã mở tab Cờ **và** mọi cờ nhãn Đỏ đã được ack. Nút disabled kèm tooltip nêu điều kiện còn thiếu.
Modal xác nhận hiện: toàn văn (gồm phần vừa sửa), từng câu kèm nguồn, danh sách cờ, mục "cần xác minh / thiếu", các câu "chưa xác minh được".
Lưu `diff_jsonb` (chênh lệch per-câu giữa bản AI và bản bác sĩ). Sau duyệt: mọi PATCH trả **409**.

### FR-09 — Banner và nhãn minh bạch (P0-8, owner F)
Mọi trang sau đăng nhập: banner "⚠ DỮ LIỆU MÔ PHỎNG — Bác sĩ rà soát trước khi sử dụng" (cố định, không đóng được) và nhãn "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" ở đầu vùng nội dung AI.

### FR-10 — Nhật ký vận hành (P0-9, owner B)
Sự kiện: `login, view_patient, generate_summary, view_summary, edit_summary, approve_summary, ack_flag, denied`.
Trường: `actor, role, action, patient_id, summary_id, at (UTC), ip`. **Không ghi nội dung hồ sơ.**
Bảng append-only: trigger DB chặn UPDATE và DELETE. Màn hình admin lọc theo người / hành động / bệnh nhân / khoảng ngày.
Kiểm tra tự động `make logcheck`: grep tên bệnh nhân seed trong log ứng dụng phải trả về 0 dòng.

### FR-11 — Benchmark một lệnh (P0-10, owner E)
`make benchmark` chạy nhóm A, B, D trên 30 BN; xuất `reports/bench_<ts>/report.md` và `report.json` kèm `git_commit, prompt_version, rules_version, model_id, data_version, bundle_sha256[]`. CI chạy nhóm B mỗi PR (không gọi LLM, nhanh).

### FR-12 — Triển khai một lệnh (P0-11, owner B)
`docker compose up -d` dựng 4 service: `caddy` (tự cấp HTTPS), `api`, `web`, `db`. Biến môi trường từ `.env`. `make seed` nạp 30 BN + tài khoản demo.

### FR-13…FR-21 — P1
FR-13 (P1-1) biểu đồ xu hướng có đường mục tiêu theo Bảng 4/5 · FR-14 (P1-2) timeline lọc theo loại và nguồn · FR-15 (P1-3) hiển thị mâu thuẫn hai nguồn cạnh nhau · FR-16 (P1-4) cờ DDI major từ DDInter · FR-17 (P1-5) xuất Composition + Bundle document, hỗ trợ `?_format=xml`, mỗi section trỏ nguồn · FR-18 (P1-6) xuất PDF có mã bản duyệt + QR · FR-19 (P1-7) nhóm C: PDSQI-9 VN + error-detection · FR-20 (P1-8) ablation 3 cấu hình · FR-21 (P1-9) adapter LLM nội bộ OpenAI-compatible.

---

## 3. Yêu cầu phi chức năng
Theo PRD Phụ lục D. Bổ sung chi tiết kỹ thuật:
- **Hiệu năng:** p95 ≤ 60 s (benchmark), timeout cứng 90 s, UI < 300 ms, ≥ 2 job đồng thời trên 2 vCPU/4 GB.
- **Xác định:** `rule_engine` và `verifier` là hàm thuần, không I/O, không gọi mạng — có test `test_no_rule_calls_network` (monkeypatch socket).
- **Tái tạo:** mỗi bản tóm tắt lưu đủ 4 phiên bản (prompt, rules, model, bundle hash); test chặn bản ghi thiếu bất kỳ trường nào.
- **Tin cậy:** 3 lần chạy cùng input trùng ≥ 90% fact (so tập claim đã chuẩn hóa).
- **Bảo mật:** HTTPS; cookie httpOnly; bcrypt; secrets chỉ qua env; CORS khóa về domain demo; không endpoint upload.
- **Tiếp cận:** WCAG AA; icon cờ kèm chữ (màu không là kênh duy nhất); điều hướng bàn phím đầy đủ cho luồng xem cờ → ack → duyệt.

---

## 4. Máy trạng thái bản tóm tắt
```
generating ──(verifier xong)──> draft ──(approve)──> approved [terminal]
     │                            │
     └──(lỗi | >90s)──> failed    └──(sửa)──> draft
approved ──(PATCH|ack)──> 409
```
**Bất biến bắt buộc (test tự động):** không endpoint nào ghi bản `draft` vào kho export hoặc Composition; `approved` không thể trở về `draft`.

---

## 5. Mô hình dữ liệu
| Bảng | Trường chính |
|---|---|
| `users` | id, email, role, pw_hash, created_at |
| `patients` | id, bundle_jsonb, bundle_sha256, data_version, as_of |
| `assignments` | doctor_id, patient_id, created_by, created_at |
| `summaries` | id, patient_id, status, content_jsonb, ai_content_jsonb, diff_jsonb, prompt_version, rules_version, model_id, bundle_sha256, created_by, approved_by, approved_at |
| `flags` | id, summary_id, rule_id, severity, payload_jsonb, group_key, status, ack_by, ack_at, ack_reason |
| `jobs` | id, summary_id, state, step, error, started_at, finished_at |
| `audit` | id, actor, role, action, patient_id, summary_id, at, ip — **chỉ INSERT** |

---

## 6. API
```
POST   /auth/login                          → set cookie
POST   /auth/logout
GET    /patients                            → danh sách được gán (doctor) | tất cả (admin)
GET    /patients/{id}                       → 403 + audit denied nếu không được gán
POST   /patients/{id}/summaries             → 202 {job_id}
GET    /jobs/{id}                           → {state, step, error}
GET    /summaries/{id}
PATCH  /summaries/{id}                      → 409 nếu approved
POST   /summaries/{id}/approve              → 422 nếu còn cờ Đỏ chưa ack
POST   /flags/{id}/ack                      → 409 nếu summary approved
GET    /summaries/{id}/sources/{claim_id}
GET    /summaries/{id}/export?format=composition|pdf[&_format=xml]   (P1, approved only)
GET    /admin/audit?actor&action&patient&from&to
POST   /admin/assignments     DELETE /admin/assignments/{id}
GET    /healthz
```

---

## 7. Truy vết yêu cầu
`tests/traceability.csv` giữ map FR ↔ US ↔ P0/P1 ↔ test file. CI fail nếu có FR thuộc P0 không có test tương ứng.
