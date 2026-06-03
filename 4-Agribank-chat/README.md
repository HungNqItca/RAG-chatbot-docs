# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

**Bộ tài liệu hoàn chỉnh** chuẩn bị cho công tác đào tạo và chuyển giao công nghệ.

## ✅ Trạng thái — TẤT CẢ 4 PHẦN HOÀN THÀNH

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|------|----------|---------:|---------:|:----------:|
| **1** | Tổng quan hệ thống | 48 | 10 | ✅ Hoàn thành |
| **2** | RAG Core (Phase 1/2/3) | 60 | 14 | ✅ Hoàn thành |
| **3** | BFF Service | 36 | 8 | ✅ Hoàn thành |
| **4** | **agribank-chat (UI) + Phụ lục** | **38** | **10** | ✅ **Hoàn thành** |

**Tổng cộng: 182 trang, 42 sơ đồ — tất cả TB orientation và fit A4 portrait.**

---

## Phần 4 — agribank-chat (UI) + Phụ lục — MỚI HOÀN THÀNH

### Cấu trúc 12 mục

| § | Tiêu đề | Sơ đồ |
|---|---|:-:|
| 1 | Giới thiệu Phần 4 (3 nguyên tắc UI: streaming-first, optimistic UI, defensive client guards) | — |
| 2 | **Kiến trúc tổng thể & cấu trúc thư mục** (stack thực tế, thư mục src/) | **Hình 1.1, 2.1** |
| 3 | **Auth flow + JWT refresh interceptor** (sessionStorage, queue-based refresh) | **Hình 3.1** |
| 4 | **State management với Zustand** (3 stores · selector hooks · pure helpers) | **Hình 4.1** |
| 5 | **useChat hook + SSE streaming** (sendMessage, sseClient, 5 event types, needsHistoryRestore) | **Hình 5.1a, 5.1b, 5.2** |
| 6 | **Component tree & UI design** (ChatPage layout, MessageBubble, SourceCard variants) | **Hình 6.1** |
| 7 | **Routing & Guards** (ProtectedRoute, AdminRoute, RBAC client-side) | **Hình 7.1** |
| 8 | **Error handling hierarchy** (ErrorBoundary, Toast, banner inline) | **Hình 8.1** |
| 9 | **Phụ lục A — API Contracts** (BFF ↔ FE — SSE event schemas đầy đủ) | — |
| 10 | **Phụ lục B — Glossary** (40+ thuật ngữ tổng hợp 4 phần) | — |
| 11 | **Phụ lục C — File ↔ chức năng** (3 service tra cứu khi debug) | — |
| 12 | **Phụ lục D — 7 ADR** (Zustand vs Redux, sessionStorage, fetch vs EventSource, refresh queue...) | — |

### 10 sơ đồ Phần 4 (tất cả TB)

| # | Sơ đồ | Vai trò |
|:-:|---|---|
| 1 | hinh_1_1_kien_truc_fe | Kiến trúc 4 lớp FE (Router → Pages → Stores + Services) |
| 2 | hinh_2_1_cau_truc_thu_muc | Cây thư mục src/ theo concern |
| 3 | hinh_3_1_auth_flow | JWT refresh queue: 401 → refresh → retry pattern |
| 4 | hinh_4_1_zustand_stores | 3 stores: authStore (persist) · sessionStore · toastStore |
| 5-6 | hinh_5_1a / hinh_5_1b | sendMessage setup → streamChat lifecycle (chia 2) |
| 7 | hinh_5_2_sse_events | 5 SSE event types (meta/token/done/followups/error) |
| 8 | hinh_6_1_component_tree | ChatPage component tree (Sidebar + MessageList + InputBar) |
| 9 | hinh_7_1_routing_guards | ProtectedRoute + AdminRoute với RBAC check |
| 10 | hinh_8_1_error_handling | 4 loại lỗi → 4 cách hiển thị khác nhau |

### Highlight nội dung Phần 4

| Mục | Bổ sung trong tài liệu đào tạo |
|---|---|
| **Stack thực tế** | React 18 + Vite 5 + Zustand + axios + react-virtuoso + react-markdown — KHÔNG phải Tailwind, dùng CSS Modules với design tokens |
| **sessionStorage rationale** | Tự xóa khi đóng tab — phù hợp internal banking tool. Đánh đổi: 2 tab = 2 session độc lập |
| **fetch + ReadableStream vs EventSource** | EventSource không hỗ trợ Authorization header → custom SSE client |
| **Refresh queue pattern** | Race condition khi nhiều request cùng 401 → solution với `_refreshing` flag + `_queue` array |
| **Optimistic UI** | userMsg + typingMsg append ngay khi user submit, không đợi server |
| **Accumulator + replace last** | onToken: `accContent +=` rồi `setMessages → [...prev.slice(0,-1), {...last, content: acc}]` |
| **Selector hooks vs pure helper** | useUserRole/useIsAdmin (hook, top-level) vs hasMinimumRole (pure, dùng được trong closure) |
| **needsHistoryRestore** | Flag set khi load session > TTL 72h, consume một lần rồi reset ngay |
| **registerSession upsert** | Logic merge: nếu existing có title fallback và firstQuestion thực → update; nếu có title thực → bỏ qua |
| **react-virtuoso** | Virtual scrolling cho session dài, tránh re-render Markdown nặng |
| **SourceCard dispatch** | check `content_type === 'tabular'`, KHÔNG dùng `!== 'legal'` (legal sources không có field này) |
| **ErrorBoundary class component** | Hooks không hỗ trợ `componentDidCatch` |
| **Phụ lục B Glossary** | 40+ thuật ngữ với reference Phần (1/2/3/4) — tra cứu nhanh |
| **Phụ lục C File ↔ chức năng** | Khi debug, tra triệu chứng → biết file nào cần xem (cả 3 service) |
| **7 ADR cuối tài liệu** | Tách section riêng — Zustand vs Redux, sessionStorage, EventSource, optimistic UI, react-virtuoso, react-markdown, refresh queue |

