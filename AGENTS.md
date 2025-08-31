This project does not contain code - only documentation for product requirements in markdown format.

## Documentation Structure

The management repository contains several key documentation areas:

### 1. PMO Documentation (`/management/docs/pmo/`)
- **Features** (`features/`) - Product feature specifications
- **Templates** (`templates/`) - Standardized templates for features, plans, and tasks

### 2. Component Documentation (`/management/docs/components/`)
- **Architecture** (`architecture.md`) - Overall system architecture
- **Data** (`data/`) - Data warehouse and ETL pipeline documentation
- **EDH** (`edh/`) - Entity Data Hub (MDM solution) documentation
- **Gain** (`gain/`) - Main product app documentation
- **Infra** (`infra/`) - Development infrastructure documentation

### 3. Agent Configuration (`/management/.opencode/`)
- **Agent** (`agent/`) - Agent-specific configurations and prompts
  - `planner.md` - Planning agent configuration
  - `reviewer.md` - Review agent configuration
- **README.md** - Agent setup documentation

## Template System

The project uses templates to standardize the creation of features, plans, and tasks. When an agent receives a request to create any of these artifacts, it should use the appropriate template from `/management/docs/pmo/templates/`:

### Available Templates

1. **Feature Template** (`/management/docs/pmo/templates/feature.md`)
   - Used for defining new product features
   - Contains sections for business context, user requirements, functional requirements, and release criteria
   - Follows Gain's conversational-first approach

2. **Plan Template** (`/management/docs/pmo/templates/plan.md`)
   - Used for technical implementation planning
   - Covers architecture alignment, repository changes, data flows, and implementation phases
   - Integrates with the local-first LangGraph + FastAPI architecture

3. **Task Template** (`/management/docs/pmo/templates/task.md`)
   - Used for individual development tasks
   - Includes task requirements, technical details, and developer completion sections
   - Tracks status and acceptance criteria
   - Tasks MUST be small enough to be completed in under 800 lines of code and in a single Repo

## File Organization Structure

When creating new features, follow the F1-example structure located at `/management/docs/pmo/features/F1-example/`:

### Directory Structure
```
/management/docs/pmo/features/
└── F[Number]-[feature-name]/
    ├── feature.md       # Feature requirements document (from feature template)
    ├── plan.md         # Implementation plan (from plan template)
    └── tasks/          # Individual task files
        ├── T1.md       # First task (from task template)
        ├── T2.md       # Second task (from task template)
        └── ...         # Additional tasks as needed
```

### Naming Conventions
- **Feature directories:** `F[Number]-[descriptive-name]` (e.g., F2-customer-analytics, F3-pricing-optimization)
- **Task files:** `T[Number].md` within the tasks subdirectory
- Numbers should increment sequentially based on existing features/tasks

### Usage Instructions for Agents

When a request comes in to create:

1. **A new feature:** 
   - Create a new directory under `/management/docs/pmo/features/` following the naming convention
   - Copy the feature template and populate it as `feature.md`
   - Create the `tasks/` subdirectory for future task files

2. **An implementation plan:**
   - Use the plan template and save it as `plan.md` in the feature directory
   - Reference the associated feature document

3. **A new task:**
   - Use the task template
   - Save it in the feature's `tasks/` subdirectory with the next sequential number
   - Reference both the feature and plan documents

4. **Component documentation:**
   - Update relevant files in `/management/docs/components/` when architecture or component-specific changes occur
   - Each component (data, edh, gain, infra) has its own `repo_layout.md` file
   - Update the main `architecture.md` for system-wide changes

5. **Local agent work:**
   - When working as a specific agent (Opus, Codex, Grok), create feature implementations under `/management/local/Attempts/[AgentName]/`
   - Follow the same structure as PMO features but within the agent-specific directory

All documents should maintain cross-references to related artifacts for traceability