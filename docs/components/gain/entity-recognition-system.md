# Entity Recognition System

## Overview

The entity recognition system is a core feature of the Gain platform that provides real-time identification and highlighting of business entities (brands, products, metrics, time periods) as users type. It uses the Aho-Corasick algorithm for efficient pattern matching and supports fuzzy matching for typo tolerance.

## Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│                 │     │                  │     │                 │
│  Frontend       │────▶│  API Gateway     │────▶│  Recognition    │
│  (React)        │     │  (Next.js)       │     │  Service        │
│                 │     │                  │     │  (FastAPI)      │
└─────────────────┘     └──────────────────┘     └─────────────────┘
                                                           │
                                                           ▼
                                                   ┌─────────────────┐
                                                   │                 │
                                                   │  Aho-Corasick   │
                                                   │  Matcher        │
                                                   │                 │
                                                   └─────────────────┘
                                                           │
                                ┌──────────────────────────┼──────────────────────────┐
                                │                          │                          │
                                ▼                          ▼                          ▼
                        ┌─────────────┐           ┌─────────────┐           ┌─────────────┐
                        │   Redis     │           │   File      │           │  Databricks │
                        │   Cache     │           │   System    │           │  Warehouse  │
                        └─────────────┘           └─────────────┘           └─────────────┘
```

## Components

### 1. Frontend Integration

**useEntityRecognition Hook**
```typescript
const useEntityRecognition = (options: {
  enabled?: boolean;
  debounceMs?: number;
}) => {
  // Debounced API calls
  // State management for entities
  // Error handling
  // Performance tracking
};
```

**Key Features:**
- 500ms debounce to reduce API calls
- Real-time entity highlighting
- Tooltip display with entity type
- Performance metrics tracking

### 2. Entity Recognition Service

**Core Service (entity_recognizer.py)**
```python
class EntityRecognitionService:
    def recognize(self, text: str, options: dict) -> dict:
        """
        Main recognition pipeline:
        1. Input validation (max 500 chars)
        2. Exact pattern matching
        3. Optional fuzzy matching
        4. Overlap resolution
        5. XML tagging generation
        """
```

**Processing Pipeline:**
1. **Input Validation**: Ensure text length < 500 characters
2. **Exact Matching**: Use Aho-Corasick for O(n) pattern matching
3. **Fuzzy Matching**: Optional fuzzy matching for typos
4. **Overlap Resolution**: Prefer longest matches
5. **Output Generation**: Create tagged text and entity list

### 3. Aho-Corasick Implementation

**Algorithm Choice:**
- Using `pyahocorasick` library for reliability
- O(n + m + z) complexity where:
  - n = text length
  - m = total pattern length
  - z = number of matches

**Pattern Matching Logic:**
```python
class AhoCorasickMatcher:
    def __init__(self):
        self.automaton = ahocorasick.Automaton()
        
    def add_pattern(self, pattern: str, metadata: dict):
        # Add pattern to automaton
        # Store metadata for retrieval
        
    def find_all(self, text: str) -> list[dict]:
        # Find all matches in text
        # Check word boundaries
        # Return matches with positions
```

**Overlap Resolution:**
```python
def _resolve_overlaps(self, matches: list[dict]) -> list[dict]:
    # Sort by match length (descending)
    # Process longest matches first
    # Track used positions
    # Return non-overlapping matches
```

### 4. Entity Data Structure

**Gazetteer (gazetteer.json)**
```json
{
  "entities": {
    "manufacturers": ["MARS", "NESTLE", "FERRERO"],
    "brands": ["Dairy Milk", "KitKat", "Snickers"],
    "products": ["CHOCOLATE BARS", "BOXED CHOCOLATES"],
    "categories": ["CONFECTIONERY"]
  },
  "metrics": ["revenue", "sales", "market share"],
  "timewords": ["Q1", "Q2", "2024 Q1", "2025 Q1"],
  "special_tokens": ["vs", "compare", "analyze"]
}
```

**Aliases (aliases.jsonl)**
```jsonl
{"type": "brand", "name": "Dairy Milk", "alias": "dairy milk", "confidence": 1.0}
{"type": "brand", "name": "Dairy Milk", "alias": "dairymilk", "confidence": 0.9}
{"type": "brand", "name": "KitKat", "alias": "kit kat", "confidence": 0.95}
```

## Data Generation Pipeline

### Databricks Integration
```python
# devtools/generate_entity_artifacts.py
class EntityArtifactGenerator:
    def extract_entities(self):
        # Connect to Databricks SQL Warehouse
        # Query master_product_hierarchy table
        # Categorize by entity type
        
    def generate_aliases(self, entities):
        # Create lowercase variations
        # Add plural forms
        # Generate abbreviations
        # Handle special characters
