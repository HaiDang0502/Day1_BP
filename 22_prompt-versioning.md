# docs/22 — Prompt & phiên bản hóa ✅ v1.0

## Cấu trúc repo
```
prompts/
  writer/v1.0.0.md      # system + template 8 mục, few-shot, glossary nhúng
  writer/CHANGELOG.md
rules/
  rules_v1.0.0.yaml     # 17 quy tắc, tham số ngưỡng (docs/13 là spec, file này là code-config)
  CHANGELOG.md
```
SemVer: MAJOR = đổi nghĩa đầu ra (thêm/bớt mục, đổi schema); MINOR = đổi hướng dẫn/từ vựng; PATCH = sửa chính tả. Mỗi bản tóm tắt lưu (prompt_version, rules_version, model_id, bundle_sha256) — không bản ghi nào thiếu 4 trường này (test tự động).

## Nội dung prompt writer (khung v1.0.0)
- System: vai trò "soạn bản tóm tắt hồ sơ", KHÔNG khuyến nghị điều trị; chỉ dùng facts/events/flags được cấp; mỗi câu bắt buộc kèm `sources[]`; thiếu dữ liệu → đúng chuỗi "không có dữ liệu"; văn phong theo glossary (docs/19); định dạng số/ngày VN.
- Input: JSON {facts, events, flags_payload, note_excerpts(có ref #char), as_of, target_hba1c}.
- Output: JSON schema 8 mục (Phụ lục C), mỗi claim {text, sources[], numbers[]}; từ chối mẫu tự do.
- Few-shot: 2 ví dụ (1 BN đủ dữ liệu, 1 BN bẫy thiếu).
## Quy trình đổi prompt
PR riêng, chỉ đổi prompt/rules → CI chạy benchmark nhóm B + A-trên-5-BN-mẫu → đính report vào PR → 1 người khác duyệt. Cấm sửa prompt và code logic trong cùng PR.
