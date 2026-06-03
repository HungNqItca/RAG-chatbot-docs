flowchart TB
    START["BFF startup (lifespan)"]
    LOAD["session_store.load_from_db() · SELECT * FROM user_sessions → memory"]
    QUERY["GET RAG_CORE/api/v1/sessions · danh sách active + user_id metadata"]

    GUARD{"active_ids rỗng?<br/>(RAG Core trả 0 session)"}

    SKIP["<b>BỎ QUA cleanup</b> (log WARNING) · tránh wipe khi RAG Core restart độc lập"]

    CLEAN["<b>cleanup_stale_session_mappings()</b> · DELETE WHERE session_id NOT IN active_ids"]
    RECOVER["<b>Recover</b> · sessions có user_id trong RAG Core nhưng thiếu trong BFF → re-register"]

    STATS[/"log {cleaned, recovered, skipped_no_uid}"\]

    START --> LOAD --> QUERY --> GUARD
    GUARD -->|"yes"| SKIP --> STATS
    GUARD -->|"no"| CLEAN --> RECOVER --> STATS

    classDef start fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef step fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef skip fill:#FFEBEE,stroke:#C62828,color:#B71C1C
    classDef clean fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef stats fill:#E0F2F1,stroke:#00695C,stroke-width:2px,color:#004D40

    class START start
    class LOAD,QUERY,RECOVER step
    class GUARD decision
    class SKIP skip
    class CLEAN clean
    class STATS stats
