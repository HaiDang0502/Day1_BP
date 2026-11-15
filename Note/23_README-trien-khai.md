# docs/23 — README triển khai (P0-11) 🟡 v1.0 — điền [domain], [vm] trước 1/12

## Yêu cầu hạ tầng
1 VM 2 vCPU/4 GB (Ubuntu 22.04+), tên miền trỏ A-record về VM, mở cổng 80/443. **Hạn có VM + domain: 1/12 (Ambiguity #4).**

## Một lệnh
```bash
git clone <repo> && cd vmec03
cp .env.example .env   # điền: ANTHROPIC_API_KEY, POSTGRES_PASSWORD, JWT_SECRET, DOMAIN
docker compose up -d   # services: caddy (auto-HTTPS), api, web, db
make seed              # nạp 30 BN + users demo (in mật khẩu 1 lần, không log)
```
Kiểm tra: `https://[domain]/healthz` → ok; đăng nhập doctor demo; chạy 1 bản tóm tắt end-to-end.

## Vận hành
- `make benchmark` (trên VM hoặc máy dev — cần ANTHROPIC_API_KEY).
- Backup: `make dump` → file sql + sha256 (dữ liệu mô phỏng, không nhạy cảm, vẫn không public bucket).
- Log: `docker compose logs api` — KHÔNG chứa nội dung hồ sơ (kiểm tra bằng `make logcheck`: grep mẫu tên BN seed trong log phải = 0).
- Freeze Demo Day: tag `demo-day` từ 10/12; chỉ hotfix qua nhánh release có duyệt.
## Secrets
Chỉ qua `.env` (không commit); xoay JWT_SECRET làm đăng xuất toàn bộ — chấp nhận ở demo. HTTPS bắt buộc: Caddy tự cấp Let's Encrypt theo DOMAIN.
## Checklist trước public URL
[ ] /healthz ok · [ ] 403+audit khi doctor mở BN không gán · [ ] 409 khi sửa bản duyệt · [ ] banner mô phỏng mọi trang · [ ] p95 trên 10 BN ≤ 60 s · [ ] logcheck pass · [ ] tập dượt demo 2 lần (điều kiện RC 9/12)
