 ```mermaid
flowchart TB
    START(["Input: file .docx / .pdf / .txt<br/>+ domain code + title tuỳ chọn"])

    S0["Bước 0 — Validate file<br/>Extension .docx/.pdf/.txt<br/>Size ≤ 50 MB"]

    S05{"Bước 0.5 — Checksum Guard<br/>MD5 file đã có<br/>trong documents table?"}
    SKIP(["✓ SKIP — trả về IngestionResult<br/>skipped=true, document_id cũ"])

    REINGEST{"document_id đã tồn tại<br/>(checksum khác)?"}
    PRE_DELETE["Pre-delete từ cả 3 DB<br/>delete_document_from_all_dbs<br/>Clean slate re-ingestion"]

    S1["Bước 1 — Parse Document<br/>LegalDocumentSplitter.parse_document<br/>→ LegalDocumentSchema<br/>(Điều → Khoản hierarchical)"]

    S2["Bước 2 — Lưu MongoDB<br/>insert_document → mongo_id<br/>write_state.mongo=True"]

    S3["Bước 3 — Lưu SQLite documents<br/>file_name, title, domain,<br/>total_articles, total_clauses,<br/>checksum, file_size<br/>write_state.sqlite_doc=True"]

    S4["Bước 4 — Lưu SQLite chunks<br/>1 Khoản = 1 chunk<br/>insert_chunks_batch<br/>text (normalized) + original_text<br/>write_state.sqlite_chunks=True"]

    S5["Bước 5 — Generate Embeddings<br/>+ Lưu ChromaDB<br/>sentence-transformers model<br/>upsert_documents (idempotent)<br/>write_state.chroma=True"]

    S6{"Bước 6 — Build BM25?<br/>auto_build_bm25 flag"}
    S6A["BM25Builder.build_index<br/>IDF + TF → bm25_statistics<br/>+ chunk_term_frequencies"]
    S6B["Skip — chạy rebuild_bm25.py sau"]

    DONE(["✓ IngestionResult<br/>success=true<br/>document_id + metrics"])

    ERROR{{"Exception xảy ra<br/>trong bất kỳ bước nào"}}
    ROLLBACK["_rollback_partial_ingestion<br/>Compensating transaction<br/>theo thứ tự ngược"]
    FAIL(["✗ IngestionResult<br/>success=false + error details"])

    START --> S0
    S0 --> S05
    S05 -->|Checksum trùng| SKIP
    S05 -->|Không có hoặc khác| S1
    S1 --> REINGEST
    REINGEST -->|Có| PRE_DELETE
    REINGEST -->|Không| S2
    PRE_DELETE --> S2
    S2 --> S3
    S3 --> S4
    S4 --> S5
    S5 --> S6
    S6 -->|Có| S6A
    S6 -->|Không| S6B
    S6A --> DONE
    S6B --> DONE

    S1 -.->|fail| ERROR
    S2 -.->|fail| ERROR
    S3 -.->|fail| ERROR
    S4 -.->|fail| ERROR
    S5 -.->|fail| ERROR
    ERROR --> ROLLBACK
    ROLLBACK --> FAIL

    classDef startNode fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef stepNode fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef decisionNode fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef successNode fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px
    classDef errorNode fill:#ffebee,stroke:#c62828,stroke-width:2px
    class START,DONE,SKIP,FAIL startNode
    class S0,S1,S2,S3,S4,S5,S6A,S6B,PRE_DELETE stepNode
    class S05,REINGEST,S6,ERROR decisionNode
    class ROLLBACK errorNode
```