# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

Bộ tài liệu kỹ thuật chuẩn bị cho công tác đào tạo và chuyển giao công nghệ — 6 phần, viết theo lối **Why → What → How**.

## Trạng thái

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|:----:|----------|---------:|---------:|:----------:|
| **1** | Tổng quan hệ thống | 48 | 10 | ✅ Hoàn thành |
| **2** | RAG Core (Phase 1/2/3) | 60 | 14 | ✅ Hoàn thành |
| **3** | BFF Service | 36 | 8 | ✅ Hoàn thành |
| **4** | agribank-chat (UI) + Phụ lục | 38 | 10 | ✅ Hoàn thành |
| **5** | Hạ tầng & Vận hành (DevOps) | 25 | 6 | ✅ Hoàn thành |
| **6** | Nâng cấp Agentic RAG | 30 | 7 | ✅ Hoàn thành |

---

## Phần 1 — Tổng quan hệ thống

Giới thiệu kiến trúc 3 tầng (RAG Core · BFF · Frontend), bối cảnh nghiệp vụ pháp lý ngân hàng, luồng xử lý end-to-end 46 bước, cơ chế phân loại nội dung (Content Type Dispatch), cấu trúc mã nguồn, vận hành và triển khai production.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 3 | Kiến trúc tổng thể — 3 tầng | Hình 3.1 |
| 5 | Luồng end-to-end 6 giai đoạn (~46 bước) | Hình 5.1a, 5.1b |
| 6 | Content Type Dispatch (8 rule) | Hình 6.1 |
| 7 | Cấu trúc mã nguồn | Hình 7.1a, 7.1b, 7.2 |
| 9 | Khởi động 3 service | Hình 9.1 |
| 10 | Vòng đời dữ liệu | Hình 10.1 |
| 11 | Triển khai production | Hình 11.1 |

---

## Phần 2 — RAG Core

Hai pipeline song song xử lý tài liệu pháp lý (Legal) và dữ liệu dạng bảng (Tabular), Saga pattern đảm bảo tính nhất quán khi indexing, cấu trúc EAV, 4 chiến lược retrieval, hybrid BM25+vector với RRF fusion và reranking, generation orchestrator với slot detection và decision tree.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 2.1 | Pipeline Legal · Pipeline Tabular | Hình 2.1a, 2.1b |
| 2.2 | Saga Forward · Saga Rollback | Hình 2.2a, 2.2b |
| 2.3 | EAV Structure · search_text + BM25 | Hình 2.3a, 2.3b |
| 3.1 | Bốn chiến lược retrieval | Hình 3.1 |
| 3.2 | Hybrid+RRF · Rerank+Post-processing | Hình 3.2a, 3.2b |
| 3.3 | Tabular retrieval | Hình 3.3 |
| 4.2 | GenerationOrchestrator dispatch | Hình 4.1 |
| 4.3 | Tiền xử lý · Decision tree | Hình 4.2a, 4.2b |
| 4.4 | TabularGenerationHandler | Hình 4.3 |

**Điểm nổi bật:**
- `unified_dispatch.enabled = true` (mặc định) — Unified pipeline thay Legacy
- `tabular.min_score = 0.005` — theo phân phối RRF score thực tế
- Saga pattern với compensating transaction đảm bảo không để indexing nửa chừng

---

## Phần 3 — BFF Service

Backend-for-Frontend (FastAPI) đứng giữa React UI và RAG Core: xác thực JWT, RBAC 4 cấp rank-aware, SSE proxy với session ownership, UserDB với 7-mixin architecture, SQLite 5 bảng.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 2 | Kiến trúc tổng thể BFF (3 lớp) | Hình 1.1 |
| 3 | UserDB — 7 mixin class · schema 5 bảng | Hình 2.1, 7.1 |
| 4 | JWT lifecycle · RBAC 4 cấp · Login brute-force | Hình 3.1, 3.2, 4.1 |
| 5 | SSE Proxy · Session reconcile | Hình 5.1, 6.1 |
| 7 | 7 Architecture Decision Records | — |

**Điểm nổi bật:**
- Admin password ngẫu nhiên khi khởi tạo — không có default `admin123`
- `assert_can_assign_role` dùng `>=` (admin không thể gán role bằng mình)
- 3 self-protect rules: admin không thể tự xóa, tự khóa, tự đổi role
- Guard `active_ids=[]` trong session reconcile tránh wipe toàn bộ session khi restart
- Buffer cap 64 KB cho SSE proxy — ngăn OOM attack

