# docs/28 — Bộ nộp bài Demo Day v2.0 ✅

**Định dạng (chốt theo giả định PRD mục 9 #5):** repo + báo cáo PDF + video + URL demo. Owner E xác nhận lại với ban tổ chức **trước 25/11** để còn thời gian đổi nếu khác.

## 1. Repo (tag `demo-day`, freeze từ 10/12)
- [ ] Code `api/`, `web/`, `rules/`, `prompts/`
- [ ] `docs/` đầy đủ (26 tài liệu) + `refs/` (QĐ 5481, QĐ 1353, `citations.json`, `SHA256SUMS.txt`)
- [ ] `data/` tag `data-v1` + `manifest.json` + Data Card + báo cáo bất biến
- [ ] `tests/` xanh toàn bộ; `tests/traceability.csv` đủ FR P0
- [ ] `docs/20_LICENSES.md` đầy đủ; notice LOINC trong README
- [ ] `README.md` gốc repo: mô tả 5 dòng + lệnh triển khai + liên kết tới `docs/00_INDEX.md`
- [ ] `.env.example` (không có secret thật)

## 2. Báo cáo PDF (đề xuất ≤ 20 trang)
Dàn bài:
1. Bài toán và số liệu có nguồn (1 trang) — nêu rõ số nào có nguồn, số nào chưa có.
2. Kiến trúc "xác định trước, LLM sau" (2 trang, có sơ đồ docs/11 §2).
3. 17 quy tắc và căn cứ (2 trang) — nhấn: 11/17 trích QĐ 5481 đã đối chiếu toàn văn, 6/17 là quy tắc hệ thống và hiện rõ loại nguồn; 3 quy tắc đã sửa sau khi đọc văn bản gốc (docs/14 §4).
4. Kết quả benchmark A/B/C/D + ablation với CI 95% (4 trang).
5. Tuân thủ pháp lý (2 trang, tóm từ docs/21) — Luật 91/2025, NĐ 356/2025, NĐ 102/2025, Luật AI 134/2025, TT 13/2025.
6. Hạn chế (1 trang) — **bắt buộc**: không có bác sĩ hành nghề chấm (docs/06 §E), dữ liệu mô phỏng, 5 giả định y khoa chưa xác nhận (docs/06 §F), PDSQI-9 bản dịch chưa chuẩn hóa.
7. Roadmap P2 (1 trang).
Phụ lục: Data Card, báo cáo benchmark mới nhất, hồ sơ rủi ro AI (docs/24).

## 3. Video (≤ 5 phút)
Kịch bản: đăng nhập → danh sách bệnh nhân → **Tạo bản tóm tắt** (cho thấy tiến trình 9 bước) → đọc 5 dòng chính → click một câu mở panel nguồn, tô sáng đoạn ghi chú → tab Cờ: một cờ Đỏ với trích dẫn QĐ 5481 → bấm Đã xem xét → sửa một câu → Duyệt (cho thấy modal xác nhận) → thử sửa lại → **409** → xuất PDF/Composition.
Lưu ý khi quay: để banner "DỮ LIỆU MÔ PHỎNG" luôn trong khung hình; không quay màn hình có API key.

## 4. URL demo
- [ ] URL sống có HTTPS từ 9/12
- [ ] Tài khoản `doctor` và `admin` riêng cho giám khảo (quyền trên tập bệnh nhân demo)
- [ ] Checklist công bố URL (docs/23) pass hết
- [ ] Theo dõi uptime từ 9/12 đến 13/12
- [ ] Tập dượt demo 2 lần (điều kiện Release candidate)

## 5. Phân công và hạn
| Việc | Owner | Hạn |
|---|---|---|
| Xác nhận định dạng nộp bài với BTC | E | **25/11** |
| Dàn bài + bản nháp báo cáo PDF | E | 2/12 |
| Chốt số liệu benchmark cuối cho báo cáo | E | 7/12 |
| Kịch bản video + quay + dựng | F | 9/12 |
| Checklist URL + tài khoản giám khảo | B | 9/12 |
| Data Card + manifest hoàn chỉnh | D | 2/12 |
| Freeze + tag `demo-day` | B | 10/12 |
| Tập dượt 2 lần | cả đội | 11–12/12 |
