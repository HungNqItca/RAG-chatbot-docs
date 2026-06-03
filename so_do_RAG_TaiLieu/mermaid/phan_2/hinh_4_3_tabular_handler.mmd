flowchart TB
    Q["question (content_type='tabular')"]
    PREP["session.get_history() + _condense_if_needed_async() + <b>P3 QueryAnalyzer</b>"]
    RET_NUM["retrieve_tabular() (BM25+Vector+RRF) + <b>P3 _apply_numeric_filters</b> (boost VND±20%, %±10%)"]

    P5_GATE{"<b>P5 Gate</b> · top score ≥ 0.005?"}
    NO_CTX["build_no_context_prompt()"]

    P5_AGG{"aggregation_intent ≠ LOOKUP?"}
    AGG["<b>TabularAggregator</b> · COMPARE/MIN/MAX/LIST_ALL → markdown"]
    P1["<b>P1 · TabularPromptBuilder</b> field-aware labels"]
    LLM["LLM stream + TABULAR_SYSTEM_PROMPT · SSE meta/tokens/done/followups"]

    Q --> PREP --> RET_NUM --> P5_GATE
    P5_GATE -->|"no"| NO_CTX --> LLM
    P5_GATE -->|"yes"| P5_AGG
    P5_AGG -->|"yes"| AGG --> LLM
    P5_AGG -->|"no"| P1 --> LLM

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef session fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef retrieve fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef p5 fill:#FCE4EC,stroke:#C2185B,color:#C2185B
    classDef agg fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef p1 fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef llm fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17

    class Q in
    class PREP session
    class RET_NUM retrieve
    class NO_CTX p5
    class AGG agg
    class P1 p1
    class LLM llm
    class P5_GATE,P5_AGG decision
