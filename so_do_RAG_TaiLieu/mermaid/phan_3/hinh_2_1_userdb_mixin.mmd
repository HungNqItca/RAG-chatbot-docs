flowchart TB
    UDB["<b>UserDB</b> · thin shell · singleton get_user_db()<br/>kế thừa từ 7 mixin class"]

    M1["<b>_ConnectionMixin</b><br/>WAL+FK<br/>_open · _conn · _tx"]
    M2["<b>_MigrationsMixin</b><br/>3 idempotent<br/>migrations"]
    M3["<b>_UsersCrudMixin</b><br/>CRUD + lockout<br/>(failed_login)"]
    M4["<b>_RefreshTokensMixin</b><br/>SHA-256 hash<br/>save/revoke"]
    M5["<b>_SessionMappingsMixin</b><br/>save/load + cleanup<br/>(skip nếu active=[])"]
    M6["<b>_ConversationsMixin</b><br/>permanent history<br/>+ session_meta"]
    M7["<b>_ActivityLogMixin</b><br/>log_chat_activity<br/>GMT+7 timestamp"]

    UDB --> M1
    UDB --> M2
    UDB --> M3
    UDB --> M4
    UDB --> M5
    UDB --> M6
    UDB --> M7

    classDef shell fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef mixin fill:#E1F5FE,stroke:#0277BD,color:#01579B

    class UDB shell
    class M1,M2,M3,M4,M5,M6,M7 mixin
