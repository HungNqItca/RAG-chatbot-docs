# CHANGELOG — Round 3: Bổ sung Phần 6 (Nâng cấp Agentic RAG)

> Đối chiếu bộ tài liệu thiết kế (đang ở trạng thái pre-Agentic sau Round 2) với codebase `RAG-SQLite-agentic-rag-chatbot` (đã có đầy đủ Agentic RAG, 6 giai đoạn A–F, 125 test pass, deploy 2026-06-09).
> **Phát hiện chính:** Toàn bộ 5 phần tài liệu hiện tại đều **chưa đề cập Agentic RAG** (đã grep xác nhận — các hit "agentic" chỉ là substring của "React"). Source code thì đã có và tự tài liệu hóa rất kỹ (`phase3_generation/DESIGN.md` §A.0–A.9, `core/agent/GUIDE_AGENTIC.md` §1–13, `HANDOFF-AGENTIC_RAG.md`).
> **Phương pháp:** đọc trực tiếp source code agent (`core/agent/*.py`), DESIGN/GUIDE/HANDOFF làm căn cứ; code snippet lấy nguyên văn, không bịa.

## A. Bổ sung PHẦN 6 — Nâng cấp Agentic RAG (mới, ~30 trang, 7 sơ đồ)

Chọn phương án **Phần 6 riêng** (thay vì nhồi vào Phần 2) vì: (1) Agentic là lớp nâng cấp có công tắc bật/tắt, tách bạch với pipeline cũ — tài liệu nên phản ánh đúng; (2) rủi ro hồi quy tài liệu thấp nhất, không phải đánh số lại sơ đồ Phần 2; (3) source code cũng coi đây là dự án nâng cấp độc lập (HANDOFF/PLAN/Stage A–F). Nhược điểm "mỗi service một phần" được xử lý bằng cross-reference (đầu Phần 6 ghi rõ là mở rộng Phase 3 — Phần 2 §4).

| § | Nội dung | Nguồn code |
|---|---|---|
| 1 | Vì sao Agentic + 3 ràng buộc cứng + nguyên tắc bao trùm | HANDOFF §1, GUIDE §1 |
| 2 | Kiến trúc tổng thể + công tắc `agentic_rag` (hai đường khởi tạo) | DESIGN §A.0–A.1, `generation_orchestrator.py` |
| 3 | Lớp Tool: 4 tool + ToolObservation + ToolRegistry phòng thủ | `tools.py`, `tool_registry.py`, DESIGN §A.2 |
| 4 | ReAct loop + parser + scratchpad + budget | `react_loop.py`, `react_parser.py`, `prompts.py`, DESIGN §A.5 |
| 5 | CitationGuard: verify bằng chứng, 3 ca đối kháng, khớp linh hoạt | `citation_guard.py`, DESIGN §A.6 |
| 6 | Router + AgentOrchestrator + Hybrid repair/annotate | `router.py`, `agent_orchestrator.py`, DESIGN §A.7 |
| 7 | Streaming verify-trước-stream + frontend SSE `step` | `agent_orchestrator.py`, `sseClient.js`/`useChat.js`/`MessageBubble.jsx`, DESIGN §A.8 |
| 8 | Eval 4 nhóm (N1–N4) + bật dần + `/health` mode | `performance_evaluation/agentic_eval/*`, DESIGN §A.9 |
| 9 | 8 ADR | tổng hợp |

**7 sơ đồ (8 file, đều `flowchart TB` hoặc sequence):**
- Hình 6.1 kiến trúc Agentic · 6.2 lớp Tool · 6.3a ReAct loop · 6.3b scratchpad+budget · 6.4 CitationGuard · 6.5 Router+Orchestrator · 6.6 stream verify (sequence) · 6.7 eval+rollout.
- Hai sơ đồ chuỗi tuyến tính (6.3a ~25cm, 6.4 ~28cm) được `build_docx.py` auto-scale fit A4 (đúng cơ chế §3.6 Docs-Design-Requirement) — đã verify chữ đọc rõ qua pdftoppm.

## B. Sửa các điểm cũ cho khớp (cross-reference Agentic)

- **P1 §1 (Cấu trúc bộ tài liệu):** Sửa "bốn phần" → "sáu phần"; bảng cấu trúc bổ sung đủ Phần 5 (vốn bị thiếu sau Round 2) + Phần 6. *Đây là sửa một gap tồn đọng từ Round 2.*
- **P2 §4 (Phase 3 — Generation):** Thêm callout đầu mục dẫn sang Phần 6 ("Phase 3 còn có bản nâng cấp Agentic RAG... mục 4 dưới đây vẫn đúng khi `agentic_rag=false`").
- **P4 §5.4 (5 SSE event types):** Thêm note về event `step` của nhánh agent (callback `onStep`, `currentStep`, spinner trong `MessageBubble`), nhấn mạnh token nhánh agent là nội dung đã CitationGuard verify.

## C. File đã build lại
- **Phần 6:** mới — `.md` + 8 PNG + `.docx` (442 KB) + `.pdf` (30 trang). README riêng.
- **Phần 1, 2, 4:** rebuild `.docx` + `.pdf` (do thêm cross-reference Agentic).
- **Phần 3, 5:** giữ nguyên (không đụng nội dung). *Lưu ý: Phần 3 chưa từng có file `.docx`/`.pdf` build sẵn trong bộ tài liệu — giữ nguyên trạng thái này như các Round trước.*
- **README.md (root):** cập nhật bảng trạng thái 6 phần + mục Phần 6 + cây thư mục.
- **so_do_RAG_TaiLieu/:** thêm `mermaid/phan_6/` (8 `.mmd`) + `png/phan_6/` (8 PNG).

## D. Ghi chú kỹ thuật
- Output `.docx`/`.pdf` đặt trong `*/source/` của từng phần — khớp bố cục bộ tài liệu gốc.
- Sơ đồ Phần 6 đều `flowchart TB` theo ràng buộc (0 sơ đồ LR). Sơ đồ 6.6 dùng `sequenceDiagram` (vertical tự nhiên).
- Render Mermaid cần Chrome cho puppeteer (Chrome for Testing 131); width render 1600px.
- Code snippet trong Phần 6 lấy nguyên văn từ `core/agent/*.py` và `generation_config.yaml` — không bịa.

## E. Bug đã tài liệu hóa (từ chu trình review A–F của source)
Phần 6 ghi lại 4 bug thực với triệu chứng/nguyên nhân/cách fix:
- GĐ A: contract bug `TabularAggregator` cần `RetrievalResult` (không phải dict) → `ComputeTool._dict_to_result()`.
- GĐ C: ký tự "đ/Đ" không tách qua NFD → khớp văn bản Nghị định/Quyết định hỏng → xử lý "đ/Đ" tường minh trước NFD.
- GĐ E: `session_id` mất qua event meta nhánh agent → session không vào sidebar → bổ sung `session_id`/`steps` vào meta.
- GĐ F: path `_ROOT` sai (perf_eval là sibling của rag-core) → scorer âm thầm vô dụng → thêm `/ "rag-core"`.

## F. Khuyến nghị cho lần sau (chưa làm trong đợt này)
- Phần 3 (BFF): nếu muốn bộ tài liệu đồng nhất, nên build `.docx`/`.pdf` cho Phần 3 (hiện chỉ có `.md`).
- Khi mở rộng corpus eval lên ~110 câu và có runner sinh responses qua agent thật (cần DB staging), bổ sung kết quả eval thực nghiệm vào Phần 6 §8.
