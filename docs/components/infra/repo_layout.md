# Infra Repo Layout

Current development-only infra. Use for local environments; production infra is out of scope here.

infra/
├── compose.yaml        # Docker Compose for local stack (Postgres, Redis, etc.)
├── postgres/           # Postgres init, volumes, and configs (if present)
├── redis/              # Redis configs (if present)
└── README.md           # How to start/stop the local stack
