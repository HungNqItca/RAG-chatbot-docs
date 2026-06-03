# Tài liệu Thiết kế Hệ thống RAG-Chatbot Pháp lý Agribank

**Bộ tài liệu** chuẩn bị cho công tác đào tạo và chuyển giao công nghệ.

## Trạng thái

| Phần | Nội dung | Số trang | Số sơ đồ | Trạng thái |
|------|----------|---------:|---------:|:----------:|
| **1** | **Tổng quan hệ thống** | **44** | **8** | ✅ **Hoàn thành (đang review)** |
| 2 | RAG Core (Phase 1/2/3) | — | — | ⏳ Chờ feedback Phần 1 |
| 3 | BFF Service | — | — | ⏳ |
| 4 | agribank-chat (UI) + Phụ lục | — | — | ⏳ |

## Phần 1 — Cấu trúc nội dung

| § | Tiêu đề | Sơ đồ |
|:-:|---|:-:|
| 1 | Giới thiệu tài liệu | — |
| 2 | Bối cảnh & Bài toán nghiệp vụ | — |
| 3 | Kiến trúc tổng thể — 3 tầng | **Hình 3.1** |
| 4 | Công nghệ nền (4 bảng thư viện) | — |
| 5 | Luồng end-to-end một câu hỏi (6 giai đoạn, ~46 bước) | **Hình 5.1** |
| 6 | Hai pipeline song song — Content Type Dispatch (7 rule) | **Hình 6.1** |
| 7 | Cấu trúc mã nguồn | **Hình 7.1, 7.2** |
| 8 | Môi trường & Cấu hình (3 .env + 2 YAML) | — |
| 9 | Vận hành — Khởi động 3 service | **Hình 9.1** |
| 10 | Vòng đời dữ liệu | **Hình 10.1** |
| 11 | Triển khai production | **Hình 11.1** |
| 12 | Thuật ngữ & từ viết tắt (3 sub-bảng) | — |

## Cấu trúc thư mục

```
RAG_TaiLieu_ThietKe/
├── README.md
├── phan_1_tong_quan.docx          ← TÀI LIỆU CHÍNH (Phần 1, 44 trang A4)
├── phan_1_tong_quan.pdf           ← Bản PDF preview
├── source/
│   ├── phan_1_tong_quan.md        ← Mã nguồn Markdown
│   └── build_docx.py              ← Script Python build .docx
├── mermaid/                       ← 8 sơ đồ mermaid nguồn
│   ├── hinh_3_1_kien_truc_3tang.mmd
│   ├── hinh_5_1_sequence_e2e.mmd
│   ├── hinh_6_1_dispatch_logic.mmd
│   ├── hinh_7_1_module_rag_core.mmd
│   ├── hinh_7_2_module_bff.mmd
│   ├── hinh_9_1_thu_tu_khoi_dong.mmd
│   ├── hinh_10_1_vong_doi_du_lieu.mmd
│   └── hinh_11_1_production.mmd
└── png/                           ← 8 PNG đã render (đính kèm trong docx)
```

## Cập nhật chính so với bản v1 (32 trang) — phản ánh codebase đã refactor

| Mục | Bản cũ | Bản v2 |
|---|---|---|
| `unified_dispatch.enabled` | mặc định **false** (Legacy) | mặc định **true** (Unified) |
| `tabular.min_score` | 0.30 | **0.005** (theo phân phối RRF score thực tế) |
| BFF UserDB | monolithic ~800 dòng | **7-mixin architecture** |
| `auth/permissions.py` | (không có) | **mới** — tách `ROLE_RANK` + assert helpers |
| `proxy/session_authorization.py` | (không có) | **mới** — `assert_session_access()` shared helper |
| `utils/errors.py` | (không có) | **mới** — factory `unauthorized()`/`forbidden()`/`not_found_user()` |
| Permanent chat history | RAG Core TTL 72h là duy nhất | **2 lớp**: BFF DB vĩnh viễn + RAG Core 72h |
| RBAC | 2 tầng (user/admin) | **4 tầng** (user/manager/admin/superadmin) rank-aware |
| Frontend hooks | 3 hooks | **+ 3 hooks tổng quát mới** (useApiMutation, useForm, usePaginatedResource) |
| Frontend utils | rải rác | **tập trung** apiErrors, sessionNormalizers, time |
| MongoDB | chỉ Atlas | **Atlas + local mode** |
| Sơ đồ BFF module | (không có) | **Hình 7.2 mới** — mixin architecture |

## Cách rebuild docx khi sửa nội dung

### Bước 1 — Sửa Markdown
Chỉnh `source/phan_1_tong_quan.md`. Hỗ trợ:
- Heading: `#`, `##`, `###`, `####`
- Bold: `**text**`, italic: `*text*`, inline code: `` `code` ``
- Bullet list: `- item`, numbered list: `1. item`, nested 2 spaces
- Bảng pipe: `| col1 | col2 |` với separator `|---|---|`, `|:-:|:--|--:|` (alignment)
- Ảnh: `![alt](png/file.png)`
- Code block: ` ```lang ... ``` `
- Blockquote: `> nội dung`
- Đường ngang: `---`

### Bước 2 — Sửa sơ đồ Mermaid (nếu cần)
```bash
cd 1-tong-quan
mmdc -i mermaid/01_kien_truc_3_tang.mmd -o png/01_kien_truc_3_tang.png -b white -w 1600

```



### Bước 3 — Build lại .docx
```bash
cd "1-Tong-quan\source"

python build_docx.py phan_1_tong_quan.md phan_1_tong_quan.docx --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" --subtitle "RAG-CHATBOT NỘI BỘ — PHẦN 1" --version "Bản chính thức"
```

### Yêu cầu phụ thuộc
- Python 3.10+: `pip install python-docx`
- Mermaid CLI (chỉ khi sửa sơ đồ): `npm install -g @mermaid-js/mermaid-cli`
- LibreOffice (chỉ khi convert PDF): `apt install libreoffice`

## Đặc tả tài liệu

- **Ngôn ngữ:** tiếng Việt cho toàn bộ nội dung; thuật ngữ kỹ thuật giữ tiếng Anh khi cần.
- **Đối tượng đọc:** kỹ sư mới gia nhập đội phát triển/vận hành — viết theo lối "Why → What → How".
- **Sơ đồ:** Mermaid được render PNG độ phân giải 1600px để đảm bảo chất lượng in ấn.
- **Font:** Times New Roman (body), Arial (heading), Consolas (code).
- **Trang:** A4 (21 × 29.7 cm), lề top/bottom 2 cm, left 2.5 cm, right 2 cm.
