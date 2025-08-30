This project does not contain code - only documentation for product requirements in markdown format.

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

All documents should maintain cross-references to related artifacts for traceability