```

### Alias Generation Rules
1. **Lowercase**: All entity names → lowercase
2. **Plurals**: Add 's' and "'s" variations
3. **Spaces**: Replace with underscores/hyphens
4. **Abbreviations**: Common abbreviations (e.g., "manufacturing" → "mfg")
5. **Prefixes**: First 3 characters for long names

## Performance Characteristics

### Time Complexity
- **Pattern Matching**: O(n) where n = text length
- **Overlap Resolution**: O(m log m) where m = number of matches
- **Total**: O(n + m log m)

### Space Complexity
- **Automaton**: O(p) where p = total pattern characters
- **Cache**: O(c) where c = cached results
- **Total**: O(p + c)

### Performance Targets
- **Response Time**: < 100ms for 500 characters
- **Throughput**: > 1000 requests/second
- **Cache Hit Rate**: > 80%
- **Memory Usage**: < 100MB for automaton

## Caching Strategy

### Three-Tier Cache
1. **LRU Cache** (In-Memory)
   - Python's @lru_cache decorator
   - 1000 entry limit
   - Instant access

2. **Redis Cache** (Distributed)
   - 24-hour TTL
   - Shared across workers
   - Graceful fallback

3. **File System** (Persistent)
   - JSON/JSONL files
   - Always available
   - Source of truth

### Cache Keys
```python
# Recognition results
f"entity:recognition:{hash(text)}"

# Artifacts
"entity:gazetteer"
"entity:aliases"
```

## Entity Types

### Business Entities
- **manufacturer**: Company that produces products (e.g., "Mars", "Nestle")
- **brand**: Product brand name (e.g., "KitKat", "Snickers")
- **product**: Product category (e.g., "Chocolate Bars")
- **category**: High-level category (e.g., "Confectionery")

### Analytical Entities
- **metric**: Business metrics (e.g., "revenue", "market share")
- **time_period**: Temporal references (e.g., "Q1", "2025 Q1", "YTD")
- **special**: Analysis keywords (e.g., "compare", "versus")

## Recognition Rules

### Word Boundary Detection
```python
def _is_word_boundary(self, text: str, start: int, end: int) -> bool:
    # Check character before start
    if start > 0 and text[start - 1].isalnum():
        return False
    
    # Check character after end
    if end < len(text) and text[end].isalnum():
        return False
    
    return True
```

### Priority Resolution
When matches overlap, priority is:
1. **Length**: Longest match wins
2. **Type**: If same length, use type priority:
   - manufacturer: 4
   - brand: 3
   - product/category: 2
   - metric/time_period: 1
   - special: 0

### Case Sensitivity
- All matching is case-insensitive
- Original case preserved in results
- Display names maintain proper capitalization

## API Interface

### Request Format
```json
POST /api/chat/recognize-entities
{
  "text": "What is the revenue for Dairy Milk in Q1 2025?",
  "options": {
    "fuzzy_matching": false,
    "confidence_threshold": 0.8,
    "max_results": 100
  }
}
```

### Response Format
```json
{
  "tagged_text": "What is the <metric>revenue</metric> for <brand>Dairy Milk</brand> in <time_period>Q1 2025</time_period>?",
  "entities": [
    {
      "text": "revenue",
      "type": "metric",
      "start": 12,
      "end": 19,
      "confidence": 1.0,
      "metadata": {
        "display_name": "revenue"
      }
    },
    {
      "text": "Dairy Milk",
      "type": "brand",
      "start": 24,
      "end": 34,
      "confidence": 1.0,
      "metadata": {
        "display_name": "Dairy Milk"
      }
    },
    {
      "text": "Q1 2025",
      "type": "time_period",
      "start": 38,
      "end": 45,
      "confidence": 1.0,
      "metadata": {
        "display_name": "Q1 2025"
      }
    }
  ],
  "processing_time_ms": 12.5,
  "suggestions": [],
  "error": null
}
```

## Fuzzy Matching (Optional)

### Algorithm
- Uses `rapidfuzz` library
- Levenshtein distance calculation
- Configurable confidence threshold

### Configuration
```python
class FuzzyMatcher:
    def __init__(self, confidence_threshold: float = 0.8):
        self.confidence_threshold = confidence_threshold
        self.max_distance = 2  # Maximum edit distance
