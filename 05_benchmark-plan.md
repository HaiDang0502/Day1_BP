# docs/05 — Kế hoạch benchmark v1.0 🟡

Mục tiêu: mọi chỉ số Phụ lục D đo được bằng một lệnh `make benchmark`, lặp lại được.

## 1. Nhóm chỉ số và cách đo
### A. Trung thực (chạy tự động trên 30 BN × 3 lần)
- A1 câu không được nguồn hỗ trợ ≤ 2%: với mỗi claim, so `numbers[]` và thực thể với resource nguồn; chấm bằng script đối chiếu + mẫu 10% rà tay.
- A2 bỏ sót fact bắt buộc ≤ 10%: so claims với `gold.required_facts[]` (16_gold-truth-spec); match theo (loại fact, mã, ngày, giá trị ± làm tròn).
- A3 độ chính xác trích dẫn ≥ 95%: phương pháp ALCE (arXiv 2305.14627) thích nghi tiếng Việt: câu được nguồn đã trích dẫn **ủng hộ**.
- A4 câu có nguồn hợp lệ = 100%: verifier bảo đảm by-construction; benchmark xác nhận lại (đếm claim hiển thị như sự thật thiếu source).
- A5 bẫy bịa đặt 100%: 10 BN bẫy (thiếu hẳn 1 loại XN) → bản tóm tắt phải ghi "không có dữ liệu" đúng ô, không sinh giá trị.
- A6 số liệu khớp nguồn ≥ 99%: đếm trên mọi số trong mọi claim.
### B. Cờ (tự động)
- B1 recall = 100% trên ≥ 40 tình huống cài sẵn; B2 precision ≥ 90% (cờ sinh ra ∈ gold.expected_flags); B3 trung bình ≤ 3, tối đa 7 (đếm Đỏ+Vàng sau dedup — rule-spec §0.4); B4 diễn giải đúng payload = 100% (so câu cờ với template payload, khác biệt → fail).
### C. Người chấm (P1)
- PDSQI-9 tiếng Việt (dịch + hiệu đính bởi mentor y khoa; ghi rõ bản dịch chưa chuẩn hóa): ≥ 2 người chấm × ≥ 10 bản; báo cáo ICC.
- Error-detection test: 10 bản cấy 1 lỗi/bản (sai số, sai thuốc, bỏ sót) → so tỷ lệ phát hiện có/không panel nguồn; đo thời gian đọc; 0 lỗi mức nặng lọt.
- Nếu không có bác sĩ cố vấn (Ambiguity #2): nhóm C do mentor + 2 thành viên được huấn luyện chấm; ghi hạn chế vào báo cáo (docs/06 §D).
### D. Kỹ thuật (tự động)
p95 latency ≤ 60 s; chi phí USD/bản (log token); ổn định: 3 lần chạy cùng input trùng ≥ 90% fact (so tập claim chuẩn hóa); bền nhiễu: 5 BN bị thêm nhiễu chính tả vào ghi chú → A-metrics không giảm > 5 điểm.

## 2. Ablation (Beta 2/12)
3 cấu hình × 30 BN × 3 lần: (1) baseline LLM không ép nguồn, không quy tắc; (2) + ép nguồn + verifier; (3) + quy tắc cờ (= MVP). Bootstrap 1.000 lần → CI 95% cho A1–A6, B1–B2. Kết quả không đạt vẫn báo cáo thật. Chi phí ước: 3×30×3 ≈ 270 lần gọi + dev ≈ trong ngân sách ~75 USD [QUYẾT ĐỊNH 25/11 — Ambiguity #3: xác nhận ngân sách trước khi chạy 3 lần lặp].

## 3. Đầu ra
`make benchmark` → `reports/bench_<ts>/report.{md,json}` kèm: git commit, prompt_version, rules_version, model_id, data tag (data-v1), sha256 từng bundle. CI chạy nhóm B mỗi PR (nhanh, không gọi LLM).
