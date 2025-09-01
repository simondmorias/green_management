# Backend Architecture (apps/api)

## Technology Stack

### Core Framework
- **FastAPI**: Modern, fast web framework for building APIs
- **Python 3.9+**: Programming language
- **Uvicorn**: ASGI server implementation
- **Pydantic v2**: Data validation using Python type annotations

### Data Processing
- **pyahocorasick**: High-performance Aho-Corasick automaton
- **rapidfuzz**: Fuzzy string matching (optional)
- **numpy**: Numerical computations

### Infrastructure
- **Redis**: Caching layer (optional)
- **APScheduler**: Task scheduling
- **python-dotenv**: Environment variable management

### Development & Testing
- **pytest**: Testing framework
- **httpx**: Async HTTP client for testing
- **ruff**: Python linter and formatter
- **mypy**: Static type checker

## Project Structure

```
apps/api/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry point
│   │
│   ├── routes/                 # API endpoints
│   │   ├── __init__.py
│   │   ├── admin.py           # Admin endpoints
│   │   └── chat.py            # Chat & entity recognition endpoints
│   │
│   ├── services/              # Business logic layer
│   │   ├── __init__.py
│   │   ├── entity_recognizer.py      # Main recognition service
│   │   ├── aho_corasick_matcher.py   # Pattern matching algorithm
│   │   ├── fuzzy_matcher.py          # Fuzzy matching service
│   │   ├── artifact_loader.py        # Entity data loader
│   │   ├── cache_manager.py          # Redis cache management
│   │   ├── scheduler.py              # Task scheduling
│   │   └── static_responses.py       # Static response handler
│   │
│   ├── schemas/               # Pydantic models
│   │   └── entity_recognition.py     # Request/response models
│   │
│   ├── utils/                 # Utility functions
│   │   ├── __init__.py
│   │   └── keyword_matcher.py        # Keyword extraction
│   │
│   └── data/                  # Static data files
│       ├── artifacts/         # Entity recognition data
│       │   ├── gazetteer.json        # Entity dictionary
│       │   └── aliases.jsonl         # Entity aliases
│       └── sample_data.py            # Sample data for testing
│
├── backups/                   # Artifact backup storage
│   └── artifacts/
│       └── update_*/          # Timestamped backups
│
├── tests/                     # Test suite
│   ├── __init__.py
│   ├── test_chat.py
│   ├── test_entity_recognition.py
│   ├── test_cache_manager.py
│   ├── test_artifact_loader.py
│   ├── test_fuzzy_matching.py
│   ├── test_static_responses.py
│   └── performance/
│       ├── locustfile.py
│       └── test_entity_recognition_load.py
│
├── Makefile                   # Development commands
├── pyproject.toml            # Python project configuration
└── .env.example              # Environment variables template
```

## Core Components

### 1. FastAPI Application (main.py)

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(
    title="Gain API",
    description="AI Assistant Platform API",
    version="0.1.0"
)

# CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# Include routers
app.include_router(chat_router, prefix="/api/chat")
app.include_router(admin_router, prefix="/api/admin")
```

### 2. Entity Recognition Service

**Main Service (entity_recognizer.py)**
```python
class EntityRecognitionService:
    def __init__(self):
        self.matcher: EntityMatcher
        self.fuzzy_matcher: FuzzyMatcher
        self.cache_manager: CacheManager
        self.artifact_loader: ArtifactLoader
    
    def recognize(self, text: str, options: dict) -> dict:
        # 1. Exact matching with Aho-Corasick
        # 2. Optional fuzzy matching
        # 3. Overlap resolution
        # 4. Return tagged text and entities
```

**Aho-Corasick Matcher**
```python
class AhoCorasickMatcher:
    def find_all(self, text: str) -> list[dict]:
        # Uses pyahocorasick for O(n) pattern matching
        # Handles word boundaries
        # Returns all matches with positions
    
    def _resolve_overlaps(self, matches: list) -> list:
        # Prefers longest matches
        # Uses entity type priority as tiebreaker
