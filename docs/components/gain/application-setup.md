# Gain Application Setup & Frameworks

## Overview

Gain is a monorepo-based conversational AI platform built with modern web technologies. The application consists of a React/Next.js frontend, FastAPI backend, and a sophisticated entity recognition system for real-time intent highlighting.

## Technology Stack

### Frontend (apps/web)
- **Framework**: Next.js 13+ (React 18)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **UI Components**: Custom components with shadcn/ui patterns
- **State Management**: React hooks (useState, useEffect, custom hooks)
- **HTTP Client**: Native fetch API with Next.js API routes as proxy
- **Build Tool**: Next.js built-in (Webpack under the hood)
- **Package Manager**: pnpm (workspace enabled)

### Backend (apps/api)
- **Framework**: FastAPI (Python 3.9+)
- **Language**: Python with type hints
- **Validation**: Pydantic v2
- **Entity Recognition**: Custom Aho-Corasick implementation with pyahocorasick
- **Caching**: Redis (optional, graceful fallback)
- **Scheduling**: APScheduler (for artifact updates)
- **ASGI Server**: Uvicorn
- **Testing**: pytest

### Development Tools (devtools/)
- **Entity Generation**: Python scripts for Databricks integration
- **Data Source**: Databricks SQL Warehouse
- **Artifact Format**: JSON (gazetteer) and JSONL (aliases)

## Project Structure

```
gain/
├── apps/
│   ├── api/                 # FastAPI backend
│   │   ├── app/
│   │   │   ├── main.py      # Application entry point
│   │   │   ├── routes/      # API endpoints
│   │   │   ├── services/    # Business logic
│   │   │   ├── schemas/     # Pydantic models
│   │   │   └── data/        # Static data & artifacts
│   │   ├── tests/           # API tests
│   │   └── pyproject.toml   # Python dependencies
│   │
│   └── web/                 # Next.js frontend
│       ├── src/
│       │   ├── pages/       # Next.js pages & API routes
│       │   ├── components/  # React components
│       │   ├── hooks/       # Custom React hooks
│       │   ├── services/    # API client services
│       │   ├── types/       # TypeScript definitions
│       │   └── styles/      # Global styles
│       ├── public/          # Static assets
│       └── package.json     # Node dependencies
│
├── devtools/                # Development utilities
│   └── generate_entity_artifacts.py
│
├── docs/                    # Documentation
│   └── components/gain/     # Application-specific docs
│
└── pnpm-workspace.yaml      # Monorepo configuration
```

## Key Components

### 1. Chat Interface (Frontend)
- **ChatInterface.tsx**: Main chat container component
- **EnhancedMessageInput.tsx**: Input field with entity recognition
- **MessageBubble.tsx**: Individual message display
- **MessageList.tsx**: Chat history display

### 2. Entity Recognition System
- **EntityRecognitionService**: Core recognition logic
- **AhoCorasickMatcher**: Efficient pattern matching algorithm
- **FuzzyMatcher**: Fuzzy matching for typos/variations
- **CacheManager**: Redis caching layer (optional)
- **ArtifactLoader**: Loads entity data from files/cache

### 3. API Routes
- **/api/chat**: Main chat endpoint
- **/api/chat/recognize-entities**: Entity recognition endpoint
- **/api/admin/artifacts/update**: Update entity data
- **/api/admin/scheduler/**: Scheduling management
- **/health**: Health check endpoint

### 4. Data Flow
1. User types in frontend → debounced entity recognition request
2. Next.js API route proxies to FastAPI backend
3. Backend processes text through Aho-Corasick matcher
4. Returns tagged entities with metadata
5. Frontend displays recognized entities inline

## Configuration

### Environment Variables

#### Frontend (.env.local)
```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
```

#### Backend (.env)
```bash
# Databricks Configuration
DATABRICKS_HOST=https://adb-xxxx.azuredatabricks.net
DATABRICKS_HTTP_PATH=/sql/1.0/warehouses/xxxx
DATABRICKS_TOKEN=dapi-xxxx

# Redis Configuration (optional)
REDIS_HOST=localhost
REDIS_PORT=6379

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
```

