flowchart TB
    T_YAML["📋 YAML config (type + field_mapping)"]
    T_REG["register_tabular_type.py → TABULAR_TYPE_REGISTRY + TABULAR_FIELD_META (immutable)"]
    T_XLS["📊 Excel file (.xlsx)"]
    T_LOAD["load_tabular_data.py · parsers: parse_vnd / parse_percent / parse_currency_amount"]
    T_BUILD["<b>Build search_text (multi-rep numeric) + VECTOR BLOB float32 L2-norm</b> → TABULAR_DATA"]
    T_BM25["TabularBM25Builder → tabular_bm25_statistics + tabular_term_frequencies"]
    T_SIG["📡 tabular_reload_signal.txt → runtime hot-reload"]

    T_YAML --> T_REG
    T_XLS --> T_LOAD
    T_REG --> T_LOAD
    T_LOAD --> T_BUILD --> T_BM25 --> T_SIG

    classDef tab fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20
    classDef io fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef sig fill:#FFFDE7,stroke:#F57F17,stroke-width:2px,color:#F57F17

    class T_REG,T_LOAD,T_BUILD,T_BM25 tab
    class T_YAML,T_XLS io
    class T_SIG sig