```

### 3. API Routes

**Chat Endpoints (/api/chat/)**
```python
@router.post("/")
async def chat(request: ChatRequest) -> ChatResponse:
    """Main chat endpoint"""
    
@router.post("/recognize-entities")
async def recognize_entities(
    request: EntityRecognitionRequest
) -> EntityRecognitionResponse:
    """Entity recognition endpoint"""
```

**Admin Endpoints (/api/admin/)**
```python
@router.post("/artifacts/update")
async def update_artifacts(
    request: ArtifactUpdateRequest,
    background_tasks: BackgroundTasks
) -> ArtifactUpdateResponse:
    """Trigger artifact update from Databricks"""

@router.get("/health")
async def health_check() -> HealthResponse:
    """System health check"""
```

## Data Models (Pydantic Schemas)

### Request Models
```python
class EntityRecognitionRequest(BaseModel):
    text: str = Field(..., max_length=500)
    options: Optional[EntityRecognitionOptions] = None

class EntityRecognitionOptions(BaseModel):
    fuzzy_matching: bool = False
    confidence_threshold: float = 0.8
    max_results: int = 100
```

### Response Models
```python
class EntityRecognitionResponse(BaseModel):
    tagged_text: str
    entities: List[RecognizedEntity]
    processing_time_ms: float
    suggestions: Optional[List[str]] = None
    error: Optional[str] = None

class RecognizedEntity(BaseModel):
    text: str
    type: EntityType
    start: int
    end: int
    confidence: float
    id: Optional[str] = None
    metadata: EntityMetadata
```

## Service Layer Architecture

### Singleton Pattern
Services use singleton pattern for efficiency:

```python
_service_instance: Optional[EntityRecognitionService] = None

def get_entity_recognition_service() -> EntityRecognitionService:
    global _service_instance
    if _service_instance is None:
        _service_instance = EntityRecognitionService()
    return _service_instance
```

### Dependency Injection
FastAPI's dependency injection for clean architecture:

```python
@router.post("/recognize")
async def recognize(
    request: Request,
    service: EntityRecognitionService = Depends(get_entity_recognition_service)
):
    return service.recognize(request.text)
```

## Caching Strategy

### Redis Cache Manager
```python
class CacheManager:
    def __init__(self):
        self.redis_client = redis.Redis(
            host=REDIS_HOST,
            port=REDIS_PORT,
            decode_responses=True
        )
    
    def get_cached_artifacts(self) -> Optional[dict]:
        # Check Redis for cached artifacts
        # Return None if not found or expired
    
    def cache_artifacts(self, artifacts: dict, ttl: int = 86400):
        # Store artifacts with 24-hour TTL
```

### Cache Keys
- `entity:gazetteer` - Entity dictionary
- `entity:aliases` - Entity aliases
- `entity:recognition:{hash}` - Cached recognition results

## Performance Optimizations

### 1. Aho-Corasick Algorithm
- O(n) time complexity for pattern matching
- Builds automaton once, reuses for all searches
- Efficient for thousands of patterns

### 2. Caching Layers
```python
# Three-tier caching:
1. LRU cache for recognition results (in-memory)
2. Redis cache for artifacts (distributed)
3. File system cache (persistent backup)
```

### 3. Async Processing
```python
async def process_batch(texts: List[str]):
    tasks = [recognize_async(text) for text in texts]
    results = await asyncio.gather(*tasks)
    return results
```

### 4. Response Streaming
For large responses, use streaming:
```python
from fastapi.responses import StreamingResponse

async def stream_results():
    for chunk in process_large_data():
        yield json.dumps(chunk) + "\n"

@router.get("/stream")
async def get_stream():
    return StreamingResponse(stream_results())
```

## Error Handling

### Global Exception Handler
```python
@app.exception_handler(ValidationError)
async def validation_exception_handler(request, exc):
    return JSONResponse(
        status_code=422,
        content={"detail": exc.errors()}
    )

@app.exception_handler(Exception)
async def general_exception_handler(request, exc):
    logger.error(f"Unhandled exception: {exc}")
    return JSONResponse(
        status_code=500,
        content={"detail": "Internal server error"}
    )
