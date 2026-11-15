# docs/20 — LICENSES & nguồn dữ liệu ngoài ✅ (rà 15/11/2026)

| Nguồn | Giấy phép / điều kiện | Cách dùng trong dự án | Nghĩa vụ phải thực hiện |
|---|---|---|---|
| **QĐ 5481/QĐ-BYT** (30/12/2020) và **QĐ 1353/QĐ-BYT** (23/02/2021) | Tài liệu chuyên môn do Bộ Y tế ban hành kèm quyết định — trích dẫn có ghi nguồn | Ngưỡng, chống chỉ định, tần suất xét nghiệm, mục tiêu điều trị cho 11/17 quy tắc | Trích đúng, ghi số văn bản + mục/bảng; bản gốc lưu trong `refs/` kèm SHA-256; mọi trích dẫn ghi "đã sửa đổi bởi QĐ 1353/QĐ-BYT" |
| **DDInter 2.0** (ddinter2.scbdd.com) | **CC BY-NC-SA 4.0**, truy cập mở (xác nhận 15/11/2026) | Bảng con ~25 hoạt chất, cặp tương tác mức "major" (P1-4) | Ghi attribution bài gốc (Nucleic Acids Research 2025); dùng phi thương mại — phù hợp đồ án; nếu thương mại hóa phải thay nguồn; sản phẩm phái sinh chia sẻ cùng giấy phép |
| **LOINC** | Bản quyền Regenstrief Institute; dùng miễn phí theo LOINC License | Mã xét nghiệm (docs/18) | Ghi notice LOINC trong README; ghi phiên bản LOINC đã dùng |
| **WHO ATC/DDD** | WHOCC — phi thương mại, ghi nguồn | Mã thuốc | Ghi nguồn WHOCC + ngày tra |
| **ICD-10** | WHO; bản dịch/phân loại dùng tại Việt Nam theo Bộ Y tế | Mã bệnh | Ghi nguồn |
| **VN Core FHIR IG 0.10.0** (fhir.hl7.org.vn) | Theo giấy phép công bố trên trang IG — xác nhận khi tải gói | Profile, tên trường, validator | Ghi phiên bản IG + ngày tải; **lưu gói offline** trong repo (phòng rủi ro registry không tải được — PRD Phụ lục E) |
| **drugbank.vn / cổng Cục Quản lý Dược** | Dữ liệu công bố công khai | Tra biệt dược ↔ hoạt chất ↔ số đăng ký | Ghi URL + ngày tra cho **từng dòng** trong `data/drug_map.csv` |
| **Synthea** | Apache-2.0 | **Chỉ tham khảo** logic tiến triển bệnh; không dùng code hay dữ liệu trực tiếp | Nếu sau này dùng code: giữ nguyên notice Apache-2.0 |
| **PDSQI-9** (JAMIA 2025) | Công cụ nghiên cứu đã công bố | Dịch tiếng Việt để chấm nhóm C | Trích dẫn bài gốc; ghi rõ **bản dịch chưa chuẩn hóa** trong mọi báo cáo |
| **Bộ dữ liệu mô phỏng của đội** | **CC BY 4.0** (đề xuất) | Sản phẩm của dự án | Ghi trong Data Card; nêu rõ là dữ liệu tổng hợp, không phải dữ liệu bệnh nhân |
| **KHÔNG dùng** | MIMIC-IV (DUA cấm gửi lên API LLM bên thứ ba), ACI-Bench, MTS-Dialog, VietMed-Sum | — | Đã loại từ PRD mục 7; ghi lý do trong báo cáo cuối |

## Quy tắc chung
1. Mọi nguồn mới phải thêm một dòng vào bảng này **trước** khi merge code dùng nguồn đó.
2. `make benchmark` in đường dẫn file này vào báo cáo để người đọc biết nguồn nào đã dùng.
3. Nguồn có điều kiện phi thương mại (DDInter, WHOCC) được đánh dấu rõ trong code bằng comment tại chỗ dùng, để nếu dự án đổi mục đích thì tìm được ngay.
4. Không commit bất kỳ dữ liệu nào có giấy phép không cho phép phân phối lại vào repo công khai.
