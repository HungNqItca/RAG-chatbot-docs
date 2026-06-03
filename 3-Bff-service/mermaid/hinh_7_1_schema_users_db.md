flowchart TB
    USERS["<b>users</b><br/>id (UUID PK) · username UNIQUE · password_hash bcrypt<br/>role CHECK(user/manager/admin/superadmin) · is_active<br/>failed_login_attempts · locked_until · last_login"]

    RT["<b>refresh_tokens</b><br/>id PK AUTOINCR · user_id FK<br/>token_hash (SHA-256) · revoked · created_at"]

    US["<b>user_sessions</b><br/>session_id PK · user_id FK · title · updated_at<br/>(persist ownership map + sidebar metadata)"]

    CONV["<b>conversations</b><br/>id PK · session_id FK ON DELETE CASCADE · user_id<br/>role(user/assistant) · content · sources_json (json.dumps)"]

    CAL["<b>chat_activity_log</b><br/>id PK · session_id · user_id FK · question_preview<br/>generation_time_ms · created_at GMT+7"]

    USERS -->|"FK user_id"| RT
    USERS -->|"FK user_id<br/>ON DELETE CASCADE"| US
    USERS -->|"FK user_id"| CAL
    US -->|"FK session_id<br/>ON DELETE CASCADE"| CONV

    classDef users fill:#E1F5FE,stroke:#0277BD,stroke-width:2px,color:#01579B
    classDef rt fill:#FCE4EC,stroke:#C2185B,color:#C2185B
    classDef us fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20
    classDef conv fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C
    classDef cal fill:#FFF3E0,stroke:#E65100,color:#BF360C

    class USERS users
    class RT rt
    class US us
    class CONV conv
    class CAL cal
