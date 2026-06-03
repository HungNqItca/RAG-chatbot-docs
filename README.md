# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

Bộ tài liệu kỹ thuật chuẩn bị cho công tác đào tạo và chuyển giao công nghệ — 182 trang, 42 sơ đồ, viết theo lối **Why → What → How**.

## Trạng thái

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|:----:|----------|---------:|---------:|:----------:|
| **1** | Tổng quan hệ thống | 48 | 10 | ✅ Hoàn thành |
| **2** | RAG Core (Phase 1/2/3) | 60 | 14 | ✅ Hoàn thành |
| **3** | BFF Service | 36 | 8 | ✅ Hoàn thành |
| **4** | agribank-chat (UI) + Phụ lục | 38 | 10 | ✅ Hoàn thành |

---

## Phần 1 — Tổng quan hệ thống

Giới thiệu kiến trúc 3 tầng (RAG Core · BFF · Frontend), bối cảnh nghiệp vụ pháp lý ngân hàng, luồng xử lý end-to-end 46 bước, cơ chế phân loại nội dung (Content Type Dispatch), cấu trúc mã nguồn, vận hành và triển khai production.

| § | Tiêu đề | Sơ đồ |
|:-:|---------|:-----:|
| 3 | Kiến trúc tổng thể — 3 tầng | Hình 3.1 |
| 5 | Luồng end-to-end 6 giai đoạn (~46 bước) | Hình 5.1a, 5.1b |
| 6 | Content Type Dispatch (7 rule) | Hình 6.1 |
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
│   ├── mermaid/                       ← Sơ đồ .md (embed trong doc)
│   └── png/                           ← PNG đã render
├── 2-Rag-core/                        ← Cấu trúc tương tự
├── 3-Bff-service/
├── 4-Agribank-chat/
└── so_do_RAG_TaiLieu/
    ├── mermaid/phan_1..4/             ← File .mmd gốc để render
    ├── png/phan_1..4/                 ← PNG render từ .mmd
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
