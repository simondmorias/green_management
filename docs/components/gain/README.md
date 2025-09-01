# Gain Application Documentation

This directory contains detailed technical documentation for the Gain conversational AI platform.

## Documentation Index

### Core Documentation

1. **[Application Setup](./application-setup.md)**
   - Complete technology stack overview
   - Project structure and organization
   - Development environment setup
   - Build and deployment processes
   - Configuration management

2. **[Frontend Architecture](./frontend-architecture.md)**
   - Next.js/React implementation details
   - Component architecture and patterns
   - State management strategies
   - TypeScript type system
   - Performance optimizations
   - Testing approach

3. **[Backend Architecture](./backend-architecture.md)**
   - FastAPI service architecture
   - API route structure
   - Service layer design patterns
   - Data models and validation
   - Caching strategies
   - Error handling and logging

4. **[Entity Recognition System](./entity-recognition-system.md)**
   - Aho-Corasick algorithm implementation
   - Pattern matching and overlap resolution
   - Data generation from Databricks
   - Performance characteristics
   - Fuzzy matching capabilities
   - Future enhancements

### Supporting Documentation

5. **[Repository Layout](./repo_layout.md)**
   - Detailed file and folder structure
   - Module organization
   - Naming conventions

6. **[Manual Test Scenarios](./manual-test-scenarios.md)**
   - End-to-end testing procedures
   - Feature validation steps
   - Performance testing

## Quick Links

### Development
- [Frontend Setup](./frontend-architecture.md#development-setup)
- [Backend Setup](./backend-architecture.md#development-setup)
- [Environment Configuration](./application-setup.md#configuration)

### Architecture
- [System Overview](./application-setup.md#overview)
- [Technology Stack](./application-setup.md#technology-stack)
- [Data Flow](./application-setup.md#key-components)

### Features
- [Live Intent Highlighting](./entity-recognition-system.md#overview)
- [Chat Interface](./frontend-architecture.md#key-components)
- [API Endpoints](./backend-architecture.md#api-routes)

## Key Technologies

### Frontend
- Next.js 13+ (React 18)
- TypeScript
- Tailwind CSS
- Custom React Hooks

### Backend
- FastAPI (Python 3.9+)
- Pydantic v2
- pyahocorasick
- Redis (optional)

### Infrastructure
- Monorepo with pnpm workspaces
- Databricks SQL Warehouse
- Docker (managed in infra repo)

## Performance Targets

- **Entity Recognition**: < 100ms response time
- **API Latency**: < 200ms p95
- **Frontend Bundle**: < 500KB gzipped
- **Cache Hit Rate**: > 80%

## Recent Updates

### Feature F3 - Live Intent Highlighting (Completed)
- Real-time entity recognition as users type
- Aho-Corasick pattern matching implementation
- Redis caching layer with graceful fallback
- Debounced frontend requests (500ms)
- Clean inline display with tooltips

### Improvements
- Longest match preference in entity recognition
- Removed overlapping UI pills for cleaner interface
- Added year+quarter pattern recognition (e.g., "2025 Q1")
- Removed common words from recognition (e.g., "what", "how")

## Contact & Support

For questions about the Gain application architecture or implementation details, please refer to the specific documentation files above or contact the development team.

## Related Documentation

- **Architecture Overview**: `/docs/architecture.md` (higher-level system design)
- **Infrastructure**: Managed in separate `infra` repository
- **Agents & Services**: See `/services` directory documentation