# Data Repo Layout

This reflects the current structure. Use it to place files consistently. If you must deviate, add a clear note in your commit message to inform reviewers.

data/
├── data_generator/                 # Synthetic and provided datasets + generators
│   ├── src/                        # Generation scripts and helpers (Python)
│   │   ├── databricks_utils/       # Metadata update helpers for Databricks
│   │   │   ├── generate_metadata_sql.py
│   │   │   ├── update_metadata_sdk.py
│   │   │   └── update_table_metadata.py
│   │   ├── generate_rgm_data.py    # Main generator for RGM demo data
│   │   └── statistical_models.py   # Seasonal patterns and market share logic
│   ├── provided_data/              # Realistic seed CSVs used as inputs
│   ├── generated_data/             # Generated dim/fact CSVs (source of truth)
│   ├── tests/                      # Data quality and scenario tests
│   │   ├── run_all_tests.py
│   │   ├── test_*.py               # constraints, dimensions, facts, visuals
│   │   └── validate_constraints.py
│   ├── generate_data.py            # CLI wrapper for generation
│   ├── run_tests.py                # Convenience runner
│   ├── README.md
│   ├── RGM_DATA_QUERY_GUIDE.md     # How to query the generated data
│   └── AGENTS.md                   # Assistant guidance for data work
├── etl/                            # Databricks ETL bundle (dev)
│   ├── .databricks/                # Bundle metadata and env overrides
│   │   ├── bundle/                 # Terraform state for the bundle (dev)
│   │   └── .databricks.env         # Local env for bundle commands
│   ├── src/
│   │   ├── jobs/                   # Job entrypoints for Databricks
│   │   │   ├── create_master_product_hierarchy.py
│   │   │   └── __init__.py
│   │   └── __init__.py
│   ├── databricks.yml              # Bundle config
│   ├── requirements.txt            # Runtime deps for jobs
│   └── Makefile                    # Common ETL commands
├── AGENTS.md
└── .gitignore