```

### Use Cases
- Typo correction ("Dary Milk" → "Dairy Milk")
- Spacing variations ("kitkat" → "Kit Kat")
- Minor misspellings

## Testing Strategy

### Unit Tests
```python
def test_longest_match_preference():
    """Ensure 'Dairy Milk' matches as whole, not just 'Milk'"""
    matcher = AhoCorasickMatcher()
    matcher.add_pattern("Milk", {"type": "product"})
    matcher.add_pattern("Dairy Milk", {"type": "brand"})
    
    matches = matcher.find_all("I love Dairy Milk chocolate")
    assert len(matches) == 1
    assert matches[0]["text"] == "Dairy Milk"
```

### Performance Tests
```python
def test_recognition_performance():
    """Ensure recognition completes within 100ms"""
    service = EntityRecognitionService()
    text = "A" * 500  # Max length text
    
    start = time.time()
    result = service.recognize(text)
    duration = (time.time() - start) * 1000
    
    assert duration < 100  # Must complete within 100ms
```

### Load Tests
```python
# Using Locust for load testing
class EntityRecognitionUser(HttpUser):
    wait_time = between(1, 3)
    
    @task
    def recognize_entities(self):
        self.client.post(
            "/api/chat/recognize-entities",
            json={"text": random.choice(TEST_PHRASES)}
        )
```

## Monitoring & Observability

### Metrics to Track
- **Recognition Latency**: p50, p95, p99
- **Cache Hit Rate**: Redis and LRU cache
- **Entity Count**: Average entities per request
- **Error Rate**: Failed recognitions
- **Throughput**: Requests per second

### Logging
```python
logger.info(f"Recognized {len(entities)} entities in {duration}ms")
logger.warning(f"Text length ({len(text)}) exceeds limit")
logger.error(f"Recognition failed: {error}")
```

### Health Checks
```python
def check_entity_recognition_health():
    # Test with known input
    result = service.recognize("test Mars brand")
    
    return {
        "status": "healthy" if result["entities"] else "degraded",
        "artifacts_loaded": service.artifacts_loaded,
        "cache_available": cache_manager.is_available(),
        "last_update": artifact_loader.last_update_time
    }
```

## Optimization Techniques

### 1. Automaton Reuse
- Build automaton once at startup
- Reuse for all recognition requests
- Rebuild only on artifact updates

### 2. Text Preprocessing
```python
def preprocess_text(text: str) -> str:
    # Remove excessive whitespace
    text = " ".join(text.split())
    # Truncate to max length
    text = text[:500]
    return text
```

### 3. Result Caching
```python
@lru_cache(maxsize=1000)
def cached_recognize(text: str, fuzzy: bool = False):
    # Cache recognition results
    # Key includes text and options
    return service.recognize(text, {"fuzzy_matching": fuzzy})
```

### 4. Batch Processing
```python
async def recognize_batch(texts: List[str]):
    # Process multiple texts efficiently
    tasks = [recognize_async(text) for text in texts]
    return await asyncio.gather(*tasks)
```

## Future Enhancements

1. **Machine Learning Integration**
   - Train custom NER models
   - Context-aware entity recognition
   - Confidence scoring improvement

2. **Multi-language Support**
   - Language detection
   - Language-specific patterns
   - Translation integration

3. **Context Awareness**
   - Use conversation history
   - Domain-specific recognition
   - User preference learning

4. **Performance Improvements**
   - GPU acceleration for ML models
   - WebAssembly for client-side recognition
   - Edge caching with CDN

5. **Advanced Features**
   - Entity relationship detection
   - Sentiment analysis per entity
   - Entity disambiguation