---

## Phần 4 — agribank-chat (UI) + Phụ lục

React 18 + Vite 5 + Zustand frontend: streaming-first UI, optimistic message append, SSE client tùy chỉnh (thay EventSource), JWT refresh queue chống race condition, virtual scrolling với react-virtuoso.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 2 | Kiến trúc 4 lớp · cấu trúc thư mục src/ | Hình 1.1, 2.1 |
| 3 | Auth flow · JWT refresh queue (401 → refresh → retry) | Hình 3.1 |
| 4 | Zustand 3 stores · selector hooks · pure helpers | Hình 4.1 |
| 5 | useChat hook · SSE streaming · 5 event types | Hình 5.1a, 5.1b, 5.2 |
| 6 | Component tree (ChatPage · Sidebar · MessageList) | Hình 6.1 |
| 7 | Routing · ProtectedRoute · AdminRoute · RBAC | Hình 7.1 |
| 8 | Error handling hierarchy (4 loại · 4 cách hiển thị) | Hình 8.1 |
| A | API Contracts — SSE event schema đầy đủ | — |
| B | Glossary — 40+ thuật ngữ (tổng hợp cả 4 phần) | — |
| C | File ↔ Chức năng — tra cứu khi debug | — |
| D | 7 Architecture Decision Records | — |

**Điểm nổi bật:**
- CSS Modules + design tokens — **không dùng Tailwind**
- `fetch + ReadableStream` thay `EventSource` (EventSource không hỗ trợ Authorization header)
- Refresh queue với `_refreshing` flag + `_queue[]` — xử lý nhiều request cùng 401
- Accumulator pattern: `onToken` cộng dồn rồi replace phần tử cuối của messages array
- `needsHistoryRestore` flag — load lại history khi session > 72h TTL

---

## Cấu trúc repository

```
Tài liệu/
├── 1-Tong-quan/
│   ├── source/phan_1_tong_quan.md     ← Markdown nguồn
│   ├── source/build_docx.py           ← Script build .docx
│   ├── source/*.docx + *.pdf          ← File đã build
│   ├── mermaid/                       ← Sơ đồ nguồn (.md / .mmd)
│   └── png/                           ← PNG đã render
├── 2-Rag-core/                        ← Cấu trúc tương tự
├── 3-Bff-service/
├── 4-Agribank-chat/
├── 5-Ha-tang-Van-hanh/
├── 6-Agentic-rag/                     ← Nâng cấp Agentic RAG (mới)
└── so_do_RAG_TaiLieu/
    ├── mermaid/phan_1..6/             ← File .mmd gốc để render
    ├── png/phan_1..6/                 ← PNG render từ .mmd
    ├── mermaid_config.json
    └── puppeteer.json
```

---

## Rebuild tài liệu

### 1. Render sơ đồ Mermaid

```bash
mmdc -i so_do_RAG_TaiLieu/mermaid/phan_2/hinh_3_2a_hybrid_rrf.mmd \
     -o so_do_RAG_TaiLieu/png/phan_2/hinh_3_2a_hybrid_rrf.png \
     -c so_do_RAG_TaiLieu/mermaid_config.json \
     -p so_do_RAG_TaiLieu/puppeteer.json \
     -w 1400
```

### 2. Build .docx từ Markdown

```bash
cd 2-Rag-core/source
python3 build_docx.py phan_2_rag_core.md ../../phan_2_rag_core.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 2" \
    --version "Bản chính thức"
```

### 3. Xuất PDF (cần LibreOffice)

```bash
soffice --headless --convert-to pdf phan_2_rag_core.docx
```

### Cài đặt phụ thuộc

```bash
pip install python-docx Pillow
npm install -g @mermaid-js/mermaid-cli
# PDF: apt install libreoffice  (hoặc brew install libreoffice)
```

---

## Quy ước sơ đồ

| Quy tắc | Lý do |
|---------|-------|
| `flowchart TB` — không dùng `LR` | Chữ quá nhỏ khi xoay ngang trên A4 |
| Mỗi sơ đồ = một concept | Tránh quá tải; sơ đồ phức tạp chia a/b/c có cross-reference |
| Node label tối đa 2 dòng | Chi tiết dài đặt trong text bên cạnh sơ đồ |
| Auto-scale trong `build_docx.py` | Image luôn fit 1 trang A4 portrait (max 16×25 cm) |

## Đặc tả định dạng

