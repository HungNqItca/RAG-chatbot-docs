flowchart TB
    START["question + session_id"]
    PRE_1A["<b>Tiền xử lý 1A</b> · Implicit Continuation"]
    PRE_1B["<b>Tiền xử lý 1B</b> · Abbreviation Normalization"]
    PRE_2C["<b>Tiền xử lý 2C</b> · Disambiguation Choice"]
    A["<b>Tầng A — Slot Detection</b> (regex ~0ms · QuerySlots)"]
    NEXT(["→ Hình 4.2b · Quyết định B/B.2/B.5/C/D"])

    START --> PRE_1A --> PRE_1B --> PRE_2C --> A --> NEXT

    classDef pre fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef tier_a fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef terminal fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef link fill:#FFF3E0,stroke:#E65100,stroke-dasharray:5 5,color:#BF360C

    class PRE_1A,PRE_1B,PRE_2C pre
    class A tier_a
    class START terminal
    class NEXT link