```

### Service-Level Error Handling
```python
try:
    result = matcher.recognize_entities(text)
except Exception as e:
    logger.error(f"Recognition failed: {e}")
    return {
        "entities": [],
        "error": str(e),
        "fallback": True
    }
```

## Logging Configuration

```python
import logging
from logging.handlers import RotatingFileHandler

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        RotatingFileHandler(
            "app.log",
            maxBytes=10485760,  # 10MB
            backupCount=5
        ),
        logging.StreamHandler()
    ]
)
```

## Testing Strategy

### Unit Tests
```python
# test_entity_recognition.py
def test_recognize_entities():
    service = EntityRecognitionService()
    result = service.recognize("revenue for Q1 2025")
    
    assert len(result["entities"]) == 2
    assert result["entities"][0]["type"] == "metric"
    assert result["entities"][1]["type"] == "time_period"
```

### Integration Tests
```python
# test_chat.py
async def test_chat_endpoint():
    async with AsyncClient(app=app) as client:
        response = await client.post(
            "/api/chat",
            json={"message": "Hello"}
        )
        assert response.status_code == 200
        assert "response" in response.json()
```

### Performance Tests
```python
# performance/locustfile.py
class EntityRecognitionUser(HttpUser):
    @task
    def recognize_entities(self):
        self.client.post(
            "/api/chat/recognize-entities",
            json={"text": "sample text"}
        )
```

## Configuration Management

### Environment Variables
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # API Configuration
    api_host: str = "0.0.0.0"
    api_port: int = 8000
    
    # Redis Configuration
    redis_host: str = "localhost"
    redis_port: int = 6379
    redis_ttl: int = 86400
    
    # Databricks Configuration
    databricks_host: str
    databricks_http_path: str
    databricks_token: str
    
    class Config:
        env_file = ".env"

settings = Settings()
```

## Deployment Considerations

### Production Server
```bash
# Production deployment with Gunicorn
gunicorn app.main:app \
    --workers 4 \
    --worker-class uvicorn.workers.UvicornWorker \
    --bind 0.0.0.0:8000 \
    --log-level info
```

### Health Checks
```python
@router.get("/health")
async def health_check():
    checks = {
        "api": "healthy",
        "redis": check_redis_connection(),
        "artifacts": check_artifacts_loaded(),
    }
    
    status = "healthy" if all(
        v == "healthy" for v in checks.values()
    ) else "degraded"
    
    return {
        "status": status,
        "checks": checks,
        "timestamp": datetime.utcnow()
    }
```

### Monitoring & Metrics
```python
from prometheus_client import Counter, Histogram, generate_latest

# Metrics
request_count = Counter(
    'api_requests_total',
    'Total API requests',
    ['method', 'endpoint', 'status']
)

request_duration = Histogram(
    'api_request_duration_seconds',
    'API request duration',
    ['method', 'endpoint']
)

@app.get("/metrics")
async def metrics():
    return Response(generate_latest())
```

## Security Considerations

### Input Validation
- Pydantic models validate all inputs
- Max length constraints on text fields
- Rate limiting on endpoints

### Authentication (Future)
```python
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

security = HTTPBearer()

@router.post("/protected")
async def protected_route(
    credentials: HTTPAuthorizationCredentials = Depends(security)
):
    # Verify JWT token
    payload = verify_token(credentials.credentials)
    return {"user": payload["sub"]}
```

### CORS Configuration
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["GET", "POST"],
    allow_headers=["*"],
)
```

## Performance Targets

- **Entity Recognition**: < 100ms for 500 characters
- **API Response Time**: < 200ms p95
- **Throughput**: > 1000 requests/second
- **Memory Usage**: < 500MB per worker
- **Cache Hit Rate**: > 80%

## Scaling Strategies

### Horizontal Scaling
- Multiple API workers behind load balancer
- Redis cluster for distributed caching
- Database read replicas

### Vertical Scaling
- Optimize Aho-Corasick automaton construction
- Use memory-mapped files for large artifacts
- Implement connection pooling

### Caching Strategy
- LRU cache for hot data
- Redis for distributed cache
- CDN for static artifacts