flowchart TB
    IN["top-20 từ RRF (Hình 3.2a)"]
    RERANK["<b>CrossEncoderReranker</b> · mmarco-mMiniLMv2-L12-H384-v1<br/>predict([query, text] × 20) → sigmoid → output_k=10"]
    POST["<b>Post-process</b> · min_score≥0.35 · dedup Jaccard≥0.95 · cắt → 5"]
    AE["<b>Article Expansion (MongoDB)</b> · ghép Khoản, truncate 1500 từ → article_text"]
    OUT[/"5 RetrievalResult + article_text full Điều"\]

    IN --> RERANK --> POST --> AE --> OUT

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef rerank fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#4A148C
    classDef post fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef ae fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef out fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C

    class IN in
    class RERANK rerank
    class POST post
    class AE ae
    class OUT out
