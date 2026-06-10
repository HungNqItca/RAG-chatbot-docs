# Docs-Design-Requirement.md

> **Mục đích:** Đặc tả các yêu cầu thiết kế tài liệu kỹ thuật đã được Quốc Hùng và Claude thống nhất qua nhiều vòng review.
> **Sử dụng:** Đính kèm vào prompt khi bắt đầu luồng chat mới về dự án tài liệu RAG-Chatbot Agribank.

---

## 1. PHẠM VI

Bộ tài liệu **RAG-Chatbot Pháp lý Agribank** gồm 4 phần, mỗi phần là một file `.md` → render thành `.docx` + `.pdf`:

| Phần | Nội dung |
|------|----------|
| 1 | Tổng quan hệ thống |
| 2 | RAG Core (Phase 1 Indexing / Phase 2 Retrieval / Phase 3 Generation) |
| 3 | BFF Service (auth, session ownership, SSE proxy) |
| 4 | agribank-chat (React UI) + Phụ lục |

**Ngôn ngữ:** Tiếng Việt xuyên suốt; thuật ngữ kỹ thuật giữ tiếng Anh khi cần.

**Đối tượng đọc:** Kỹ sư mới gia nhập đội phát triển/vận hành — viết theo lối **"Why → What → How"**.

---

## 2. STYLE TÀI LIỆU (build_docx.py)

### 2.1 Font và kích thước

| Phần tử | Font | Size | Đậm? |
|---|---|---|:-:|
| Body text | Times New Roman | 13pt | Thường |
| H1 (mục lớn) | Times New Roman | 16pt | ✓ đậm |
| H2 (mục con) | Times New Roman | 14pt | ✓ đậm |
| H3 | Times New Roman | 14pt | Thường |
| H4 | Times New Roman | 13pt | Thường |
| Code inline + code block | Consolas | 9.5pt | — |

**Quan trọng:** Tất cả heading dùng Times New Roman (không phải Arial). Tiếng Việt có dấu đầy đủ trong Times New Roman.

### 2.2 Màu sắc

```python
HEADING_COLOR = RGBColor(0x1F, 0x3A, 0x6E)   # Xanh thẫm cho TẤT CẢ heading
COLOR_TABLE_HEADER_BG = "DCE6F1"              # Xanh nhạt nền header bảng
```

Cover page cũng dùng xanh thẫm `#1F3A6E`.

### 2.3 Trang A4 portrait

| Thông số | Giá trị |
|---|---|
| Kích thước | 21 × 29.7 cm |
| Lề top/bottom | 2 cm |
| Lề left | 2.5 cm |
| Lề right | 2 cm |
| **Content area** | **16.5 × 25.7 cm** |

### 2.4 Format markdown

- H1 = `#`, H2 = `##`, H3 = `###`, H4 = `####`
- Code block: ` ``` ` với language hint (`python`, `javascript`, `bash`...)
- Bảng pipe-table với alignment markers `|---|`
- Blockquote `> **Lưu ý:**` cho callout vàng
- List có thứ tự render manual numbering với hanging indent (build_docx.py đã handle)
- Inline code: backtick \`code\`

---

## 3. SƠ ĐỒ MERMAID — RÀNG BUỘC NGHIÊM NGẶT

> **Đây là phần quan trọng nhất — đã thống nhất qua 4-5 vòng iteration.**

### 3.1 Quy tắc cốt lõi (không được vi phạm)

1. **TB-only orientation** — Mọi flowchart đều `flowchart TB` (top-to-bottom).
   - **TUYỆT ĐỐI KHÔNG** dùng `LR` (left-to-right). Lý do: khi insert vào A4 portrait với width 16cm, sơ đồ LR có nhiều node ngang → chữ trong mỗi node bị scale xuống quá nhỏ, không đọc được.
   - Sequence diagram (`sequenceDiagram`) vốn vertical tự nhiên — chấp nhận được.

2. **Một sơ đồ — một concept**
   - Không ghép 2 luồng vào một sơ đồ (ví dụ: KHÔNG ghép Legal pipeline + Tabular pipeline thành một).
   - Sơ đồ phức tạp được **chia thành sub-diagrams** với suffix `a, b, c...` và có **cross-reference** dạng `→ Hình X.Yb`.

3. **Compact node labels**
   - Mỗi node 1-2 dòng ngắn. Tránh `<br/>` quá nhiều trong một node.
   - Chi tiết dài → đặt trong text markdown bên cạnh sơ đồ, không nhồi vào node.

### 3.2 Mermaid config (đã tune)

File `mermaid_config.json`:
```json
{
  "theme": "default",
  "themeVariables": {
    "fontFamily": "Arial, sans-serif",
    "fontSize": "14px"
  },
  "flowchart": {
    "useMaxWidth": false,
    "htmlLabels": true,
    "curve": "basis",
    "nodeSpacing": 30,
    "rankSpacing": 30,
    "padding": 8
  },
  "sequence": {
    "useMaxWidth": false,
    "diagramMarginX": 20,
    "diagramMarginY": 10,
    "actorMargin": 50,
    "messageFontSize": 13
  }
}
```

File `puppeteer.json`:
```json
{ "args": ["--no-sandbox", "--disable-setuid-sandbox"] }
```

### 3.3 Render command

```bash
mmdc -i mermaid/hinh_X_Y.mmd \
     -o png/hinh_X_Y.png \
     -c mermaid_config.json \
     -p puppeteer.json \
     -b white \
     -w 1600
