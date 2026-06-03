flowchart TB
    REQ["Request /admin/users (POST/PUT/DELETE)"]
    DEP["<b>FastAPI Depends(require_admin)</b> · rank ≥ 3"]
    ACTOR{"Actor role rank?"}

    R12["<b>Hierarchy checks:</b><br/>· assert_can_assign_role(actor, body.role) — raise 403 nếu rank(new_role) ≥ rank(actor)<br/>· assert_can_manage_target(actor, target) — raise 403 nếu rank(target) ≥ rank(actor)"]

    SELF{"actor.id == target.id?"}
    SELF_BLOCK["<b>Self-protect</b> · không cho phép tự xóa / tự khóa / tự đổi role"]
    BLOCK[("❌ 403 Forbidden")]
    OK[/"✓ Cho phép thao tác"\]

    REQ --> DEP --> ACTOR
    ACTOR -->|"user/manager"| BLOCK
    ACTOR -->|"admin/superadmin"| R12
    R12 --> SELF
    SELF -->|"yes"| SELF_BLOCK
    SELF -->|"no"| OK

    NOTE["<b>ROLE_RANK</b> · user=1 · manager=2 · admin=3 · superadmin=4<br/>Luật: rank(target) &lt; rank(actor) — admin KHÔNG thể tạo/sửa admin khác (chỉ superadmin)"]
    NOTE -.- DEP

    classDef in fill:#FFF3E0,stroke:#E65100,stroke-width:2px,color:#BF360C
    classDef dep fill:#E1F5FE,stroke:#0277BD,color:#01579B
    classDef check fill:#FFFDE7,stroke:#F57F17,color:#F57F17
    classDef block fill:#FFEBEE,stroke:#C62828,stroke-width:2px,color:#B71C1C
    classDef ok fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20
    classDef note fill:#FFFDE7,stroke:#F57F17,stroke-dasharray:5 5,color:#5D4037

    class REQ in
    class DEP,R12 dep
    class ACTOR,SELF check
    class BLOCK,SELF_BLOCK block
    class OK ok
    class NOTE note
