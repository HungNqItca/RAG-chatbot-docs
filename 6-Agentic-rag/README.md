# Phần 6 — Nâng cấp Agentic RAG

Tài liệu hóa bản nâng cấp **Agentic RAG** của phân hệ `rag-core` (mở rộng Phase 3 — Generation). Cho phép hệ thống trả lời câu hỏi **đa bước** (so sánh nhiều văn bản, kết hợp pháp lý + biểu phí) mà pipeline RAG-SQLite cũ không xử lý được. Toàn bộ được bọc sau công tắc `agentic_rag` (mặc định `false` = hệ cũ nguyên trạng).

## Trạng thái

| Hạng mục | Giá trị |
|---|---|
| Số trang | ~30 |
| Số sơ đồ | 7 (8 file, do 6.3 chia a/b) |
| Nguồn code | `rag-core/phase3_generation/core/agent/` (6 giai đoạn A–F, 125 test pass) |
| Trạng thái | ✅ Hoàn thành |

## Cấu trúc nội dung

| § | Tiêu đề | Sơ đồ |
|:-:|---|:-:|
| 1 | Giới thiệu: vì sao Agentic + 3 ràng buộc cứng | — |
| 2 | Kiến trúc tổng thể + công tắc `agentic_rag` | Hình 6.1 |
| 3 | Lớp Tool — 4 tool bọc hàm Phase 2/MongoDB | Hình 6.2 |
| 4 | Vòng lặp ReAct + parser phòng thủ | Hình 6.3a, 6.3b |
| 5 | CitationGuard — chống hallucinate điều khoản | Hình 6.4 |
| 6 | Router + AgentOrchestrator | Hình 6.5 |
| 7 | Streaming verify-trước-stream + frontend | Hình 6.6 |
| 8 | Eval 4 nhóm + bật dần + `/health` mode | Hình 6.7 |
| 9 | ADR (8 quyết định) | — |

## Ba ràng buộc cứng (giữ trọn)

1. **Không hallucinate điều khoản** — CitationGuard verify exact-match + eval N4.
2. **Phạm vi đóng kín** — 4 tool, không tool tra web.
3. **Độc lập backend** — ReAct prompt-based (không native function-calling), sẵn sàng cho LLM local.

> Nguyên tắc bao trùm: *LLM được tự do về điều hướng, nhưng bị kỷ luật về trích dẫn.*

## Sơ đồ — 7 hình (tất cả TB / sequence)

| # | Sơ đồ | Mục |
|:-:|---|---|
| 1 | hinh_6_1_kien_truc_agentic | §2 Điểm cắm Agentic + công tắc |
| 2 | hinh_6_2_lop_tool | §3 ToolRegistry + 4 tool |
| 3-4 | hinh_6_3a / hinh_6_3b | §4 ReAct loop · scratchpad + budget |
| 5 | hinh_6_4_citation_guard | §5 Luồng verify CitationGuard |
| 6 | hinh_6_5_router_orchestrator | §6 Router + Hybrid repair/annotate |
| 7 | hinh_6_6_stream_verify | §7 Verify-trước-stream (sequence) |
| 8 | hinh_6_7_eval_rollout | §8 Eval 4 nhóm + bật dần |

Hai sơ đồ chuỗi tuyến tính (6.3a, 6.4) cao hơn 25cm và được `build_docx.py` auto-scale fit A4 (đúng cơ chế §3.6 của Docs-Design-Requirement) — đã verify chữ vẫn đọc rõ.

## Rebuild

```bash
# 1. Render sơ đồ (cần Chrome cho puppeteer)
cd <workspace>
for f in 6-Agentic-rag/mermaid/*.mmd; do
  name=$(basename "$f" .mmd)
  mmdc -i "$f" -o "6-Agentic-rag/png/${name}.png" \
       -c so_do_RAG_TaiLieu/mermaid_config.json \
       -p so_do_RAG_TaiLieu/puppeteer.json -b white -w 1600
done

# 2. Build .docx
cd 6-Agentic-rag/source
python3 build_docx.py phan_6_agentic_rag.md phan_6_agentic_rag.docx \
    --title "TÀI LIỆU THIẾT KẾ HỆ THỐNG" \
    --subtitle "RAG-CHATBOT PHÁP LÝ NỘI BỘ AGRIBANK — PHẦN 6" \
    --version "Bản v1 — Phần 6 (Nâng cấp Agentic RAG)"

# 3. Xuất PDF
soffice --headless --convert-to pdf phan_6_agentic_rag.docx
```

## Tham khảo source

- `rag-core/phase3_generation/core/agent/GUIDE_AGENTIC.md` — hướng dẫn dev §1–13.
- `rag-core/phase3_generation/DESIGN.md` — §A.0–A.9 (kiến trúc Agentic đầy đủ).
- `UPGRADE/AGENTIC_RAG/HANDOFF-AGENTIC_RAG.md` — tổng kết 6 giai đoạn A–F.
