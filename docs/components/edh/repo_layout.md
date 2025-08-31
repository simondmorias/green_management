# EDH Repo Layout

Entity Data Hub monorepo using pnpm workspaces. This mirrors the current structure. Keep files aligned; if deviating, call it out in commits.

edh/
├── packages/               # Workspace packages (MDM services, libs)
│   ├── core/               # Core entity models and utils (if present)
│   ├── services/           # API or processors (if present)
│   └── shared/             # Shared libs/types (if present)
├── apps/                   # Executables (admin UI, API) if used
├── pnpm-workspace.yaml     # Workspace definition
├── package.json            # Root scripts and tooling config
├── tsconfig.json           # Base TS config (if present)
└── README.md               # Overview and local dev notes
