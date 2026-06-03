flowchart TB
    Q["RetrievalQuery<br/>(query_text, top_k, strategy,<br/>enable_rerank, filters)"]

    CACHE{"TTL Cache hit?<br/>(MD5 key)"}
    HIT["Trả về<br/>strategy_used: '...[cached]'"]

    STRAT{"strategy?"}

    V["<b>VectorRetriever</b><br/>ChromaDB cosine<br/>score = 1 - distance<br/>filter ≥ 0.3"]
    K["<b>BM25Retriever</b><br/>SQLite Okapi BM25<br/>k1=1.5, b=0.75<br/>norm: s/(s+10)"]
    H["<b>HybridRetriever</b><br/>Vector + BM25<br/>RRF (k=60) hoặc<br/>weighted (α=0.7)"]

    A["<b>Adaptive Router</b><br/>(8 rules, first match)"]

    R1["1. Số hiệu '39/2016/TT-NHNN'<br/>→ keyword"]
    R2["2. 'Thông tư N' / 'Nghị định N'<br/>→ keyword"]
    R3["3. 'Điều N quy định/nói/...'<br/>→ hybrid"]
    R4["4. Tham chiếu Điều/Khoản<br/>không hỏi nội dung<br/>→ keyword"]
    R5["5. Query ≤ 5 từ + có domain<br/>keyword trong adaptive_keywords<br/>→ keyword"]
    R6["6. Query rất ngắn ≤ 3 từ<br/>→ keyword"]
    R7["7. Query dài ≥ 12 từ<br/>→ vector"]
    R8["8. Còn lại (4-11 từ)<br/>→ hybrid"]

    RES["List[RetrievalResult]<br/>(input_k=20)"]

    RR{"enable_rerank?"}
    RERANK["<b>CrossEncoderReranker</b><br/>mmarco-mMiniLMv2-L12-H384<br/>sigmoid → score ∈ [0,1]<br/>output_k=10"]

    POST["<b>Post-processing</b><br/>· min_score ≥ 0.35 (chỉ rerank)<br/>· dedup Jaccard ≥ 0.95<br/>· MMR (default off)<br/>· Article Expansion từ MongoDB<br/>· cắt max_final_results=5"]

    OUT[/"RetrievalResponse<br/>article_text per chunk"\]

    Q --> CACHE
    CACHE -->|"hit"| HIT --> OUT
    CACHE -->|"miss"| STRAT

    STRAT -->|"vector"| V
    STRAT -->|"keyword"| K
    STRAT -->|"hybrid"| H
    STRAT -->|"adaptive"| A

    A --> R1
    A --> R2
    A --> R3
    A --> R4
    A --> R5
    A --> R6
    A --> R7
    A --> R8
    R1 -.-> K
    R2 -.-> K
    R4 -.-> K
    R5 -.-> K
    R6 -.-> K
    R3 -.-> H
    R8 -.-> H
    R7 -.-> V

    V --> RES
    K --> RES
    H --> RES

    RES --> RR
    RR -->|"true"| RERANK --> POST
    RR -->|"false"| POST
    POST --> OUT

    classDef in fill:#FFF3E0,stroke:#E65100
    classDef vector fill:#E1F5FE,stroke:#0277BD
    classDef keyword fill:#E8F5E9,stroke:#2E7D32
    classDef hybrid fill:#FFFDE7,stroke:#F57F17
    classDef adaptive fill:#FCE4EC,stroke:#C2185B
    classDef rerank fill:#F3E5F5,stroke:#6A1B9A
    classDef post fill:#E0F2F1,stroke:#00695C
    classDef out fill:#FFEBEE,stroke:#C62828,stroke-width:2px
    classDef decision fill:#FFFDE7,stroke:#F57F17

    class Q,HIT in
    class V vector
    class K keyword
    class H hybrid
    class A,R1,R2,R3,R4,R5,R6,R7,R8 adaptive
    class RERANK rerank
    class POST,RES post
    class OUT out
    class CACHE,STRAT,RR decision
