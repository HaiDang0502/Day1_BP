# docs/22 — Prompt & phiên bản hóa v2.0 ✅

## 1. Cấu trúc trong repo
```
prompts/
  writer/v1.0.0.md        # system prompt + template 8 mục + few-shot
  writer/CHANGELOG.md
rules/
  rules_v1.0.0.yaml       # tham số ngưỡng, khớp docs/13 (spec là docs/13, file này là cấu hình)
  CHANGELOG.md
```
**SemVer:** MAJOR = đổi nghĩa đầu ra (thêm/bớt mục, đổi schema) · MINOR = đổi hướng dẫn, từ vựng, few-shot, hoặc đổi ngưỡng quy tắc · PATCH = sửa chính tả, định dạng.

Mỗi bản tóm tắt lưu đủ 4 trường: `prompt_version`, `rules_version`, `model_id`, `bundle_sha256`. Test tự động chặn bản ghi thiếu bất kỳ trường nào.

## 2. Nội dung prompt writer v1.0.0

**System:**
- Vai trò: soạn bản tóm tắt hồ sơ cho bác sĩ đọc trước lượt tái khám. **Không** chẩn đoán, **không** khuyến nghị điều trị, **không** đánh giá mức độ khẩn cấp.
- Chỉ dùng dữ liệu được cấp trong input. Không dùng kiến thức ngoài. Không suy đoán.
- Mỗi câu **bắt buộc** kèm `sources[]` là mã resource (`ResourceType/id`), với ghi chú thì `DocumentReference/id#char=a-b`.
- Thiếu dữ liệu → ghi đúng chuỗi `không có dữ liệu` (không dùng "chưa rõ", "có thể").
- Văn phong và chuỗi cố định theo glossary (nhúng toàn bộ docs/19 §1–4).
- Định dạng số: dấu phẩy thập phân; ngày dd/mm/yyyy.
- Diễn giải cờ: dùng đúng dữ liệu trong `flags_payload`, kết thúc `— cần bác sĩ xem xét.`, không thêm nhận định nào ngoài payload.

**Input (JSON):**
```json
{
  "as_of": "2026-12-01",
  "patient": {"age": 67, "sex": "nữ", "dx_duration_years": 8},
  "facts": [...],          // fact có cấu trúc do code dựng, mỗi fact có source_refs
  "events": [...],         // timeline
  "flags_payload": [...],  // cờ do rule_engine sinh (docs/13 §0.10)
  "note_excerpts": [...],  // đoạn ghi chú kèm ref #char=a-b
  "target_hba1c": {"value": 8.0, "tier": "elderly_multimorbid", "basis": "Bảng 5"}
}
```

**Output (JSON schema cố định):** 8 mục theo Phụ lục C; mỗi mục là danh sách claim `{text, sources[], numbers[]}`; `numbers[]` phần tử có `{value, unit, source_ref}` hoặc `{value, unit, derived_from: [fact_id]}` cho số suy ra.

**Few-shot:** 2 ví dụ — (1) bệnh nhân đủ dữ liệu có 2 cờ; (2) bệnh nhân bẫy thiếu hẳn UACR (minh họa cách ghi "không có dữ liệu" đúng ô và không bịa).

## 3. Quy trình đổi prompt hoặc ngưỡng
1. PR **chỉ** đổi `prompts/` hoặc `rules/` — **cấm** sửa prompt/rules và code logic trong cùng một PR.
2. CI tự chạy: toàn bộ nhóm B (không gọi LLM) + nhóm A trên 5 bệnh nhân mẫu.
3. Đính báo cáo so sánh trước/sau vào PR.
4. Một người khác duyệt. Nếu chỉ số nhóm A giảm > 2 điểm phần trăm hoặc B1 < 100% → không merge.
5. Tăng version và viết CHANGELOG (một dòng: đổi gì, vì sao, ảnh hưởng chỉ số nào).

## 4. Tái tạo một bản tóm tắt cũ
```bash
make reproduce SUMMARY_ID=<id>
# đọc 4 phiên bản từ bảng summaries → checkout prompt + rules tương ứng
# → chạy lại pipeline trên bundle có sha256 đã ghi → so sánh với bản đã lưu
```
Dùng khi điều tra sự cố hoặc khi giám khảo hỏi "bản này được tạo ra thế nào".