```

**Render width = 1600px** (đã thử 2400px — không cải thiện vì mermaid render theo aspect ratio nội tại).

### 3.4 Tiêu chí fit A4

Sơ đồ được coi là **fit 1 trang A4** nếu khi insert ở width 16cm thì height ≤ 25cm:

```
height_at_16cm_width ≤ 25cm
↔ ratio W/H ≥ 0.64
↔ height_at_16cm = 16 / (px_w / px_h)
```

**Kiểm tra bằng Python:**
```python
from PIL import Image
im = Image.open('png/hinh_X_Y.png')
w, h = im.size
height_cm = 16 / (w / h)
print(f'{height_cm:.1f}cm', '✓ FIT' if height_cm <= 25 else '❌ TRÀN')
```

### 3.5 Chiến lược xử lý khi sơ đồ tràn trang

| Tình huống | Cách xử lý |
|---|---|
| Height ≤ 25cm | ✓ OK, không cần làm gì |
| Height 25-30cm (tràn nhẹ) | Compact labels: rút ngắn text trong node, gộp 2 node liên quan thành 1, bỏ note phụ |
| Height 30-50cm (tràn vừa) | Compact mạnh + giảm số nodes (gộp các step nhỏ) |
| Height > 50cm (tràn nhiều) | **CHIA thành 2 sub-diagrams** (a, b) theo concept logic, có cross-reference |

**Ví dụ đã làm:**
- Hình 2.2 Saga rollback (107cm) → 2.2a Forward + 2.2b Rollback
- Hình 3.2 Hybrid+Rerank (104cm) → 3.2a Hybrid+RRF + 3.2b Rerank+Post
- Hình 4.2 Query Pipeline (38cm) → 4.2a Preprocess + 4.2b Decision tree
- Hình 5.1 Sequence E2E (29cm) → 5.1a Giai đoạn 1-3 + 5.1b Giai đoạn 4-6
- Hình 7.1 Module rag-core (43cm) → 7.1a 3 phases + 7.1b Scripts ops

### 3.6 Auto-scale image trong build_docx.py (fallback cuối)

Khi sơ đồ TB sau compact vẫn cao quá 25cm, `build_docx.py` sẽ **tự động giảm width image** để fit page height:

```python
MAX_WIDTH_CM = 16.0
MAX_HEIGHT_CM = 25.0

from PIL import Image
with Image.open(str(img_path)) as im:
    px_w, px_h = im.size
ratio = px_w / px_h
height_at_max_w = MAX_WIDTH_CM / ratio

if height_at_max_w > MAX_HEIGHT_CM:
    # Ảnh quá cao → giảm width để fit
    target_w_cm = MAX_HEIGHT_CM * ratio
    run.add_picture(str(img_path), width=Cm(target_w_cm))
else:
    run.add_picture(str(img_path), width=Cm(MAX_WIDTH_CM))
```

Logic này đảm bảo **mọi sơ đồ TB đều fit 1 trang A4** kể cả khi tỷ lệ không lý tưởng. Vì TB layout có chiều ngang hẹp, scale xuống vẫn giữ chữ đọc được.

### 3.7 Naming convention sơ đồ

```
hinh_<section>_<num>[suffix]_<short_name>.mmd
```

Ví dụ:
- `hinh_3_1_kien_truc_3tang.mmd` (Hình 3.1 ở mục 3)
- `hinh_2_2a_saga_forward.mmd` (Hình 2.2 chia thành a)
- `hinh_2_2b_saga_rollback.mmd` (Hình 2.2 chia thành b)
- `hinh_5_1_sse_proxy.mmd` (Hình 5.1 ở mục 5)

### 3.8 Cách reference sơ đồ trong markdown

```markdown
![Hình X.Y — Tiêu đề ngắn](png/hinh_X_Y_short_name.png)

