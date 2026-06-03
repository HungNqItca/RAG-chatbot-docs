flowchart TB
    REQ["POST /auth/login (username, password)"]

    GET["get_user_by_username()"]

    LOCK{"locked_until còn hiệu lực?"}
    L423[("❌ HTTP 423 Locked<br/>'Tài khoản tạm khóa, thử lại sau X phút'")]

    VERIFY{"verify_password() OK?"}
    INC["<b>increment_failed_login()</b><br/>+1 attempt; nếu ≥ 5 → SET locked_until = now()+15min"]
    L401[("❌ HTTP 401 Unauthorized")]

    ACTIVE{"is_active == 1?"}
    L403[("❌ HTTP 403 Forbidden")]

    OK_FLOW["<b>reset_failed_login()</b> + <b>update_last_login()</b><br/>create_access_token + create_refresh_token<br/>save_refresh_token (SHA-256, revoke old)"]

    OK[/"✓ TokenResponse"\]

    REQ --> GET --> LOCK
    LOCK -->|"yes"| L423
    LOCK -->|"no"| VERIFY
    VERIFY -->|"sai"| INC --> L401
    VERIFY -->|"đúng"| ACTIVE
    ACTIVE -->|"không"| L403
    ACTIVE -->|"có"| OK_FLOW --> OK

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef step fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef decision fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef block fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef ok fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20

    class REQ in
    class GET,INC,OK_FLOW step
    class LOCK,VERIFY,ACTIVE decision
    class L423,L401,L403 block
    class OK ok
