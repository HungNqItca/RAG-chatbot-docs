flowchart TB
    YT["YAML type definition<br/>type_tab: PHI<br/>fields: FT1=MUC_PHI · FN1=SOTIEN_MIN · FN2=SOTIEN_MAX"]
    META_T["TABULAR_FIELD_META · immutable mapping<br/>(TYPE_TAB, FIELD_SLOT) → FIELD_NAME<br/>(PHI, FT1) → MUC_PHI<br/>(PHI, FN1) → SOTIEN_MIN"]
    YM["YAML mapping (per Excel)<br/>column_mapping:<br/>FT1: {col_idx: 3}<br/>FN1: {col_idx: 4, parser: parse_vnd}"]
    DATA_T["TABULAR_DATA (EAV-lite + VECTOR)<br/>10 text · 10 numeric · 5 date slots<br/>+ search_text · VECTOR BLOB · SOURCE_DOC"]
    NEXT(["→ Hình 2.3b<br/>search_text multi-rep & BM25 normalization"])

    YT --> META_T --> DATA_T
    YM --> DATA_T
    DATA_T --> NEXT

    classDef yaml fill:#FFF3E0,stroke:#E65100,color:#BF360C
    classDef meta fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20
    classDef data fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#4A148C
    classDef link fill:#FFFDE7,stroke:#F57F17,stroke-dasharray:5 5,color:#F57F17

    class YT,YM yaml
    class META_T meta
    class DATA_T data
    class NEXT link
