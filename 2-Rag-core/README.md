# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

**Bộ tài liệu** chuẩn bị cho công tác đào tạo và chuyển giao công nghệ.

## Trạng thái

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|------|----------|---------:|---------:|:----------:|
| **1** | Tổng quan hệ thống | 48 | 10 | ✅ Hoàn thành |
| **2** | RAG Core (Phase 1/2/3) | 60 | 14 | ✅ Hoàn thành |
| 3 | BFF Service | — | — | ⏳ |
| 4 | agribank-chat (UI) + Phụ lục | — | — | ⏳ |

## Nguyên tắc design sơ đồ (đã thống nhất sau review)

Sau phản hồi *"xoay ngang chữ quá nhỏ → giữ dọc trong 1 trang A4"* — em áp dụng nghiêm ngặt:

1. **TB-only orientation:** Mọi flowchart đều `flowchart TB` (top-to-bottom). Không LR.
2. **Một sơ đồ — một concept:** Không ghép Legal + Tabular vào một hình. Sơ đồ phức tạp được chia thành 2-3 sub-diagrams có tham chiếu chéo (`→ Hình X.b`).
3. **Compact node labels:** Mỗi node 1-2 dòng ngắn. Chi tiết dài đặt trong text markdown bên cạnh.
4. **Auto-scale fallback:** `build_docx.py` tự động giảm width image nếu vẫn vượt height A4 (25cm). Không bao giờ tràn trang.

### Verification

**24 sơ đồ — 0 sơ đồ orientation LR** (đã verify bằng grep). Toàn bộ là TB hoặc sequenceDiagram (vốn vertical).

## Phần 1 — 10 sơ đồ (tất cả TB)

| # | Sơ đồ | Mục |
|:-:|---|---|
| 1 | hinh_3_1_kien_truc_3tang | §3 Kiến trúc 3 tầng |
| 2-3 | hinh_5_1a / hinh_5_1b | §5 Sequence E2E (chia 2 phần) |
| 4 | hinh_6_1_dispatch_logic | §6 Content Type Dispatch |
| 5-6 | hinh_7_1a / hinh_7_1b | §7 rag-core phases · scripts ops |
| 7 | hinh_7_2_module_bff | §7.2 BFF mixin architecture |
| 8 | hinh_9_1_thu_tu_khoi_dong | §9 Thứ tự khởi động — **đã đổi LR → TB** |
| 9 | hinh_10_1_vong_doi_du_lieu | §10 Vòng đời dữ liệu — **đã đổi LR → TB** |
| 10 | hinh_11_1_production | §11 Triển khai production |

## Phần 2 — 14 sơ đồ (tất cả TB)

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

## Build_docx.py — auto-scale image

Logic mới trong `add_image()`:

```python
MAX_WIDTH_CM = 16.0
MAX_HEIGHT_CM = 25.0

with Image.open(str(img_path)) as im:
    px_w, px_h = im.size
ratio = px_w / px_h
height_at_max_w = MAX_WIDTH_CM / ratio

if height_at_max_w > MAX_HEIGHT_CM:
    # Ảnh quá cao → giảm width để fit chiều cao A4
    target_w_cm = MAX_HEIGHT_CM * ratio
    run.add_picture(str(img_path), width=Cm(target_w_cm))
else:
    run.add_picture(str(img_path), width=Cm(MAX_WIDTH_CM))
```

Đảm bảo: dù sơ đồ TB có hơi cao (compact label vẫn không đủ), image sẽ được scale xuống để **luôn fit 1 trang A4 portrait** mà không mất chữ — vì TB layout có chiều ngang hẹp tự nhiên, scale xuống vẫn giữ chữ to dễ đọc.

## Cấu trúc thư mục

```
RAG_TaiLieu_ThietKe/
├── README.md
├── phan_1_tong_quan.docx              ← 48 trang, 10 sơ đồ
├── phan_1_tong_quan.pdf
├── phan_2_rag_core.docx                ← 60 trang, 14 sơ đồ
├── phan_2_rag_core.pdf
├── source/
│   ├── phan_1_tong_quan.md
│   ├── phan_2_rag_core.md
│   └── build_docx.py
├── mermaid/                            ← 24 file .mmd (22 TB + 2 sequence)
└── png/                                ← 24 PNG render
```

## Cách rebuild

```bash
cd RAG_TaiLieu_ThietKe
cp source/phan_2_rag_core.md ./phan_2_rag_core.md
python3 source/build_docx.py phan_2_rag_core.md phan_2_rag_core.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 2" \
    --version "Bản chính thức"
```

## Đặc tả tài liệu

- **Style:** Times New Roman 13pt body · heading xanh thẫm `#1F3A6E` · H1/H2 đậm · H3/H4 thường.
- **Trang:** A4 portrait (21 × 29.7 cm), lề top/bottom 2cm, left 2.5cm, right 2cm. Content area 16.5cm × 25.7cm.
- **Sơ đồ:** Mermaid TB orientation · `nodeSpacing: 30, rankSpacing: 30, padding: 8` · auto-scale image fit page height.
- **Ngôn ngữ:** Tiếng Việt cho toàn bộ nội dung.
