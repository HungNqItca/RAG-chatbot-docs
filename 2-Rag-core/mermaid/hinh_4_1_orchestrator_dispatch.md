flowchart TB
    Q["ChatRequest (question · session_id?)"]
    SES["_ensure_session()"]
    HIS["session.get_history()"]
    CLF["_classify_and_log()<br/>ContentTypeClassifier 8 rules"]

    UD{"unified_dispatch.enabled?"}

    UNI["_run_unified_retrieval()<br/>retrieve_unified_sync() · ThreadPoolExecutor<br/>Legal + Tabular pool → CrossEncoder rerank"]
    URDISP{"top-1 content_type?"}
    LH_WR["legal_handler.<br/>stream_answer_with_results()<br/>(skip _retrieve)"]
    TH_WR["tabular_handler.<br/>stream_answer_with_results()<br/>(skip _retrieve)"]

    LEGACY{"classifier content_type?"}
    LH["legal_handler.stream_answer()<br/>full Query Pipeline 4 tầng"]
    TH["tabular_handler.stream_answer()<br/>condense → retrieve → prompt"]

    FB["⚠ Fallback _legacy_stream()<br/>(reranker off / results empty)"]

    PROMPT["build_prompt() → LLM stream → SSE meta/tokens/done"]

    Q --> SES --> HIS --> CLF --> UD
    UD -->|"true"| UNI --> URDISP
    URDISP -->|"legal"| LH_WR --> PROMPT
    URDISP -->|"tabular"| TH_WR --> PROMPT
    UNI -.->|"on error"| FB --> LEGACY

    UD -->|"false"| LEGACY
    LEGACY -->|"legal"| LH --> PROMPT
    LEGACY -->|"tabular"| TH --> PROMPT

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef session fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef classify fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef unified fill:#FCE4EC,stroke:#C2185B,stroke-width:2px,color:#C2185B
    classDef legal fill:#FFF3E0,stroke:#E65100,color:#BF360C
    classDef tabular fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef llm fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20
    classDef fallback fill:#FFEBEE,stroke:#C62828,stroke-dasharray:5 5,color:#B71C1C
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17

    class Q in
    class SES,HIS session
    class CLF classify
    class UNI,LH_WR,TH_WR unified
    class LH legal
    class TH tabular
    class PROMPT llm
    class FB fallback
    class UD,URDISP,LEGACY decision
