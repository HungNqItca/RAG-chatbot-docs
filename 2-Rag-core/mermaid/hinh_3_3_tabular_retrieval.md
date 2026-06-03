flowchart TB
    Q["query · top_k=10 · type_tab_hint='PHI'"]
    INIT["<b>_init_tabular_retrievers()</b> (lazy, double-checked lock)"]
    SIG["reload signal check + rebuild nếu mới"]
    DUAL["<b>BM25 + Vector parallel</b> (filter type_tab · top_k×2 mỗi loại)"]
    RRF_T["<b>shared.fusion.rrf_merge()</b> · rrf_k=60 · top_k=10"]
    META["build_tabular_metadata · type_tab/code_tab/item_name/source_doc/FT*/FN*"]
    OUT[/"List[RetrievalResult] (không article_text)"\]

    Q --> INIT --> SIG --> DUAL --> RRF_T --> META --> OUT

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef init fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef sig fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef dual fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef merge fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef meta fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef out fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C

    class Q in
    class INIT init
    class SIG sig
    class DUAL dual
    class RRF_T merge
    class META meta
    class OUT out