---

## Phần 1-3 — Tổng hợp

### Phần 1 — 10 sơ đồ
| # | Sơ đồ | Mục |
|:-:|---|---|
| 1 | hinh_3_1_kien_truc_3tang | §3 Kiến trúc 3 tầng |
| 2-3 | hinh_5_1a / hinh_5_1b | §5 Sequence E2E (chia 2 phần) |
| 4 | hinh_6_1_dispatch_logic | §6 Content Type Dispatch |
| 5-6 | hinh_7_1a / hinh_7_1b | §7 rag-core phases · scripts ops |
| 7 | hinh_7_2_module_bff | §7.2 BFF mixin architecture |
| 8 | hinh_9_1_thu_tu_khoi_dong | §9 Thứ tự khởi động |
| 9 | hinh_10_1_vong_doi_du_lieu | §10 Vòng đời dữ liệu |
| 10 | hinh_11_1_production | §11 Triển khai production |

### Phần 2 — 14 sơ đồ
| # | Sơ đồ | Mục |
|:-:|---|---|
| 1-2 | hinh_2_1a / hinh_2_1b | §2.1 Pipeline Legal · Pipeline Tabular |
| 3-4 | hinh_2_2a / hinh_2_2b | §2.2.6 Saga Forward · Saga Rollback |
| 5-6 | hinh_2_3a / hinh_2_3b | §2.3 EAV Structure · search_text+BM25 |
| 7 | hinh_3_1_retrieval_strategies | §3.1 Bốn chiến lược |
| 8-9 | hinh_3_2a / hinh_3_2b | §3.2 Hybrid+RRF · Rerank+Post |
| 10 | hinh_3_3_tabular_retrieval | §3.3 Tabular retrieval |
| 11 | hinh_4_1_orchestrator_dispatch | §4.2 GenerationOrchestrator |
| 12-13 | hinh_4_2a / hinh_4_2b | §4.3 Tiền xử lý · Decision tree |
| 14 | hinh_4_3_tabular_handler | §4.4 TabularGenerationHandler |

### Phần 3 — 8 sơ đồ
| # | Sơ đồ | Mục |
|:-:|---|---|
| 1 | hinh_1_1_kien_truc_bff | Kiến trúc 3 lớp BFF |
| 2 | hinh_2_1_userdb_mixin | UserDB shell + 7 mixin |
| 3 | hinh_3_1_jwt_lifecycle | JWT lifecycle |
| 4 | hinh_3_2_rbac_rank_check | RBAC decision tree |
| 5 | hinh_4_1_login_bruteforce | Login với 3 lớp kiểm tra |
| 6 | hinh_5_1_sse_proxy | SSE streaming flow |
| 7 | hinh_6_1_reconcile | Session reconcile |
| 8 | hinh_7_1_schema_users_db | Schema 5 bảng SQLite |

---

## Cấu trúc thư mục (FINAL)

```
RAG_TaiLieu_ThietKe/
├── README.md
├── phan_1_tong_quan.docx + .pdf       ← 48 trang, 10 sơ đồ
├── phan_2_rag_core.docx + .pdf         ← 60 trang, 14 sơ đồ
├── phan_3_bff_service.docx + .pdf      ← 36 trang, 8 sơ đồ
├── phan_4_agribank_chat.docx + .pdf    ← 38 trang, 10 sơ đồ
├── source/
│   ├── phan_1_tong_quan.md
│   ├── phan_2_rag_core.md
│   ├── phan_3_bff_service.md
│   ├── phan_4_agribank_chat.md
│   └── build_docx.py                   ← script với auto-scale image
├── mermaid/                            ← 42 file .mmd source
└── png/                                ← 42 PNG render
```

## Nguyên tắc design sơ đồ (đã áp dụng nhất quán cả 4 phần)

1. **TB-only orientation** — Mọi flowchart đều `flowchart TB`. Không LR (chữ quá nhỏ trên A4).
2. **Một sơ đồ — một concept** — Không ghép nhiều luồng. Sơ đồ phức tạp được chia thành sub-diagrams (a, b) có cross-reference.
3. **Compact node labels** — Mỗi node 1-2 dòng ngắn. Chi tiết dài đặt trong text markdown bên cạnh.
4. **Auto-scale fallback** — `build_docx.py` tự động giảm width image nếu vẫn vượt height A4 (25cm).

## Cách rebuild bất kỳ phần nào

```bash
cd RAG_TaiLieu_ThietKe
cp source/phan_4_agribank_chat.md ./phan_4_agribank_chat.md
python3 source/build_docx.py phan_4_agribank_chat.md phan_4_agribank_chat.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 4" \
    --version "Bản chính thức"
soffice --headless --convert-to pdf phan_4_agribank_chat.docx
```

## Đặc tả tài liệu

- **Style:** Times New Roman 13pt body · heading xanh thẫm `#1F3A6E` · H1/H2 đậm · H3/H4 thường.
- **Trang:** A4 portrait (21 × 29.7 cm), lề top/bottom 2cm, left 2.5cm, right 2cm.
- **Sơ đồ:** Mermaid TB orientation · `nodeSpacing: 30, rankSpacing: 30, padding: 8`.
- **Ngôn ngữ:** Tiếng Việt cho toàn bộ nội dung; thuật ngữ kỹ thuật giữ tiếng Anh.
- **Đối tượng đọc:** Kỹ sư mới gia nhập đội phát triển/vận hành — viết theo lối "Why → What → How".
