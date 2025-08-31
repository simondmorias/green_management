# Coding Guidelines for AI Agents - Gain Platform

## Repository Structure
- `/data`: ETL pipelines and data generation (Python/Databricks). This is the data warehouse.
- `/infra`: Docker infrastructure (compose.yaml, PostgreSQL, Redis). This is for development environments only.
- `/gain`: Gain Product app (Next.js frontend, FastAPI backend, LangGraph agents). This is the main repo.
- `/edh`: Entity Data Hub (TypeScript monorepo with pnpm workspaces). Our MDM solution.
- `/management`: Documentation and PMO templates (markdown only, no code) - contains features, specifications and tasks