*Hình X.Y — Caption italic đầy đủ giải thích nội dung sơ đồ và highlight điểm quan trọng.*
```

**Caption italic** ngay dưới sơ đồ là bắt buộc — giải thích sơ đồ trong bối cảnh nội dung.

---

## 4. CẤU TRÚC NỘI DUNG MỖI PHẦN

### 4.1 Mẫu chuẩn

Mỗi phần tài liệu nên có cấu trúc:

```
# PHẦN X — TIÊU ĐỀ

## 1. Giới thiệu Phần X
   - Bối cảnh
   - Các nguyên tắc thiết kế cốt lõi (2-3 nguyên tắc)
   - Mục lục các section

## 2. Kiến trúc tổng thể
   - Sơ đồ Hình 1.1 / 2.1
   - Cấu trúc thư mục
   - Vị trí trong hệ thống

## 3-N. Các mục chi tiết
   - Sơ đồ + code thực tế + giải thích
   - Mỗi mục: Why → What → How

## (Cuối) ADR — Quyết định thiết kế quan trọng
   - 5-8 ADR
   - Mỗi ADR: Quyết định / Lý do / Đánh đổi / Khi nào nên đổi
```

### 4.2 Phong cách viết

- **Why → What → How** — Trước khi nói code làm gì, giải thích vì sao cần.
- **Code snippets từ source thực tế** — KHÔNG bịa code. Copy nguyên bản từ file source.
- **Bảng so sánh tường minh** khi có lựa chọn (vd: HS256 vs RS256, Redux vs Zustand).
- **Cross-reference giữa các phần** (vd: "đã được mô tả ở Phần 1 §3.2", "→ Hình 4.2b").
- **Highlight bug đã fix** với 3 thông tin: triệu chứng / nguyên nhân / cách fix.
- **Section ADR cuối tài liệu** — tách riêng để tra cứu nhanh.

### 4.3 Số lượng tham khảo (cho 4 phần đã làm)

| Phần | Trang | Sơ đồ |
|---|---:|---:|
| 1 (Tổng quan) | 48 | 10 |
| 2 (RAG Core) | 60 | 14 |
| 3 (BFF) | 36 | 8 |
| 4 (UI + Phụ lục) | 38 | 10 |

Phần 4 có **4 phụ lục** đặc biệt:
- Phụ lục A — API Contracts
- Phụ lục B — Glossary (40+ thuật ngữ, reference Phần 1-4)
- Phụ lục C — Bảng tra cứu File ↔ Chức năng (cho 3 service)
- Phụ lục D — ADR cho UI (7 quyết định)

---

## 5. QUY TRÌNH BUILD

### 5.1 Render mermaid → PNG

```bash
cd <workspace>
for f in mermaid/*.mmd; do
  name=$(basename "$f" .mmd)
  mmdc -i "$f" -o "png/${name}.png" \
       -c mermaid_config.json -p puppeteer.json \
       -b white -w 1600
done
```

### 5.2 Đo dimensions sau render

```python
from PIL import Image
import glob
print(f"{'File':<55} {'h@16cm':>8} status")
for f in sorted(glob.glob("png/*.png")):
    im = Image.open(f); w, h = im.size
    height_cm = 16 / (w / h)
    status = "✓" if height_cm <= 25 else "❌ TRÀN"
    print(f"{f:<55} {height_cm:>6.1f}cm  {status}")
```

### 5.3 Build markdown → docx

```bash
python3 source/build_docx.py phan_X_xxx.md phan_X_xxx.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN X" \
    --version "Bản v1 — Phần X/4"
```

### 5.4 Convert docx → PDF

```bash
soffice --headless --convert-to pdf phan_X_xxx.docx
```

### 5.5 Inspect từng trang để verify

```bash
rm -f pX-page-*.jpg
pdftoppm -jpeg -r 80 phan_X_xxx.pdf pX-page
# Sau đó view từng JPEG: pX-page-NN.jpg
```

---

## 6. CHECKLIST TỰ KIỂM TRA TRƯỚC KHI GIAO BÀI

- [ ] **Tất cả flowchart** Mermaid là `flowchart TB` (không LR)
- [ ] **Mọi sơ đồ fit 1 trang A4** (height ≤ 25cm ở width 16cm, hoặc đã auto-scale)
- [ ] **Một sơ đồ — một concept** (không ghép nhiều luồng)
- [ ] **Caption italic** dưới mỗi sơ đồ
- [ ] **Code snippets từ source thực tế** (không bịa)
- [ ] **Cross-reference** các phần khác và sơ đồ liên quan
- [ ] **Section ADR cuối tài liệu** với 5-8 quyết định
- [ ] **Body 13pt Times New Roman, heading xanh thẫm `#1F3A6E`**
- [ ] **H1/H2 đậm, H3/H4 thường**
- [ ] **Tiếng Việt** xuyên suốt
- [ ] **Bug đã fix** được tài liệu hóa với triệu chứng/nguyên nhân/cách fix
- [ ] **Update README.md** với metadata phần mới

---

## 7. CÁC LƯU Ý ĐẶC BIỆT (Lessons Learned)

### 7.1 Không tự ý workaround

Khi gặp vấn đề (sơ đồ tràn trang, render lỗi, code không hiểu), **báo ngay** với người dùng — đừng tự quyết định cách workaround có thể vi phạm yêu cầu.

### 7.2 Đọc source trước rồi hỏi

Khi không chắc về source code (file không tồn tại như mong đợi, stack khác dự đoán), **đọc file thực tế** rồi confirm với người dùng. Không suy đoán.

Ví dụ thực tế trong dự án này:
- Em từng đoán sai stack frontend (nghĩ là Tailwind, thực tế là CSS Modules)
- Em từng đoán sai SSE client (nghĩ EventSource, thực tế là fetch + ReadableStream)

→ Phải đọc `package.json` và source code trước khi viết.

### 7.3 Mermaid quirks đã học

- Outer `flowchart LR` + inner `direction TB` → mermaid render rất hẹp/cao
- Outer `flowchart TB` + inner `direction LR` → mermaid render hợp lý hơn
- Node label dài 1 dòng cũng làm box cao (vì mermaid wrap text trong box hẹp)
- Tăng `-w 2400` không giúp giảm height (mermaid render theo ratio nội tại)
- 7+ node trong chain TB linear → height vượt 30cm dễ dàng → cần chia

### 7.4 LibreOffice convert nhanh hơn pandoc

`soffice --headless --convert-to pdf` rất nhanh (~2-3s) và giữ đúng style của docx.

### 7.5 Inspect bằng pdftoppm

```bash
pdftoppm -jpeg -r 80 file.pdf prefix
```

Tạo `prefix-01.jpg, prefix-02.jpg...` để xem từng trang bằng image viewer/Claude view tool.

### 7.6 Bug pattern đã encounter

| Triệu chứng | Nguyên nhân | Fix |
|---|---|---|
| Sơ đồ tràn trang | Quá nhiều node hoặc label dài | Compact / chia sub-diagrams / auto-scale |
| Chữ trong sơ đồ quá nhỏ | Dùng LR orientation | Đổi sang TB |
| Width sơ đồ chỉ 250-300px | Mermaid render TB với labels ngắn | Accept (auto-scale sẽ scale xuống height fit) |
| Caption nằm tách khỏi hình (orphan) | Word page break | Chấp nhận hoặc reorder content |

---

## 8. CÔNG CỤ CẦN CÓ

| Tool | Cài đặt | Vai trò |
|---|---|---|
| `mmdc` | `npm install -g @mermaid-js/mermaid-cli` | Render mermaid → PNG |
| `python-docx` | `pip install python-docx` | Build markdown → docx |
| `Pillow` | `pip install Pillow` | Đo dimensions PNG |
| `LibreOffice` | `apt install libreoffice` | Convert docx → PDF |
| `pdftoppm` | `apt install poppler-utils` | Convert PDF → JPEG để inspect |

---

## 9. CẤU TRÚC THƯ MỤC DỰ ÁN

```
RAG_TaiLieu_ThietKe/
├── README.md
├── phan_1_tong_quan.docx + .pdf
├── phan_2_rag_core.docx + .pdf
├── phan_3_bff_service.docx + .pdf
├── phan_4_agribank_chat.docx + .pdf
├── source/
│   ├── phan_1_tong_quan.md
│   ├── phan_2_rag_core.md
│   ├── phan_3_bff_service.md
│   ├── phan_4_agribank_chat.md
│   └── build_docx.py
├── mermaid/                            (42 file .mmd)
├── png/                                (42 PNG render)
├── mermaid_config.json
└── puppeteer.json
```

---

## 10. NGUYÊN TẮC GIAO TIẾP

- **Tiếng Việt** xuyên suốt giữa người dùng và Claude.
- **Chi tiết, cẩn thận, có trích dẫn cụ thể** từ source code khi viết documentation.
- **Báo cáo trước khi làm việc lớn** (ví dụ: "Em đề xuất outline X gồm N mục, anh xác nhận trước khi em viết chi tiết?").
- **Không tự ý quyết định workaround** khi gặp ràng buộc — hỏi người dùng.

---

> **End of Docs-Design-Requirement.md**
