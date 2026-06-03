flowchart TB
    Q["query: 'điều kiện vay vốn tín dụng'<br/>top_k = 5, enable_rerank = true"]
    HYBRID["HybridRetriever<br/>retrieval_k = min(int(5×1.5), 30) = 30"]

    VR["<b>VectorRetriever</b><br/>embed query → ChromaDB (cosine ≥ 0.3)<br/>30 results: chunk_A (0.82), chunk_B (0.78), chunk_C (0.71)..."]
    BR["<b>BM25Retriever</b><br/>tokenize · SQL · batch BM25 · norm s/(s+10)<br/>30 results: chunk_C (0.85), chunk_E (0.81), chunk_A (0.74)..."]

    RRF["<b>RRF fusion (k=60)</b> · score(d) = Σ 1/(k+rank)<br/>chunk_C: 1/61 + 1/62 = 0.0326<br/>chunk_A: 1/61 + 1/63 = 0.0322<br/>chunk_B: 1/62 = 0.0161"]

    OUT[/"top input_k=20 → Hình 3.2b"\]

    Q --> HYBRID
    HYBRID --> VR
    HYBRID --> BR
    VR --> RRF
    BR --> RRF
    RRF --> OUT

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef sub fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef merge fill:#FFFDE7,stroke:#F57F17,stroke-width:2px,color:#F57F17
    classDef out fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C

    class Q in
    class VR,BR sub
    class HYBRID,RRF merge
    class OUT out
