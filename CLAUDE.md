# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

Technical documentation for an Agribank RAG Chatbot system — 4-part series in Vietnamese, 182 pages, 42 diagrams. Each part has Markdown source → rendered PNG diagrams → built .docx output.

## Build Commands

### Render a single Mermaid diagram to PNG
```bash
mmdc -i <file.mmd> -o <file.png> \
  -c so_do_RAG_TaiLieu/mermaid_config.json \
  -p so_do_RAG_TaiLieu/puppeteer.json \
  -w 1400 -H 1000
```

### Render all diagrams for a part (example: phan_2)
```bash
for f in so_do_RAG_TaiLieu/mermaid/phan_2/*.mmd; do
  mmdc -i "$f" -o "so_do_RAG_TaiLieu/png/phan_2/$(basename $f .mmd).png" \
    -c so_do_RAG_TaiLieu/mermaid_config.json \
    -p so_do_RAG_TaiLieu/puppeteer.json -w 1400
done
```

### Build .docx from Markdown source
```bash
cd 1-Tong-quan/source   # or 2-Rag-core/source
python3 build_docx.py phan_1_tong_quan.md ../../phan_1_tong_quan.docx \
  --title "TIÊU ĐỀ" --subtitle "PHỤ ĐỀ" --version "v1.0"
```
Use `--no-cover` to skip the cover page.

### Convert .docx to PDF (requires LibreOffice)
```bash
libreoffice --headless --convert-to pdf phan_1_tong_quan.docx
```

### Install dependencies
```bash
pip install python-docx Pillow
npm install -g @mermaid-js/mermaid-cli
```

## Repository Structure

```
Tài liệu/
├── 1-Tong-quan/       # Part 1: System Overview (48 pages, 10 diagrams)
├── 2-Rag-core/        # Part 2: RAG Core — Indexing/Retrieval/Generation (60 pages, 14 diagrams)
├── 3-Bff-service/     # Part 3: BFF Service (36 pages, 8 diagrams)
├── 4-Agribank-chat/   # Part 4: React Frontend (38 pages, 10 diagrams)
└── so_do_RAG_TaiLieu/ # Central diagram registry (all .mmd sources + rendered .png)
```

Each part follows: `source/<part>.md` (master source) → `mermaid/*.md` (diagram snippets) → `png/` (rendered) → `<part>.docx` (final output).

The `so_do_RAG_TaiLieu/mermaid/phan_N/` folders mirror the canonical `.mmd` files used for rendering; `1-Tong-quan/mermaid/` etc. contain `.md` versions of the same diagrams embedded in documentation context.

## Diagram Conventions

- **Orientation**: All flowcharts use `flowchart TB` only — never `LR` (text overflows A4 width)
- **One concept per diagram**: Complex flows are split into sub-diagrams `a`, `b`, `c` with cross-references in surrounding text
- **Node labels**: Max 2 lines; details belong in body text, not diagram nodes
- **Naming**: `hinh_<section>_<seq>[a|b]_<slug>.mmd` — e.g. `hinh_3_2a_hybrid_rrf.mmd`

## Document Styling (enforced by build_docx.py)

| Element | Style |
|---------|-------|
| Body font | Times New Roman 13pt, line spacing 1.4 |
| Headings | Navy `#1F3A6E`; H1/H2 bold 16/14pt; H3/H4 normal 14/13pt |
| Code blocks | Consolas 9.5pt, background `#F5F5F5` |
| Table header | Background `#DCE6F1`, borders `#BFBFBF` |
| Blockquotes | Background `#FFF8E1`, left border orange |
| Page | A4 portrait, margins: top/bottom 2cm, left 2.5cm, right 2cm |
| Images | Auto-scaled to fit 16cm × 25cm max (build_docx.py handles this) |

## System Architecture (documented in this repo)

The chatbot system has 3 tiers:
1. **RAG Core** (`2-Rag-core/`) — dual pipeline (Legal documents + Tabular data), Saga-pattern indexing, hybrid BM25+vector retrieval with RRF fusion and reranking, generation orchestrator with slot detection
2. **BFF Service** (`3-Bff-service/`) — FastAPI backend-for-frontend, 7-mixin UserDB pattern, JWT + RBAC 4-tier auth, SSE proxy with session ownership, SQLite 5-table schema
3. **agribank-chat** (`4-Agribank-chat/`) — React 18 + Vite 5 + Zustand, CSS Modules (no Tailwind), SSE accumulator streaming, JWT refresh queue, react-virtuoso virtual scrolling

Each part's `README.md` is a concise technical summary of that part's content and diagram index.
