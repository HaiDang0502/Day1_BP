# docs/27 — Báo cáo ablation (template — điền sau Beta 2/12) ✅

## Thiết lập
Data: data-v1 (30 BN, sha256 manifest …) · 3 cấu hình: C1 baseline (không ép nguồn, không quy tắc) / C2 +ép nguồn+verifier / C3 +quy tắc (= MVP) · mỗi cấu hình × 3 lần chạy · model: [model_id] · prompt: [version] · rules: [version] · bootstrap 1.000 lần, CI 95%.

## Kết quả (điền)
| Chỉ số | C1 | C2 | C3 (MVP) | Ngưỡng MVP |
|---|---|---|---|---|
| A1 câu không được nguồn hỗ trợ | …% (CI …) | … | … | ≤ 2% |
| A2 bỏ sót fact bắt buộc | | | | ≤ 10% |
| A3 độ chính xác trích dẫn | | | | ≥ 95% |
| A4 câu có nguồn hợp lệ | n/a | | | 100% |
| A5 bẫy bịa đặt | | | | 100% |
| A6 số khớp nguồn | | | | ≥ 99% |
| B1 / B2 | n/a | n/a | | 100% / ≥ 90% |
| B3 (TB / max, sau dedup) | n/a | n/a | | ≤ 3 / 7 |
| p95 (s) / chi phí ($/bản) | | | | ≤ 60 |
| Ổn định (trùng fact 3 lần) | | | | ≥ 90% |

## Phân tích (điền)
- Đóng góp của ép nguồn (C2−C1) và của quy tắc (C3−C2), kèm CI; cặp nào CI giao nhau → ghi "không kết luận được".
- Case study: 2 lỗi tiêu biểu C1 bị C2/C3 chặn; 1 lỗi còn lọt ở C3.
- Cam kết: kết quả không đạt vẫn báo cáo thật (Phụ lục D PRD).
