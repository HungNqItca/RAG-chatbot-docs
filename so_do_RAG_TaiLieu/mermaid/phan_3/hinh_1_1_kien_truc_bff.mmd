flowchart TB
    FE["🌐 React Frontend (:5173)"]

    subgraph BFF["BFF Service (:8082) — Cổng bảo mật giữa FE và RAG Core"]
        direction TB
        MW["<b>Middleware Stack</b> · RequestLogger → CORS → SlowAPI"]
        ROUTERS["<b>Routers</b> · auth/* · api/v1/* · admin/* · /health"]
        CORE["<b>Core</b> · UserDB (SQLite) · SessionStore · SSE Proxy (httpx shared)"]
    end

    RC["🤖 RAG Core (:8001) — bind 127.0.0.1, không expose ra ngoài"]

    FE -->|"HTTP/HTTPS · JWT Bearer"| MW --> ROUTERS --> CORE
    CORE -->|"httpx.stream() / call_rag_core_json()"| RC

    classDef fe fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef bff fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef rc fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20

    class FE fe
    class MW,ROUTERS,CORE bff
    class RC rc
