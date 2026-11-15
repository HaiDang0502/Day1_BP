# docs/05 — Kế hoạch benchmark v2.0 ✅ ĐÃ CHỐT

Mục tiêu: mọi chỉ số Phụ lục D của PRD đo được bằng một lệnh `make benchmark`, lặp lại được, có khoảng tin cậy.

## 1. Nhóm A — Trung thực (tự động, 30 BN × 3 lần)
| Chỉ số | Cách đo | Ngưỡng |
|---|---|---|
| A1 câu không được nguồn hỗ trợ | Với mỗi claim, script đối chiếu thực thể + số với resource trong `sources[]`; mẫu 10% rà tay để hiệu chuẩn script | ≤ 2% |
| A2 bỏ sót fact bắt buộc | So claims với `gold.required_facts[]`; match theo (loại fact, mã, ngày, giá trị ± làm tròn) | ≤ 10% |
| A3 độ chính xác trích dẫn | Phương pháp ALCE thích nghi tiếng Việt: nguồn được trích có **ủng hộ** câu hay không | ≥ 95% |
| A4 câu có nguồn hợp lệ | Verifier bảo đảm by-construction; benchmark xác nhận lại bằng cách đếm claim hiển thị như sự thật mà thiếu `sources[]` | 100% |
| A5 bẫy bịa đặt | 10 BN bẫy (docs/15 §4): phải ghi "không có dữ liệu" đúng ô và không xuất hiện giá trị loại đó | 100% |
| A6 số liệu khớp nguồn | Đếm trên mọi số trong mọi claim, gồm số suy ra (phải khớp phép tính từ fact) | ≥ 99% |

## 2. Nhóm B — Cờ (tự động, chạy mỗi PR trong CI, không gọi LLM)
| Chỉ số | Cách đo | Ngưỡng |
|---|---|---|
| B1 recall | Trên 44 tình huống cài sẵn (docs/15 §3): mọi `expected = fire` phải kích | 100% |
| B2 precision | Cờ sinh ra phải ∈ `gold.expected_flags`; cờ ∈ `forbidden_flags` tính là false positive | ≥ 90% |
| B3 số nhóm cờ | Đếm nhóm Đỏ+Vàng sau dedup (docs/13 §0.8) | TB ≤ 3; hiển thị ≤ 7 |
| B4 diễn giải cờ đúng payload | So câu cờ với template payload; mọi sai khác về số/thuốc/ngày → fail | 100% |

## 3. Nhóm C — Người chấm (P1)
**Phương án đang áp dụng: không có bác sĩ cố vấn** (quyết định PRD mục 9 #2). Nhóm chấm = mentor AI for Healthcare + 2 thành viên đội được huấn luyện (buổi 2 giờ: rubric PDSQI-9 VN, 3 bản chấm mẫu đối chiếu để hiệu chuẩn).
- **PDSQI-9 tiếng Việt:** dịch + hiệu đính bởi mentor; ghi rõ trong báo cáo là *bản dịch chưa chuẩn hóa*. ≥ 2 người chấm × ≥ 10 bản. Báo cáo ICC (độ đồng thuận).
- **Error-detection test:** 10 bản được cấy 1 lỗi mỗi bản (3 sai số liệu, 3 sai thuốc, 4 bỏ sót fact bắt buộc). Mỗi người chấm 5 bản **có** panel nguồn và 5 bản **không có**; đo tỷ lệ phát hiện và thời gian đọc. Mục tiêu: 0 lỗi mức nặng lọt qua.
- **Ngôn ngữ báo cáo:** tuyệt đối không viết "được bác sĩ đánh giá" khi chỉ có nhóm này chấm.

## 4. Nhóm D — Kỹ thuật (tự động)
p95 độ trễ ≤ 60 s · chi phí USD/bản (từ log token) · ổn định: 3 lần chạy cùng input trùng ≥ 90% fact · bền với nhiễu: 5 BN được thêm nhiễu chính tả vào ghi chú, các chỉ số nhóm A không giảm > 5 điểm phần trăm.

## 5. Ablation (Beta 2/12)
3 cấu hình × 30 BN × 3 lần = 270 lần gọi LLM:
- **C1 baseline:** LLM không bị ép trích nguồn, không có quy tắc cờ.
- **C2:** + ép trích nguồn + verifier.
- **C3 (= MVP):** + quy tắc cờ.
Bootstrap 1.000 lần → khoảng tin cậy 95% cho A1–A6, B1–B2. Cặp cấu hình có CI giao nhau → ghi "không kết luận được", không tuyên bố cải thiện.

**Ngân sách (chốt): 75 USD** cho toàn bộ phát triển + ablation. Phân bổ: ~40 USD phát triển/thử nghiệm, ~25 USD ablation (270 lần gọi), ~10 USD dự phòng. Nếu vượt 60 USD trước 2/12 thì giảm ablation xuống 2 lần lặp thay vì 3 và ghi rõ trong báo cáo.

## 6. Đầu ra
`make benchmark` → `reports/bench_<ts>/report.md` + `report.json`, kèm: `git_commit`, `prompt_version`, `rules_version`, `model_id`, `data_version`, `bundle_sha256[]`, thời điểm chạy. Mọi số trong báo cáo cuối phải trích được về một file trong `reports/`.
**Cam kết:** kết quả không đạt ngưỡng vẫn được báo cáo thật, kèm phân tích nguyên nhân.
