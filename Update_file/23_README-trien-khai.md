# docs/23 — README triển khai (P0-11) v2.0 ✅

## Yêu cầu hạ tầng
1 máy ảo Ubuntu 22.04+, **2 vCPU / 4 GB RAM**, tên miền trỏ A-record về IP máy ảo, mở cổng 80 và 443.
**Hạn cứng có máy ảo + tên miền: 1/12/2026** (nếu trượt thì Release candidate 9/12 không đạt điều kiện "URL sống").

## Triển khai một lệnh
```bash
git clone <repo> && cd vmec03
cp .env.example .env
#   điền: DOMAIN, ANTHROPIC_API_KEY, POSTGRES_PASSWORD, JWT_SECRET
docker compose up -d          # caddy (auto-HTTPS) + api + web + db
make seed                     # nạp 30 bệnh nhân + tài khoản demo (in mật khẩu 1 lần, không ghi log)
```

## Kiểm tra sau triển khai
```bash
curl -fsS https://$DOMAIN/healthz          # → {"status":"ok"}
make smoke                                 # đăng nhập + tạo 1 bản tóm tắt end-to-end
make logcheck                              # log không chứa tên bệnh nhân seed → phải trả 0
```

## Biến môi trường
| Biến | Bắt buộc | Ghi chú |
|---|---|---|
| `DOMAIN` | ✅ | Caddy dùng để xin chứng chỉ Let's Encrypt |
| `ANTHROPIC_API_KEY` | ✅ | chỉ đặt trong `.env`, không commit, không in ra log |
| `POSTGRES_PASSWORD` | ✅ | |
| `JWT_SECRET` | ✅ | đổi giá trị này làm đăng xuất toàn bộ phiên — chấp nhận ở demo |
| `LLM_BASE_URL` | — | đặt khi dùng adapter model nội bộ (P1-9) |
| `LLM_MODEL_ID` | ✅ | ghi vào mỗi bản tóm tắt để tái tạo |

## Vận hành
- **Benchmark:** `make benchmark` (cần `ANTHROPIC_API_KEY`). Chạy trên máy dev hoặc máy ảo; xuất `reports/bench_<ts>/`.
- **Sao lưu:** `make dump` → file `.sql` + `.sha256`. Dữ liệu mô phỏng nên không nhạy cảm, nhưng **vẫn không** đặt lên bucket công khai.
- **Log:** `docker compose logs api`. Không chứa nội dung hồ sơ (`make logcheck` kiểm tra tự động trong CI).
- **Freeze Demo Day:** tag `demo-day` từ 10/12. Sau mốc này chỉ hotfix qua nhánh `release` có người thứ hai duyệt.

## Bảo mật khi triển khai
HTTPS bắt buộc (Caddy tự cấp). Secrets chỉ trong `.env` với quyền `600`. CORS khóa đúng `DOMAIN`. Không mở cổng 5432 ra ngoài. Tài khoản demo cho giám khảo có quyền `doctor` trên tập bệnh nhân riêng, không phải `admin`.

## Checklist trước khi công bố URL (điều kiện RC 9/12)
- [ ] `/healthz` trả ok qua HTTPS, chứng chỉ hợp lệ
- [ ] Bác sĩ A mở bệnh nhân của bác sĩ B → 403 **và** có dòng `denied` trong nhật ký
- [ ] Sửa bản đã duyệt → 409
- [ ] Duyệt khi còn cờ Đỏ chưa ack → 422
- [ ] Banner "DỮ LIỆU MÔ PHỎNG" và nhãn AI hiện trên mọi trang sau đăng nhập
- [ ] p95 trên 10 bệnh nhân ≤ 60 giây; không job nào vượt timeout 90 giây
- [ ] `make logcheck` pass
- [ ] Tài khoản demo hoạt động trên máy khác (không cache)
- [ ] Tập dượt demo đủ 2 lần
