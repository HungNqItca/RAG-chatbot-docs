flowchart TB
    A_OUT["QuerySlots từ Tầng A (Hình 4.2a)"]

    B_CHK{"needs_clarification AND<br/>NOT needs_condenser?"}
    B["<b>Tầng B</b> · Clarification<br/>'Anh chị muốn hỏi văn bản nào?'<br/>→ is_clarification=True"]

    B2_CHK{"≥ 2 docs trùng số?"}
    B2["<b>Tầng B.2</b> · Disambiguation<br/>liệt kê options + ngày BH<br/>→ pending_disambiguation"]

    B5_CHK{"history clarification pattern HOẶC<br/>article_num + doc_ref + NOT comparison?"}
    B5["<b>Tầng B.5</b> · MongoDB direct fetch<br/>(B.5 / B.5b / B.5d 4 biến thể)"]

    C_CHK{"needs_condenser AND history?"}
    C["<b>Tầng C</b> · Contextual Condense<br/>LLM viết lại câu (timeout 5s, fallback gốc)"]

    B5C_CHK{"condensed có article + doc?"}
    B5C["<b>Tầng B.5c</b> · Re-detect + MongoDB fetch"]

    D["<b>Tầng D</b> · Retrieval bình thường<br/>+ _adjust_for_intent()"]

    SR[/"<b>SlotResolutionResult</b>"\]

    A_OUT --> B_CHK
    B_CHK -->|"yes"| B --> SR
    B_CHK -->|"no"| B2_CHK
    B2_CHK -->|"yes"| B2 --> SR
    B2_CHK -->|"no"| B5_CHK
    B5_CHK -->|"yes"| B5 --> SR
    B5_CHK -->|"no"| C_CHK
    C_CHK -->|"yes"| C --> B5C_CHK
    B5C_CHK -->|"yes"| B5C --> SR
    B5C_CHK -->|"no"| D --> SR
    C_CHK -->|"no"| D

    classDef tier_b fill:#FFF3E0,stroke:#E65100,color:#BF360C
    classDef tier_b5 fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef tier_c fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef tier_d fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef terminal fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17

    class B,B2 tier_b
    class B5,B5C tier_b5
    class C tier_c
    class D tier_d
    class A_OUT,SR terminal
    class B_CHK,B2_CHK,B5_CHK,C_CHK,B5C_CHK decision
