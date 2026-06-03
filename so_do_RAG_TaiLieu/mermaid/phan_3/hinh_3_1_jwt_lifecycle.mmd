flowchart TB
    LOGIN["POST /auth/login (username, password)"]
    AUTH["<b>Authenticate</b> · brute-force check · bcrypt verify · is_active"]

    GEN["<b>Generate JWT HS256</b><br/>access_token (30 min, stateless)<br/>refresh_token (7 days, stateful)"]

    SAVE_RT["<b>save_refresh_token()</b> · SHA-256 hash → refresh_tokens table<br/>(revoke tất cả token cũ của user trước)"]

    CLIENT[("Client lưu<br/>access + refresh token")]

    USE["Mỗi request: Authorization: Bearer &lt;access_token&gt;"]
    DECODE["<b>get_current_user()</b> · decode_access_token() · check is_active · → request.state"]

    REFRESH["POST /auth/refresh khi access hết hạn"]
    DECODE_RT["decode_refresh_token() · verify hash trong DB"]

    LOGIN --> AUTH --> GEN --> SAVE_RT --> CLIENT
    CLIENT --> USE --> DECODE
    CLIENT --> REFRESH --> DECODE_RT --> GEN

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef auth fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef gen fill:#F3E5F5,stroke:#6A1B9A,stroke-width:2px,color:#4A148C
    classDef store fill:#E0F2F1,stroke:#00695C,color:#004D40
    classDef client fill:#FFFDE7,stroke:#F57F17,stroke-width:2px,color:#F57F17

    class LOGIN,USE,REFRESH in
    class AUTH,DECODE,DECODE_RT auth
    class GEN gen
    class SAVE_RT store
    class CLIENT client
