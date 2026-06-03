# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

**Bộ tài liệu** chuẩn bị cho công tác đào tạo và chuyển giao công nghệ.

## Trạng thái

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|------|----------|---------:|---------:|:----------:|
| **1** | Tổng quan hệ thống | 48 | 10 | ✅ Hoàn thành |
| **2** | RAG Core (Phase 1/2/3) | 60 | 14 | ✅ Hoàn thành |
| **3** | **BFF Service** | **36** | **8** | ✅ **Hoàn thành** |
| 4 | agribank-chat (UI) + Phụ lục | — | — | ⏳ |

**Tổng: 144 trang, 32 sơ đồ — tất cả TB orientation và fit A4 portrait.**

## Phần 3 — BFF Service (mới)

### Cấu trúc 7 mục

| § | Tiêu đề | Sơ đồ |
|---|---|:-:|
| 1 | Giới thiệu Phần 3 (bối cảnh, 3 nguyên tắc thiết kế) | — |
| 2 | **Kiến trúc tổng thể** (3 lớp, cấu trúc thư mục, lifespan) | **Hình 1.1** |
| 3 | **UserDB — Mixin Architecture** (7 mixin, schema 5 bảng) | **Hình 2.1, 7.1** |
| 4 | **JWT + RBAC 4 cấp** (lifecycle, rank-aware permissions) | **Hình 3.1, 3.2, 4.1** |
| 5 | **SSE Proxy & Session Ownership** (streaming, reconcile) | **Hình 5.1, 6.1** |
| 6 | **Middleware & API Design** (LIFO stack, endpoints, .env) | — |
| 7 | **ADR — 7 quyết định thiết kế quan trọng** | — |

### 8 sơ đồ Phần 3 (tất cả TB)

| # | Sơ đồ | Vai trò |
|:-:|---|---|
| 1 | hinh_1_1_kien_truc_bff | Kiến trúc 3 lớp (FE → BFF → RC) |
| 2 | hinh_2_1_userdb_mixin | UserDB shell + 7 mixin class |
| 3 | hinh_3_1_jwt_lifecycle | JWT lifecycle (login → access → refresh) |
| 4 | hinh_3_2_rbac_rank_check | RBAC decision tree + self-protect |
| 5 | hinh_4_1_login_bruteforce | Login với 3 lớp kiểm tra (lock/password/active) |
| 6 | hinh_5_1_sse_proxy | SSE streaming flow + early-save title |
| 7 | hinh_6_1_reconcile | Session reconcile với guard active_ids=[] |
| 8 | hinh_7_1_schema_users_db | Schema 5 bảng SQLite + ON DELETE CASCADE |

### Highlight nội dung Phần 3

| Mục | Bổ sung trong tài liệu đào tạo |
|---|---|
| **Mixin Architecture** | Lý do tách 7 mixin; bất biến `PRAGMA foreign_keys=ON`; migration chain idempotent với raw conn riêng |
| **Random admin password** | Không có default `admin123` — buộc đọc log để lấy mật khẩu lần đầu |
| **`assert_can_assign_role` dùng `>=`** | Sự tinh tế: admin KHÔNG thể gán role admin (chỉ superadmin được). Nếu dùng `>` sẽ mất tính phân tầng |
| **3 self-protect rules** | Admin không thể tự xóa, tự khóa, tự đổi role — tránh trạng thái không khôi phục được |
| **`httpx.stream()` vs `.post()`** | Code so sánh tường minh + giải thích vì sao streaming-first |
| **Buffer cap 64KB** | Code matrix lỗi đầy đủ + giải thích threat OOM |
| **Early-save title** | Bug gốc: title không lưu khi stream gián đoạn → sidebar "Hội thoại mới" |
| **`effective_sources` vs `acc_sources`** | Logic prefer effective_sources từ done event, fallback acc_sources từ meta |
| **Conservative cleanup** | Bug gốc: `BFF_RELOAD=true` + RAG Core restart → wipe `user_sessions`. Fix bằng guard `active_ids=[]` |
| **Decode JWT ở middleware** | Vì sao decode 1 lần ở RequestLogger, sau đó rate limiter chỉ đọc `request.state.user_id` |
| **`TRUSTED_PROXIES`** | Threat model IP spoofing và cách validate trước khi trust X-Forwarded-For |
| **7 ADR** | Tách section riêng cuối tài liệu — tra cứu nhanh quyết định và đánh đổi |

## Phần 1 — 10 sơ đồ
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

## Phần 2 — 14 sơ đồ
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

## Cấu trúc thư mục

```
RAG_TaiLieu_ThietKe/
├── README.md
├── phan_1_tong_quan.docx              ← 48 trang, 10 sơ đồ
├── phan_1_tong_quan.pdf
├── phan_2_rag_core.docx                ← 60 trang, 14 sơ đồ
├── phan_2_rag_core.pdf
├── phan_3_bff_service.docx             ← 36 trang, 8 sơ đồ — MỚI
├── phan_3_bff_service.pdf
├── source/
│   ├── phan_1_tong_quan.md
│   ├── phan_2_rag_core.md
│   ├── phan_3_bff_service.md            ← MỚI
│   └── build_docx.py
├── mermaid/                            ← 32 file .mmd (30 TB + 2 sequence)
└── png/                                ← 32 PNG render
```

## Nguyên tắc design sơ đồ (đã thống nhất)

1. **TB-only orientation** — Mọi flowchart đều `flowchart TB`. Không LR (chữ quá nhỏ trên A4).
2. **Một sơ đồ — một concept** — Không ghép Legal+Tabular vào một hình. Sơ đồ phức tạp được chia thành sub-diagrams.
3. **Compact node labels** — Mỗi node 1-2 dòng ngắn. Chi tiết dài đặt trong text markdown bên cạnh.
4. **Auto-scale fallback** — `build_docx.py` tự động giảm width image nếu vẫn vượt height A4 (25cm).

## Cách rebuild

```bash
cd RAG_TaiLieu_ThietKe
cp source/phan_3_bff_service.md ./phan_3_bff_service.md
python3 source/build_docx.py phan_3_bff_service.md phan_3_bff_service.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 3" \
    --version "Bản chính thức"
```

## Đặc tả tài liệu

- **Style:** Times New Roman 13pt body · heading xanh thẫm `#1F3A6E` · H1/H2 đậm · H3/H4 thường.
- **Trang:** A4 portrait (21 × 29.7 cm), lề top/bottom 2cm, left 2.5cm, right 2cm. Content area 16.5cm × 25.7cm.
- **Sơ đồ:** Mermaid TB orientation · `nodeSpacing: 30, rankSpacing: 30, padding: 8` · auto-scale image fit page.
- **Ngôn ngữ:** Tiếng Việt cho toàn bộ nội dung.
