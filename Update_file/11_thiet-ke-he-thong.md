# docs/11 — Thiết kế hệ thống v2.0 ✅ ĐÃ CHỐT

## 1. Stack (chốt)
| Lớp | Lựa chọn | Lý do |
|---|---|---|
| Backend | Python 3.12 + FastAPI | `fhir.resources` đọc/ghi FHIR R4; `pydantic` ràng buộc schema đầu ra LLM; async phù hợp gọi LLM |
| Job | `BackgroundTasks` + bảng `jobs` | Không cần Redis/Celery với ≤ 2 job đồng thời trên 2 vCPU; ít thành phần, dễ deploy một lệnh |
| Frontend | React 18 + Vite + TypeScript | Desktop ≥ 1280 px; không cần SSR |
| DB | PostgreSQL 16 | JSONB cho Bundle và payload cờ; trigger chặn UPDATE/DELETE trên `audit` |
| LLM | Anthropic API qua interface `LLMClient` | Hoán đổi được; adapter OpenAI-compatible (P1) cho model nội bộ — đường tuân thủ Đ18 NĐ 356/2025 |
| Proxy | Caddy | Tự cấp Let's Encrypt theo `DOMAIN`, cấu hình 5 dòng |
| Deploy | Docker Compose, 1 VM 2 vCPU/4 GB | P0-11 một lệnh |
| Vector store | **không dùng** | Không hỏi đáp tự do, không RAG |

## 2. Sơ đồ thành phần
```
                    ┌──────────── Caddy (HTTPS, domain) ────────────┐
                    │                                               │
            ┌───────▼────────┐                          ┌───────────▼──────────┐
            │  web (React)   │ ── cookie JWT ──────────▶│   api (FastAPI)      │
            └────────────────┘                          │                      │
                                                        │ auth / rbac          │
                                                        │ patients, assignments│
                                                        │ summaries, flags     │
                                                        │ audit (append-only)  │
                                                        │                      │
                                                        │ ┌── orchestrator ──┐ │
                                                        │ │ 1 fhir_loader    │ │
                                                        │ │ 2 timeline       │ │
                                                        │ │ 3 med_normalizer │ │
                                                        │ │ 4 lab_normalizer │ │
                                                        │ │ 5 rule_engine ●  │ │
                                                        │ │ 6 fact_builder   │ │
                                                        │ │ 7 llm_writer ────┼─┼──▶ Anthropic API
                                                        │ │ 8 verifier    ●  │ │
                                                        │ │ 9 save_draft     │ │
                                                        │ └──────────────────┘ │
                                                        └───────┬──────────────┘
                                                                │
                                                        ┌───────▼──────────┐
                                                        │  PostgreSQL 16   │
                                                        └──────────────────┘
● = hàm thuần, không I/O, không gọi mạng (test bắt buộc)
```

## 3. Nguyên tắc kiến trúc
1. **Xác định trước, LLM sau.** `rule_engine` và `verifier` là hàm thuần trên dữ liệu đã nạp → test 100% xác định, không flaky. LLM là thành phần duy nhất không xác định và bị verifier chặn ngay sau.
2. **Một chiều dữ liệu.** Bundle (chỉ đọc) → facts → LLM → verifier → draft. Không bước nào ghi lại vào Bundle.
3. **Cờ không phụ thuộc LLM.** Nếu bước 7 lỗi, bước 5 vẫn đã xong — nhưng job vẫn `failed` (không hiển thị bản nửa vời). Dòng thời gian và cờ chỉ hiện trong bản nháp hoàn chỉnh.
4. **Mọi bản ghi mang 4 phiên bản** (prompt, rules, model, bundle hash) → tái tạo được bất kỳ bản nào.

## 4. Luồng tạo tóm tắt (chi tiết)
1. `POST /patients/{id}/summaries` → kiểm quyền (403 + audit nếu sai) → tạo `summaries` (status `generating`) + `jobs` (queued) → trả 202 `{job_id}`.
2. Orchestrator chạy bước 1–6, cập nhật `jobs.step` sau mỗi bước.
3. Bước 7: gửi `{facts, events, flags_payload, note_excerpts, as_of, target_hba1c}` cho LLM; nhận JSON 8 mục. Parse lỗi → retry 1 lần → vẫn lỗi → `failed`.
4. Bước 8: verifier 3 kiểm tra (SRS FR-05); câu fail → `unverified[]`.
5. Bước 9: lưu `content_jsonb` (verified + unverified tách mục) + `ai_content_jsonb` (bản gốc LLM, để tính diff sau khi bác sĩ sửa) + flags; audit `generate_summary`; job `draft`.
Timeout cứng 90 giây đo từ lúc job chuyển `running`.

## 5. Bảo mật
HTTPS end-to-end (Caddy); cookie `httpOnly; Secure; SameSite=Lax`; bcrypt cost 12; secrets chỉ trong `.env` (không commit, có `.env.example`); CORS allowlist đúng domain demo; rate limit đăng nhập 5/phút/IP; không endpoint upload file; `audit` không sửa/xóa được ở tầng DB.

## 6. Điểm chừa cho triển khai thật (P2 — không làm, chỉ không chặn đường)
- `Patient.identifier` chừa slot cho **mã định danh y tế** = số định danh cá nhân (Điều 6 NĐ 102/2025).
- Composition chừa `attester` + `signature` cho ký điện tử theo Điều 3 TT 13/2025 (để trống ở demo).
- `LLMClient` đã trừu tượng → chuyển sang model nội bộ không sửa pipeline (đường tuân thủ Đ18 NĐ 356/2025).
- Tầng `fhir_loader` nhận Bundle từ biến đầu vào, không hardcode nguồn → sau này cắm FHIR API của HIS không đổi các bước sau.

## 7. Thư mục repo
```
api/           FastAPI app: routers/, services/, pipeline/, rules/, verifier/
web/           React app: pages/, components/, api/
rules/         rules_v1.0.0.yaml (tham số ngưỡng, khớp docs/13)
prompts/       writer/v1.0.0.md + CHANGELOG.md
data/          bundles/, gold/, truth/, scenarios.csv, drug_map.csv, manifest.json
refs/          QĐ 5481 + QĐ 1353 (.docx), citations.json, SHA256SUMS.txt
docs/          tài liệu dự án
tests/         rules/, pipeline/, api/, traceability.csv
reports/       bench_<ts>/, invariants_<data_version>.md
docker-compose.yml  Makefile  .env.example
```