- **Font:** Times New Roman 13pt (body) · Consolas 9.5pt (code)
- **Màu heading:** `#1F3A6E` (navy) · H1/H2 đậm · H3/H4 thường
- **Trang:** A4 portrait 21×29.7 cm · lề top/bottom 2 cm · left 2.5 cm · right 2 cm
- **Ngôn ngữ:** Tiếng Việt · thuật ngữ kỹ thuật giữ tiếng Anh

---

## Phần 5 — Hạ tầng & Vận hành

Lớp DevOps và vận hành sản xuất: topology triển khai trên GCE VM (7 service, container hardening, blue-green), pipeline CI/CD (lint → test → build → Trivy scan → deploy an toàn với auto-rollback), giám sát Prometheus + Grafana (6 quy tắc cảnh báo), tiến hóa lược đồ CSDL bằng yoyo-migrations (5 migration gồm JWT key rotation và chat feedback), cấu hình Nginx + HTTPS (2 biến thể GCP/on-premise), và chiến lược sao lưu + kiểm tra phục hồi tự động qua lớp trừu tượng provider khả chuyển.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 2 | Kiến trúc triển khai production | Hình 5.1 |
| 3 | Quy trình CI/CD | Hình 5.2 |
| 4 | Giám sát & Cảnh báo | Hình 5.3 |
| 5 | Tiến hóa lược đồ CSDL | Hình 5.4 |
| 6 | Nginx & HTTPS | Hình 5.5 |
| 7 | Sao lưu, Phục hồi & Khả chuyển | Hình 5.6 |

**Điểm nổi bật:**
- Deploy an toàn không staging: backup → smoke test → **auto-rollback**
- Container hardening: `cap_drop: ALL` · `read_only` · `no-new-privileges`
- Khả chuyển GCP → on-premise qua lớp trừu tượng `secrets_provider` / `storage_provider`

---

## Phần 6 — Nâng cấp Agentic RAG

Lớp khả năng mới mở rộng Phase 3 (Generation): vòng lặp ReAct cho phép LLM tự điều hướng gọi 4 tool (bọc hàm Phase 2/MongoDB) để trả lời câu hỏi **đa bước** — so sánh nhiều văn bản, kết hợp pháp lý + biểu phí — điều pipeline RAG-SQLite cũ không làm được. Toàn bộ bọc sau công tắc `agentic_rag` (mặc định `false` = hệ cũ nguyên trạng, zero-regression). CitationGuard verify exact-match mọi trích dẫn pháp lý chống hallucinate; Router phân loại simple/complex để câu đơn giản vẫn đi fast-path.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 2 | Kiến trúc tổng thể + công tắc `agentic_rag` | Hình 6.1 |
| 3 | Lớp Tool — 4 tool bọc hàm Phase 2/MongoDB | Hình 6.2 |
| 4 | Vòng lặp ReAct + parser phòng thủ | Hình 6.3a, 6.3b |
| 5 | CitationGuard — chống hallucinate điều khoản | Hình 6.4 |
| 6 | Router + AgentOrchestrator | Hình 6.5 |
| 7 | Streaming verify-trước-stream + frontend | Hình 6.6 |
| 8 | Eval 4 nhóm + bật dần + `/health` mode | Hình 6.7 |
| 9 | 8 Architecture Decision Records | — |

**Ba ràng buộc cứng (giữ trọn):**
- **Không hallucinate điều khoản** — CitationGuard verify exact-match trên dữ liệu tool thật + eval nhóm N4 đối kháng
- **Phạm vi đóng kín** — 4 tool, không có tool tra web → verify được 100% trích dẫn
- **Độc lập backend** — ReAct prompt-based (không native function-calling), sẵn sàng cho LLM local

**Điểm nổi bật:**
- Công tắc `agentic_rag` với cơ chế **hai đường khởi tạo** — `false` thì không import/khởi tạo bất kỳ thành phần agent nào
- **Verify-trước-stream**: CitationGuard chạy TRƯỚC token đầu tiên — không bao giờ stream trích dẫn chưa verify
- Budget cứng (max_iterations / max_tool_calls / timeout / error_fallback) chống loop vô hạn của model nhỏ
- Fast-path fallback xuyên suốt — agent lỗi/rỗng thì quay về pipeline cũ, không làm hỏng trải nghiệm
- Bật dần shadow → conservative qua `router_log_analyzer.py`; `/health` phản ánh `mode: agentic|classic`
- Nguồn: 6 giai đoạn A–F, **125 test pass, 0 fail**, đã deploy 2026-06-09
