flowchart TB
    START["ingest_document(file)"]
    WS["write_state = {<br/>mongo: F, sqlite_doc: F,<br/>sqlite_chunks: F, chroma: F}"]

    M["[1] MongoDB insert_document()<br/>→ write_state['mongo'] = True"]
    SD["[2] SQLite insert_document_meta()<br/>→ write_state['sqlite_doc'] = True"]
    SC["[3] SQLite insert_chunks_batch()<br/>→ write_state['sqlite_chunks'] = True"]
    CH["[4] ChromaDB upsert_documents()<br/>→ write_state['chroma'] = True"]

    OK[/"✓ IngestionResult success=True"\]
    EX[("⚠ Exception ở bất kỳ bước nào<br/>→ Hình 2.2b Rollback")]

    START --> WS --> M --> SD --> SC --> CH --> OK
    M -.-> EX
    SD -.-> EX
    SC -.-> EX
    CH -.-> EX

    classDef step fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef exception fill:#FCE4EC,stroke:#C2185B,stroke-width:2px,color:#C2185B
    classDef terminal fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C

    class M,SD,SC,CH step
    class EX exception
    class START,WS,OK terminal