### Build Configuration

#### Frontend (next.config.js)
```javascript
module.exports = {
  reactStrictMode: true,
  typescript: {
    ignoreBuildErrors: false,
  },
  eslint: {
    ignoreDuringBuilds: false,
  },
}
```

#### Backend (pyproject.toml)
```toml
[tool.poetry]
name = "gain-api"
version = "0.1.0"
description = "Gain Platform API"

[tool.poetry.dependencies]
python = "^3.9"
fastapi = "^0.104.0"
uvicorn = "^0.24.0"
pydantic = "^2.0.0"
pyahocorasick = "^2.0.0"
redis = "^5.0.0"
apscheduler = "^3.10.0"
```

## Development Setup

### Prerequisites
- Node.js 18+ and pnpm
- Python 3.9+ and pip/poetry
- Redis (optional, for caching)
- Databricks access (for entity data)

### Quick Start

1. **Install dependencies**:
```bash
# Root directory
pnpm install

# Backend
cd apps/api
python -m venv .venv
source .venv/bin/activate
pip install -e .

# Or with poetry
poetry install
```

2. **Set up environment variables**:
```bash
# Copy example files
cp apps/web/.env.example apps/web/.env.local
cp apps/api/.env.example apps/api/.env
# Edit with your credentials
```

3. **Generate entity artifacts**:
```bash
cd devtools
python generate_entity_artifacts.py
```

4. **Start development servers**:
```bash
# Terminal 1 - Backend
cd apps/api
make dev  # or: uvicorn app.main:app --reload

# Terminal 2 - Frontend
cd apps/web
pnpm dev  # or: npm run dev
```

5. **Access application**:
- Frontend: http://localhost:3000
- API Docs: http://localhost:8000/docs
- Health Check: http://localhost:8000/health

## Testing

### Frontend Tests
```bash
cd apps/web
pnpm test        # Run tests
pnpm test:watch  # Watch mode
pnpm test:coverage
```

### Backend Tests
```bash
cd apps/api
pytest                    # Run all tests
pytest tests/test_chat.py # Specific file
pytest -v -s             # Verbose with print statements
```

## Build & Deployment

### Frontend Build
```bash
cd apps/web
pnpm build       # Creates .next/ production build
pnpm start       # Starts production server
```

### Backend Build
```bash
cd apps/api
# No build step needed for Python
# Deploy with: uvicorn app.main:app --host 0.0.0.0 --port 8000
```

### Docker Build (managed in infra repo)
```dockerfile
# Frontend Dockerfile example
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/package*.json ./
RUN npm ci --production
EXPOSE 3000
CMD ["npm", "start"]
```

## Performance Considerations

- **Entity Recognition**: < 100ms target response time
- **Debouncing**: 500ms on frontend to reduce API calls
- **Caching**: Redis with 24-hour TTL for entity artifacts
- **Bundle Size**: Code splitting in Next.js
- **API Response**: Streaming for large responses

## Security

- **API Authentication**: Currently none (add JWT/OAuth2 for production)
- **CORS**: Configured in FastAPI for frontend origin
- **Input Validation**: Pydantic models on backend
- **Rate Limiting**: Should be added for production
- **Secrets Management**: Environment variables (use vault in production)

## Monitoring & Logging

- **Frontend**: Console logs in development, Sentry for production
- **Backend**: Python logging module, structured JSON logs
- **Health Checks**: /health endpoint for uptime monitoring
- **Metrics**: Processing time tracked for entity recognition

## Known Issues & Limitations

1. **Desktop Only**: UI requires 1024px+ screen width
2. **Message Length**: Limited to 2000 characters
3. **Entity Recognition**: 500 character limit for processing
4. **No Authentication**: Add before production deployment
5. **No WebSocket**: Using polling, consider WebSocket for real-time

## Future Enhancements

- WebSocket support for real-time chat
- User authentication and sessions
- Message persistence in database
- Multi-language entity recognition
- Voice input/output support
- Mobile responsive design