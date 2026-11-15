# docs/11 — Thiết kế hệ thống v1.0 🟡

[QUYẾT ĐỊNH 18/11 — Ambiguity #1]: stack dưới đây là **đề xuất mặc định**; đội chốt trong buổi kickoff Sprint 1.

## 1. Stack đề xuất
| Lớp | Đề xuất | Lý do |
|---|---|---|
| Backend | Python 3.12 + FastAPI | Khớp hệ sinh thái FHIR (fhir.resources), pydantic validate schema LLM |
| Job queue | Không cần broker riêng: BackgroundTasks + bảng jobs (SQLite/Postgres) | 2 vCPU/4 GB, ≤ 2 job đồng thời |
| Frontend | React + Vite + TypeScript, desktop ≥ 1280 px | |
| DB | PostgreSQL 16 (1 container) | JSONB cho Bundle, audit append-only |
| LLM | Anthropic API (demo) qua lớp adapter `LLMClient`; adapter OpenAI-compatible (P1) cho model nội bộ | NĐ 356/2025 Đ18 — đường tuân thủ khi dữ liệu thật |
| Reverse proxy | Caddy (tự HTTPS) | P0-11 |
| Deploy | Docker Compose, 1 VM 2 vCPU/4 GB | cần VM + tên miền trước 1/12 (Ambiguity #4) |
| Vector store | KHÔNG dùng | đúng PRD mục 8 |

## 2. Thành phần
```
[React SPA] ⇄ [FastAPI]
                ├─ auth (JWT cookie, bcrypt, RBAC)
                ├─ patients / assignments
                ├─ pipeline orchestrator (10 bước, FR-03)
                │    ├─ fhir_loader → timeline_builder → med_normalizer → lab_normalizer
                │    ├─ rule_engine (docs/13; thuần hàm, không I/O)
                │    ├─ fact_builder (facts + events, mỗi fact mang source refs)
                │    ├─ llm_writer (prompt v?, JSON schema 8 mục)
                │    └─ verifier (3 kiểm tra FR-05)
                ├─ flags / ack
                ├─ summaries (draft/approve/diff/export)
                └─ audit (append-only)
[PostgreSQL]  [Anthropic API]  [Caddy HTTPS]
```
Nguyên tắc kiến trúc: rule_engine và verifier **không gọi mạng**, thuần hàm trên dữ liệu đã nạp → unit test 100% xác định; LLM là thành phần duy nhất không xác định và bị verifier chặn sau.

## 3. Mô hình dữ liệu (bảng chính)
users(id, role, pw_hash) · patients(id, bundle_jsonb, bundle_sha256, data_version) · assignments(doctor_id, patient_id) · summaries(id, patient_id, status, content_jsonb, ai_content_jsonb, diff_jsonb, prompt_version, rules_version, model_id, bundle_sha256, created_by, approved_by, approved_at) · flags(id, summary_id, rule_id, severity, payload_jsonb, status, ack_by, ack_at, ack_reason) · jobs(id, summary_id, state, step, error, started_at, finished_at) · audit(id, actor, role, action, patient_id, summary_id, at, ip) — audit: chỉ INSERT (trigger chặn UPDATE/DELETE).

## 4. Luồng tạo tóm tắt (sequence)
1. POST summaries → kiểm quyền → tạo job queued → 202.
2. Orchestrator chạy 9 bước, cập nhật jobs.step (UI poll 1 s, hiện tiếng Việt).
3. Bước LLM: gửi facts + events + flags (payload) + ghi chú đã trích đoạn; nhận JSON 8 mục; parse fail → retry 1 → fail job.
4. Verifier: từng câu; câu fail → `unverified[]`.
5. Lưu draft (content = verified + unverified tách mục), audit generate_summary.
Timeout cứng 90 s [QUYẾT ĐỊNH]; job fail không lưu nội dung dở.

## 5. Prompt & tái tạo
prompts/ trong repo, mỗi file có SemVer + changelog (docs/22). summaries lưu đủ 4 phiên bản (prompt, rules, model, data) → tái tạo mọi bản.

## 6. Bảo mật
HTTPS end-to-end; cookie Secure+HttpOnly+SameSite=Lax; secrets chỉ qua biến môi trường (.env không commit); không endpoint upload file; CORS khóa về domain demo; rate limit đăng nhập 5 lần/phút.

## 7. Điểm mở rộng thật (P2 — không làm, chỉ chừa chỗ)
- Patient.identifier chừa slot `mã định danh y tế` (số định danh cá nhân, NĐ 102/2025 Đ6).
- Export Composition đặt field cho chữ ký điện tử (TT 13/2025 Đ3) — để trống ở demo.
- LLMClient interface đã trừu tượng → chuyển model nội bộ không sửa pipeline.
