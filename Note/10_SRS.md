# docs/10 — SRS (Software Requirements Specification) v1.0 🟡

Phạm vi: toàn bộ P0 + P1 của PRD 1.1 (đã áp delta 1.2). Mục [QUYẾT ĐỊNH] cần đội chốt, có hạn kèm theo.

## 1. Tác nhân & quyền (RBAC)
| Role | Quyền |
|---|---|
| doctor | GET bệnh nhân được gán; POST tạo tóm tắt; GET/PATCH bản nháp của bệnh nhân được gán; POST duyệt; POST ack cờ; GET panel nguồn |
| admin | Mọi quyền doctor trên mọi bệnh nhân; POST/DELETE gán bệnh nhân; GET nhật ký (lọc) |
| (CLI) đội dự án | Nạp dữ liệu, `make benchmark` — không qua HTTP, chạy trực tiếp trên máy chủ |
Không có role bệnh nhân. Mọi truy cập ngoài quyền: 403 + audit `denied`.

## 2. Yêu cầu chức năng (đánh số FR, map P0/P1 PRD)
FR-01 (P0-1) Đăng nhập: JWT trong cookie httpOnly, bcrypt; phiên 8 giờ; không refresh token ở MVP.
FR-02 (P0-1) Gán bệnh nhân: admin chọn doctor + patient; xác nhận trước khi lưu; audit.
FR-03 (P0-3) Pipeline 10 bước, chạy nền (job), trạng thái: queued → running(step i/9) → draft | failed.
  Các bước: (1) đọc Bundle → (2) dòng thời gian → (3) chuẩn hóa thuốc → (4) chuẩn hóa xét nghiệm → (5) quy tắc (13_rule-spec) → (6) danh sách sự kiện/fact → (7) LLM soạn 8 mục → (8) verifier → (9) lưu nháp. (+ bước 0: kiểm tra quyền.)
FR-04 (P0-4) Output LLM: JSON schema cố định 8 mục; mỗi câu `{text, sources[], numbers[]}`. Parse fail toàn cục → retry **1 lần** với cùng prompt; fail tiếp → job failed. [QUYẾT ĐỊNH 25/11 — Ambiguity #11: đề xuất retry = 1]
FR-05 (P0-4/P0-5) Verifier từng câu, 3 kiểm tra: (a) mọi `sources[]` tồn tại trong Bundle; (b) mọi số trong `text` khớp `numbers[]` và khớp giá trị nguồn sau quy đổi (dung sai làm tròn 1 chữ số thập phân); (c) không khớp mẫu cụm chỉ định. Câu fail → mục "chưa xác minh được". Cụm chỉ định (KT-3): regex trên các mẫu "nên|cần|đề nghị|khuyến cáo + <động từ>", "ngừng/đổi <thuốc>" **ở thì khuyến nghị**; chuỗi nằm trong trích dẫn sự kiện có nguồn (vd status=stopped trong Timeline) được miễn.
FR-06 (P0-5) Panel nguồn: click câu → hiện resource gốc + cơ sở + ngày; với DocumentReference hiện đoạn `#char=a-b` tô sáng; TTFB < 300 ms sau cache.
FR-07 (P0-6) Cờ: hiển thị theo §0.4 rule-spec; ack từng cờ, lý do tùy chọn; cờ không xóa được; ack được cả sau khi bản đã duyệt? → **Không**: sau duyệt bản khóa hoàn toàn, ack thuộc bản; muốn ack thêm phải tạo bản mới. [QUYẾT ĐỊNH 25/11]
FR-08 (P0-7) Duyệt: điều kiện = đã mở tab Cờ ∧ mọi cờ Đỏ đã ack (delta #13). Lưu diff so với bản AI (per-câu). Sau duyệt: PATCH → 409.
FR-09 (P0-8) Banner "DỮ LIỆU MÔ PHỎNG" + dòng "Bản nháp do AI soạn — bác sĩ xem xét và duyệt" trên mọi trang sau đăng nhập.
FR-10 (P0-9) Audit: sự kiện login, view_patient, generate_summary, view_summary, edit_summary, approve_summary, ack_flag, denied; trường: actor, role, time (UTC), patient_id, summary_id, action, ip. KHÔNG ghi nội dung hồ sơ. Append-only (bảng không UPDATE/DELETE).
FR-11 (P0-10) `make benchmark`: chạy nhóm A,B,D trên 30 BN; xuất `report.md` + `report.json` kèm git commit, prompt_version, rules_version, model_id, data sha256.
FR-12 (P0-11) `docker compose up -d` duy nhất; HTTPS (Caddy/Traefik tự cấp Let's Encrypt).
FR-13..19 (P1-1..P1-9): biểu đồ xu hướng (đường mục tiêu theo Bảng 4,5), timeline lọc, mâu thuẫn cạnh nhau, DDI major (DDInter, bảng con ~25 hoạt chất), xuất Composition + Bundle document (validator), PDF + QR, benchmark nhóm C, ablation, adapter OpenAI-compatible.

## 3. Yêu cầu phi chức năng (từ Phụ lục D, giữ nguyên ngưỡng)
Hiệu năng p95 ≤ 60 s (benchmark), timeout cứng 90 s [QUYẾT ĐỊNH — delta #12]; UI < 300 ms; ≥ 2 job đồng thời/2 vCPU 4 GB; an toàn: không câu thiếu nguồn hiển thị như sự thật; bảo mật HTTPS/JWT/bcrypt/secrets qua env; riêng tư: chỉ dữ liệu mô phỏng, log không chứa nội dung hồ sơ; tái tạo: mỗi bản lưu (prompt_version, rules_version, model_id, bundle_sha256); tin cậy: 3 lần chạy trùng ≥ 90% fact; tiếng Việt toàn bộ; WCAG AA.

## 4. Trạng thái bản tóm tắt (máy trạng thái)
`generating → draft → approved`; `generating → failed → (tạo lại)`. approved là terminal. Không có đường lưu bản chưa duyệt thành "một phần hồ sơ" (test tự động: không endpoint nào ghi draft vào kho export).

## 5. API phác thảo
`POST /auth/login` · `GET /patients` (theo gán) · `POST /patients/{id}/summaries` → 202 {job_id} · `GET /jobs/{id}` · `GET /summaries/{id}` · `PATCH /summaries/{id}` (draft only, else 409) · `POST /summaries/{id}/approve` · `POST /flags/{id}/ack` · `GET /summaries/{id}/sources/{claim_id}` · `GET /admin/audit?filter` · `POST /admin/assignments` · `GET /summaries/{id}/export?format=composition|pdf` (P1, approved only).

## 6. Truy vết yêu cầu
Bảng map FR ↔ US ↔ P0/P1 ↔ test: duy trì trong `tests/traceability.csv` (CI fail nếu FR P0 không có test).
