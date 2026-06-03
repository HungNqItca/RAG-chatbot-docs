flowchart TB
    DT_IN["TABULAR_DATA row (Hình 2.3a)"]
    ST_RAW["RAW field values: '0.05%' · '10000' · '500000'"]
    ST_PCT["Phần trăm: '0.05%' → '0.05% phần trăm 0.0005'"]
    ST_VND["VND: '10000' → '10000 10.000 10 nghìn'<br/>'500000' → '500000 500.000 500 nghìn'"]
    ST_OUT["Concat → search_text (cho BM25 + embedding)"]
    BM_TF["tabular_term_frequencies (type_tab, code_tab, term, tf, normalized_tf)"]
    BM_NORM["norm: tabular = 1/(1+e^(-raw+3)) · legal = raw/(raw+10)"]

    DT_IN --> ST_RAW
    ST_RAW --> ST_PCT
    ST_RAW --> ST_VND
    ST_PCT --> ST_OUT
    ST_VND --> ST_OUT
    ST_OUT --> BM_TF --> BM_NORM

    classDef in fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef txt fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef bm25 fill:#E1F5FE,stroke:#0277BD,color:#01579B

    class DT_IN in
    class ST_RAW,ST_PCT,ST_VND,ST_OUT txt
    class BM_TF,BM_NORM bm25
