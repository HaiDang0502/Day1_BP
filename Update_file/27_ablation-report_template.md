# docs/27 — Báo cáo ablation (template, điền sau Beta 2/12) ✅

## 1. Thiết lập
| | |
|---|---|
| Dữ liệu | `data-v1`, 30 bệnh nhân, manifest sha256 _[điền]_ |
| Cấu hình | **C1** baseline (không ép nguồn, không quy tắc) · **C2** + ép nguồn + verifier · **C3** + quy tắc cờ (= MVP) |
| Số lần chạy | 3 lần mỗi cấu hình (270 lần gọi LLM) |
| Model | `model_id` = _[điền]_ |
| Prompt / Rules | `prompt_version` = _[điền]_ · `rules_version` = _[điền]_ |
| Thống kê | Bootstrap 1.000 lần, khoảng tin cậy 95% |
| Ngày chạy | _[điền]_ · Báo cáo gốc: `reports/bench_<ts>/` |

## 2. Kết quả
| Chỉ số | C1 (CI 95%) | C2 (CI 95%) | C3 = MVP (CI 95%) | Ngưỡng MVP | Đạt? |
|---|---|---|---|---|---|
| A1 câu không được nguồn hỗ trợ | | | | ≤ 2% | |
| A2 bỏ sót fact bắt buộc | | | | ≤ 10% | |
| A3 độ chính xác trích dẫn | n/a | | | ≥ 95% | |
| A4 câu có nguồn hợp lệ | n/a | | | 100% | |
| A5 bẫy bịa đặt xử lý đúng | | | | 100% | |
| A6 số liệu khớp nguồn | | | | ≥ 99% | |
| B1 recall cờ | n/a | n/a | | 100% | |
| B2 precision cờ | n/a | n/a | | ≥ 90% | |
| B3 nhóm cờ (TB / max) | n/a | n/a | | TB ≤ 3 / ≤ 7 | |
| B4 diễn giải cờ đúng payload | n/a | n/a | | 100% | |
| p95 độ trễ (giây) | | | | ≤ 60 | |
| Chi phí (USD/bản) | | | | — | |
| Ổn định (trùng fact 3 lần) | | | | ≥ 90% | |

## 3. Phân tích
**Đóng góp của việc ép trích nguồn (C2 − C1):** _[điền hiệu số kèm CI; nếu CI chứa 0 thì ghi "không kết luận được"]_.
**Đóng góp của bộ quy tắc (C3 − C2):** _[điền]_.
**Cặp cấu hình có CI giao nhau:** _[liệt kê; không tuyên bố cải thiện cho các cặp này]_.

## 4. Case study (bắt buộc, 3 ca)
1. **Lỗi C1 bị C2 chặn:** _[trích một câu C1 sinh ra không có nguồn, và cách verifier xử lý]_.
2. **Lỗi C2 bị C3 phát hiện:** _[một tình huống an toàn mà chỉ quy tắc cờ bắt được]_.
3. **Lỗi còn lọt ở C3:** _[một trường hợp MVP vẫn sai, kèm phân loại nguyên nhân và việc đã thêm vào test hồi quy]_.

## 5. Kết luận
_[2–3 câu: cấu hình nào đáng giữ, bằng chứng nào ủng hộ kiến trúc "xác định trước, LLM sau", và giới hạn của thí nghiệm — cỡ mẫu 30 bệnh nhân mô phỏng, một model, không có bác sĩ hành nghề chấm.]_

**Cam kết minh bạch:** kết quả không đạt ngưỡng được báo cáo nguyên trạng, kèm phân tích nguyên nhân. Không chọn lọc lần chạy có lợi.
