# CHANGELOG — Cập nhật tài liệu thiết kế theo codebase RAG-SQLite

> Đối chiếu tài liệu `RAG-chatbot-docs` với codebase `RAG-SQLite-main` và sửa các điểm sai/thiếu.
> Phương pháp: trích claim tự động + đối chiếu trực tiếp với source code (không suy đoán).
> Ngày: cập nhật theo phiên làm việc.

## Các gap đã sửa

### G1.1 — Phần 1 §6.1: ContentTypeClassifier 7 → 8 rule
- **Sai:** Tài liệu ghi classifier có 7 rule (R1–R7).
- **Đúng (code `shared/classifiers/query_classifier.py`):** 8 rule — R1, R2, R3, R4, R5, R6, **R_DOC_REF**, R7.
- **R_DOC_REF:** match mã văn bản pháp lý VN (vd `15/2024/TT-NHNN`), content_type=legal, confidence=0.92, đặt **sau** R2–R6 (để tabular thắng khi query có cả mã VB lẫn từ khóa phí/lãi). Confidence 0.92 (≥0.90) kích hoạt confidence guard trong `_prepare_unified()`.
- **Đồng bộ:** sửa thêm 4 chỗ text khác (caption Hình 5.1a, §5.2, §7.2 của P1, §4.1 P2), 2 README, và 4 sơ đồ Mermaid (hinh_6_1, hinh_5_1a, hinh_10_1 ở P1; hinh_4_1 ở P2).
- **Sơ đồ hinh_6_1:** vẽ thêm node R_DOC_REF (render lại PNG, fit A4 = 13.9cm).

### G1.2 — Phần 1 §6.1: giá trị type_tab_hint
- **Sai:** ví dụ `PHI_CANHAN`, `PHI_TOCHUC`, `LAI_SUAT` (gạch dưới).
- **Đúng (code):** `PHI-DCTC` (định chế tài chính), `PHI-TOCHUC` (tổ chức), `PHI-CANHAN` (cá nhân), `LAI_SUAT`. Ưu tiên DCTC > TOCHUC > CANHAN; phí chung chung → `None`.

### G2.1 — Phần 2 §4.4: aggregation_intent không có giá trị "LOOKUP"
- **Sai:** Tài liệu ghi `aggregation_intent = "LOOKUP"` và liệt kê LOOKUP trong bảng intent.
- **Đúng (code `shared/query_analyzer.py`):** mặc định `None`; chỉ có 4 giá trị `COMPARE/MIN/MAX/LIST_ALL`. Aggregator chỉ chạy khi intent khác None VÀ số kết quả ≥ `min_rows_for_table` (=2).
- Cũng sửa `numeric_entities` từ dạng dict sang dataclass thật `NumericEntity(value, unit, raw_text, position)` (field là `unit`, không phải `type`).

### G2.2 — Phần 2 §3.5: bổ sung khóa config production thiếu trong retrieval_config.yaml
- Thêm: `vector.distance_metric`, `context_window.window_size`, `postprocess.article_expansion_timeout: 5.0`, block `unified_dispatch.enabled: false`, `feature_flags.use_shared_bm25_normalize: true`.
- Thêm ghi chú về **mirror flag**: `unified_dispatch.enabled` có ở cả 2 file (retrieval_config=false, generation_config=true); giá trị có hiệu lực thực tế là bản ở generation_config.

### G2.3 — Phần 2 §4.4: đường dẫn query_analyzer.py
- Sửa comment `# query_analyzer.py` → `# shared/query_analyzer.py` cho khớp vị trí thật.

### G4.1 — Phần 4 (Phụ lục C): đường dẫn retriever sai
- **Sai:** `phase2_retrieval/src/retrievers/adaptive.py` và `.../hybrid.py` (không tồn tại).
- **Đúng:** logic adaptive ở `src/orchestrator.py`; file hybrid thật là `hybrid_retriever.py`.

## Các điểm đã kiểm và XÁC NHẬN ĐÚNG (không sửa)
3 LLM provider; unified_dispatch=true; followup=false; bcrypt==4.0.1; ports 8001/8082/5173; 5 CSDL; JWT HS256 + TTL 30'/7 ngày; rate limit 20/60/10 per minute; brute-force 5 lần/khóa 15'; ROLE_RANK 4 cấp + 3 helper assert; toàn bộ versions package.json; slot_detector; contextual_condenser; 12 ADR.

## Cảnh báo script gắn cờ nhầm (tài liệu vốn đúng)
- `RequestLoggerMiddleware`: tài liệu đã dùng đúng tên ở phần chi tiết.
- `useSessionStore`: tài liệu đã mô tả chính xác (export từ `hooks/useSessions.js`).

## File đã build lại
- `phan_1_tong_quan.docx` + `.pdf` (53 trang) — verify pdftoppm trang 21 (bảng 8 rule) + trang 24 (sơ đồ R_DOC_REF).
- `phan_2_rag_core.docx` + `.pdf` — verify trang 41 (sơ đồ 8 rules) + config mới.
- `phan_4_agribank_chat.docx` + `.pdf`.
- Phần 3 (BFF) không thay đổi.

## Lưu ý
- File `4-Agribank-chat/source/build_docx.py` được THÊM MỚI (copy từ P2) vì zip gốc không có build script cho P3/P4. Có thể giữ để build lại P4 sau này.
- Render Mermaid yêu cầu Chrome cho puppeteer (mmdc); đã cài Chrome for Testing 131 trong môi trường build.
