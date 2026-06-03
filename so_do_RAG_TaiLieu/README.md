# Sơ đồ Mermaid + PNG — RAG-Chatbot Pháp lý Agribank

Bộ 42 sơ đồ cho 4 phần tài liệu thiết kế hệ thống.

## Cấu trúc

```
so_do_RAG_TaiLieu/
├── README.md                       (file này)
├── mermaid_config.json             (config compact: nodeSpacing/rankSpacing/padding)
├── puppeteer.json                  (config Puppeteer cho mmdc)
├── mermaid/
│   ├── phan_1/  (10 file .mmd — Tổng quan hệ thống)
│   ├── phan_2/  (14 file .mmd — RAG Core)
│   ├── phan_3/  (8 file .mmd — BFF Service)
│   └── phan_4/  (10 file .mmd — agribank-chat UI)
└── png/
    ├── phan_1/  (10 PNG)
    ├── phan_2/  (14 PNG)
    ├── phan_3/  (8 PNG)
    └── phan_4/  (10 PNG)
```

## Cách edit và render lại

### 1. Edit file `.mmd` bằng text editor

Giữ nguyên `flowchart TB` (top-to-bottom). Không đổi sang `LR` vì chữ sẽ quá nhỏ trên A4.

### 2. Render PNG bằng mmdc

```bash
# Cài mmdc nếu chưa có
npm install -g @mermaid-js/mermaid-cli

# Render 1 file
mmdc -i mermaid/phan_3/hinh_5_1_sse_proxy.mmd \
     -o png/phan_3/hinh_5_1_sse_proxy.png \
     -c mermaid_config.json \
     -p puppeteer.json \
     -b white \
     -w 1600
```

### 3. Render hàng loạt (bash)

```bash
for f in mermaid/phan_3/*.mmd; do
    name=$(basename "$f" .mmd)
    mmdc -i "$f" -o "png/phan_3/${name}.png" \
         -c mermaid_config.json -p puppeteer.json \
         -b white -w 1600
done
```

### 4. Kiểm tra sơ đồ có fit A4 không

Sơ đồ fit A4 portrait nếu `height_at_16cm_width <= 25cm`, tương đương ratio W/H >= 0.64.

```python
from PIL import Image
im = Image.open('png/phan_3/hinh_5_1_sse_proxy.png')
w, h = im.size
ratio = w / h
height_cm = 16 / ratio
print(f'h@16cm = {height_cm:.1f}cm', '✓' if height_cm <= 25 else '❌ TRÀN')
```

Nếu ❌ TRÀN: compact node labels hoặc chia thành sub-diagrams (a, b).
Build_docx.py có sẵn auto-scale fallback — sẽ giảm width image nếu vẫn cao quá 25cm.

## Danh sách 42 sơ đồ

### Phần 1 — Tổng quan hệ thống (10 sơ đồ)

| # | File | Vai trò |
|:-:|---|---|
| 1 | hinh_3_1_kien_truc_3tang | Kiến trúc 3 tầng (FE/BFF/RC) |
| 2-3 | hinh_5_1a / hinh_5_1b | Sequence E2E (chia 2 phần) |
| 4 | hinh_6_1_dispatch_logic | Content Type Dispatch |
| 5-6 | hinh_7_1a / hinh_7_1b | rag-core phases · scripts ops |
| 7 | hinh_7_2_module_bff | BFF mixin architecture |
| 8 | hinh_9_1_thu_tu_khoi_dong | Thứ tự khởi động 3 service |
| 9 | hinh_10_1_vong_doi_du_lieu | Vòng đời dữ liệu |
| 10 | hinh_11_1_production | Triển khai production |

### Phần 2 — RAG Core (14 sơ đồ)

| # | File | Vai trò |
|:-:|---|---|
| 1-2 | hinh_2_1a / hinh_2_1b | Pipeline Legal · Pipeline Tabular |
| 3-4 | hinh_2_2a / hinh_2_2b | Saga Forward · Saga Rollback |
| 5-6 | hinh_2_3a / hinh_2_3b | EAV Structure · search_text+BM25 |
| 7 | hinh_3_1_retrieval_strategies | 4 chiến lược retrieval |
| 8-9 | hinh_3_2a / hinh_3_2b | Hybrid+RRF · Rerank+Post |
| 10 | hinh_3_3_tabular_retrieval | Tabular retrieval pipeline |
| 11 | hinh_4_1_orchestrator_dispatch | GenerationOrchestrator |
| 12-13 | hinh_4_2a / hinh_4_2b | Tiền xử lý · Decision tree |
| 14 | hinh_4_3_tabular_handler | TabularGenerationHandler |

### Phần 3 — BFF Service (8 sơ đồ)

| # | File | Vai trò |
|:-:|---|---|
| 1 | hinh_1_1_kien_truc_bff | Kiến trúc 3 lớp BFF |
| 2 | hinh_2_1_userdb_mixin | UserDB shell + 7 mixin |
| 3 | hinh_3_1_jwt_lifecycle | JWT lifecycle |
| 4 | hinh_3_2_rbac_rank_check | RBAC decision tree |
| 5 | hinh_4_1_login_bruteforce | Login + lockout |
| 6 | hinh_5_1_sse_proxy | SSE streaming flow |
| 7 | hinh_6_1_reconcile | Session reconcile |
| 8 | hinh_7_1_schema_users_db | Schema 5 bảng SQLite |

### Phần 4 — agribank-chat UI (10 sơ đồ)

| # | File | Vai trò |
|:-:|---|---|
| 1 | hinh_1_1_kien_truc_fe | Kiến trúc 4 lớp FE |
| 2 | hinh_2_1_cau_truc_thu_muc | Cây thư mục src/ |
| 3 | hinh_3_1_auth_flow | JWT refresh queue |
| 4 | hinh_4_1_zustand_stores | 3 Zustand stores |
| 5-6 | hinh_5_1a / hinh_5_1b | sendMessage setup · streamChat lifecycle |
| 7 | hinh_5_2_sse_events | 5 SSE event types |
| 8 | hinh_6_1_component_tree | ChatPage component tree |
| 9 | hinh_7_1_routing_guards | ProtectedRoute · AdminRoute |
| 10 | hinh_8_1_error_handling | 4 loại lỗi → 4 cách xử lý |

## Nguyên tắc design (đã áp dụng nhất quán)

1. **TB-only orientation** — Mọi flowchart đều `flowchart TB`. Không LR.
2. **Một sơ đồ — một concept** — Sơ đồ phức tạp được chia thành sub-diagrams (a, b).
3. **Compact node labels** — Mỗi node 1-2 dòng ngắn.
4. **Auto-scale fallback** trong `build_docx.py` — đảm bảo mọi sơ đồ fit 1 trang A4.
