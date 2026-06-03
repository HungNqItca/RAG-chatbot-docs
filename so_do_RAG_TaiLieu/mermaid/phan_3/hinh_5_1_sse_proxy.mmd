flowchart TB
    REQ["POST /api/v1/chat/stream (question, session_id?, fetch_history?)"]

    PREP["<b>_prepare_chat_payload()</b><br/>· _resolve_session() · is_new check<br/>· _attach_history_if_needed() nếu fetch_history=True"]

    REG["<b>session_store.register_session(user_id, session_id)</b><br/>memory + DB INSERT (trước khi forward — tránh orphan session)"]

    STREAM["<b>stream_from_rag_core(payload, user_id, request, on_done)</b><br/>_shared_client.stream('POST', /chat/stream, json=payload)"]

    GEN["<b>event_generator()</b> · acc_content=[] · acc_sources=[]<br/>async for sse_line in stream → parse JSON event"]

    EV{"event type?"}

    META["<b>meta event</b><br/>acc_sources = ev.sources<br/>nếu is_new → update_session_metadata(title=question[:100]) <i>early-save</i>"]

    TOKEN["<b>token event</b><br/>acc_content.append(content)"]

    DONE["<b>done event</b><br/>final_sources = ev.effective_sources OR acc_sources<br/>_persist_chat_result(...)"]

    YIELD[/"yield sse_line → client"\]

    REQ --> PREP --> REG --> STREAM --> GEN --> EV
    EV -->|"meta"| META --> YIELD
    EV -->|"token"| TOKEN --> YIELD
    EV -->|"done"| DONE --> YIELD

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef prep fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef reg fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef stream fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#4A148C
    classDef event fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef yield fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17

    class REQ in
    class PREP prep
    class REG reg
    class STREAM,GEN stream
    class META,TOKEN,DONE event
    class YIELD yield
    class EV decision
