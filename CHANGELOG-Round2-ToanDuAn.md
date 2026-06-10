# CHANGELOG — Cập nhật & mở rộng tài liệu thiết kế (toàn dự án RAG-SQLite)

> Đối chiếu tài liệu thiết kế với codebase `RAG-SQLite-main` (bản mới nhất), sửa các điểm sai/lỗi thời và **bổ sung Phần 5 — Hạ tầng & Vận hành** cho các thành phần chưa được tài liệu hóa.
> Phương pháp: đọc trực tiếp source code (CI/CD, monitoring, middleware, migrations, nginx, scripts) làm căn cứ; không suy đoán.

## A. Sửa 4 phần cũ cho khớp code hiện tại (6 gap — đã xác nhận còn đúng với code mới)

- **G1.1 (P1 §6.1):** ContentTypeClassifier 7 → 8 rule, bổ sung `R_DOC_REF` (mã văn bản pháp lý VN, conf 0.92, đặt sau R2–R6). Đồng bộ 5 chỗ text + 2 README + 4 sơ đồ Mermaid (vẽ thêm node R_DOC_REF vào Hình 6.1).
- **G1.2 (P1 §6.1):** `type_tab_hint` đúng giá trị `PHI-DCTC / PHI-TOCHUC / PHI-CANHAN / LAI_SUAT`, ưu tiên DCTC > TOCHUC > CANHAN.
- **G2.1 (P2 §4.4):** Bỏ giá trị enum `LOOKUP` không tồn tại; `aggregation_intent` mặc định `None`, chỉ có `COMPARE/MIN/MAX/LIST_ALL`. Sửa dataclass `NumericEntity(value, unit, raw_text, position)`.
- **G2.2 (P2 §3.5):** Bổ sung khóa config `distance_metric`, `window_size`, `article_expansion_timeout`, `unified_dispatch.enabled` (mirror flag), `feature_flags.use_shared_bm25_normalize`.
- **G2.3 (P2 §4.4):** Sửa đường dẫn `shared/query_analyzer.py`.
- **G4.1 (P4):** Sửa đường dẫn retriever sai → `orchestrator.py` + `hybrid_retriever.py`.

## B. Bổ sung PHẦN 5 — Hạ tầng & Vận hành (mới, 25 trang, 6 sơ đồ)

Tài liệu hóa các thành phần codebase chưa từng được mô tả trong 4 phần cũ:

| § | Nội dung | Nguồn code |
|---|---|---|
| 2 | Topology production: 7 service, container hardening (cap_drop/read_only), blue-green | `docker-compose.prod.yml` |
| 3 | CI/CD: lint→test→build→trivy→deploy→smoke; auto-rollback | `.github/workflows/ci-cd.yml`, `scripts/gcp/05_deploy_vm.sh`, `99_rollback.sh` |
| 4 | Observability: Prometheus scrape, 6 alert rule, MetricsMiddleware, Grafana | `monitoring/`, `bff-service/middleware/metrics.py`, `rate_limiter.py` |
| 5 | Migrations: yoyo, 5 migration gồm JWT key rotation + chat feedback | `bff-service/migrations/0001..0005` |
| 6 | Nginx & HTTPS: 2 biến thể GCP/on-prem, cấu hình SSE | `nginx/https.conf`, `nginx/agribank.conf` |
| 7 | Backup/Restore + lớp trừu tượng provider khả chuyển | `scripts/gcp/08_backup`, `09_restore_test`, `scripts/lib/*_provider.sh` |
| 8 | 6 ADR hạ tầng | tổng hợp |

## C. File đã build lại
- Phần 1, 2, 4: rebuild docx + pdf (sơ đồ classifier 8 rule + R_DOC_REF).
- Phần 5: mới — docx + pdf (25 trang).
- Phần 3 (BFF): nội dung cũ giữ nguyên; các tính năng mới (JWT rotation, chat feedback, Redis rate-limit, MetricsMiddleware) được tài liệu hóa ở Phần 5 với cross-reference về Phần 3.
- README.md: cập nhật thành 5 phần.

## D. Ghi chú
- `build_docx.py` được copy vào source Phần 4 và Phần 5 (zip gốc chỉ có ở P1/P2).
- Sơ đồ Phần 5 đều dùng `flowchart TB` theo ràng buộc; 2 sơ đồ chuỗi tuyến tính (5.2 CI/CD, 5.4 migrations) cao hơn 25cm và được `build_docx.py` auto-scale fit A4 (đúng cơ chế §3.6 của Docs-Design-Requirement) — đã verify chữ vẫn đọc rõ.
- Render Mermaid cần Chrome cho puppeteer (đã cài Chrome for Testing 131).

## E. Khuyến nghị cho lần sau (chưa làm trong đợt này)
- Phần 3 (BFF) có thể bổ sung trực tiếp các mục: bảng `jwt_keys` + cơ chế key rotation, bảng `chat_feedback`, Redis-backed rate limiting, MetricsMiddleware — hiện đang đặt ở Phần 5. Nếu muốn giữ nguyên tắc "mỗi service một phần", nên di chuyển các mục này về Phần 3 và để Phần 5 chỉ cross-reference.
