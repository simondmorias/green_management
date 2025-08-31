# Live Intent Highlighting for Conversational Interface

## Document Control
**Product Name:** Live Intent Highlighting System  
**Date:** August 31, 2025  

---

## Executive Summary

This feature enhances the Gain platform's conversational interface with real-time intent highlighting capabilities. As users type natural language queries, the system identifies and visually highlights key entities (manufacturers, metrics, time periods) as interactive pills, providing immediate visual feedback and improving query understanding. This capability transforms the chat experience by making entity recognition transparent and interactive, while enabling more accurate query processing through deterministic entity matching.

The feature leverages pre-built artifacts generated from the data warehouse schema to provide sub-second entity recognition, ensuring a responsive and intuitive user experience that reinforces Gain's commitment to making complex revenue data accessible through natural conversation.

---

## Business Context

### Problem Statement

Current conversational interfaces in the CPG analytics space face several challenges:
- **Ambiguous Intent:** Users are uncertain if their queries will be understood correctly
- **Lack of Visual Feedback:** No immediate indication of recognized entities or concepts
- **Query Refinement Friction:** Users must submit queries to discover if entities are recognized
- **Entity Resolution Errors:** Mismatched product names, brands, or metrics lead to incorrect results
- **Cognitive Load:** Users must remember exact entity names without assistance

### Business Objectives

- [ ] Provide real-time visual feedback on entity recognition during query composition
- [ ] Reduce query errors through transparent entity matching
- [ ] Improve user confidence in the conversational interface
- [ ] Enable faster query refinement through interactive entity pills
- [ ] Establish foundation for advanced query assistance and auto-completion

### Success Metrics

- **Primary KPI:** Entity recognition latency < 100ms after typing stops
- **Secondary KPIs:** 
  - Entity recognition accuracy > 95% for exact matches
  - Query success rate improvement by 30%
  - Reduction in query reformulation attempts by 40%
- **Qualitative Measures:** 
  - User confidence in query composition
  - Perceived system intelligence
  - Reduction in support requests for entity naming

---

## User Requirements

### Target Users

**Primary Users:**
- Role: RGM Leaders & Commercial Managers
- Key Needs: Quick, accurate queries about specific products, brands, and metrics
- Current Pain Points: Uncertainty about exact product names, metric definitions, time period formats

**Secondary Users:**
- Role: Sales Directors & Account Managers
- Key Needs: Fast data access during client meetings, accurate competitor references
- Current Pain Points: Time wasted on query reformulation, incorrect entity matching

### User Stories

1. **Story ID: US1 - Real-time Entity Recognition**
   - **As a** Commercial Manager
   - **I want to** see entities highlighted as I type my question
   - **So that** I know the system understands my intent before submitting
   - **Acceptance Criteria:**
     - [ ] Entities highlight within 500ms of typing pause
     - [ ] Different entity types have distinct visual treatments
     - [ ] Highlighting preserves original query text
     - [ ] Multiple entities can be highlighted simultaneously

2. **Story ID: US2 - Entity Type Differentiation**
   - **As an** RGM Leader
   - **I want to** visually distinguish between manufacturers, metrics, and time periods
   - **So that** I can verify the system correctly categorizes my entities
   - **Acceptance Criteria:**
     - [ ] Manufacturers display with brand-specific coloring
     - [ ] Metrics show with metric-type indicators
     - [ ] Time periods have calendar-based visual cues
     - [ ] Color-blind accessible design patterns

3. **Story ID: US3 - Interactive Entity Pills**
   - **As a** Sales Director
   - **I want to** interact with highlighted entities
   - **So that** I can see alternatives or refine my selection
   - **Acceptance Criteria:**
     - [ ] Clicking entity pills shows alternatives/synonyms
     - [ ] Pills can be removed or replaced
     - [ ] Hover states provide entity metadata
     - [ ] Pills are keyboard navigable

4. **Story ID: US4 - Fuzzy Matching Support**
   - **As a** User with imperfect entity knowledge
   - **I want** the system to recognize approximate entity names
   - **So that** I don't need to remember exact spellings
   - **Acceptance Criteria:**
     - [ ] System recognizes common misspellings
     - [ ] Abbreviations are matched to full names
     - [ ] Confidence scores shown for fuzzy matches
     - [ ] Suggestions provided for low-confidence matches

### User Journey

1. **Entry Point:** User clicks into chat input field
2. **Core Flow:** 
   - User begins typing natural language query
   - After 500ms pause, system processes text for entities
   - Recognized entities appear as colored pills inline
   - User continues typing or interacts with pills
   - User submits query with confidence in entity recognition
3. **Decision Points:** 
   - Accept highlighted entities
   - Click pills to see alternatives
   - Modify query based on visual feedback
   - Submit query or continue refinement
4. **Exit Points:** Query submission with highlighted entities preserved

---

## Functional Requirements

### Core Functionality

**FR1: Live Entity Detection**
- **Description:** Real-time entity recognition during text input with debounced processing
- **Input:** User keystrokes in chat input field
- **Processing:** Debounced API call after 500ms typing pause, entity matching against artifacts
- **Output:** Inline entity highlighting with XML-style tags
- **Priority:** Must Have

**FR2: Artifact-based Entity Matching**
- **Description:** Deterministic entity matching using pre-built gazetteer and alias artifacts
- **Input:** User query text
- **Processing:** Aho-Corasick automaton matching with word boundary checks
- **Output:** Matched entities with type, confidence, and position information
- **Priority:** Must Have

**FR3: Visual Entity Pills**
- **Description:** Transform matched entities into interactive visual pills
- **Input:** Entity match data with positions and types
- **Processing:** React component rendering with type-specific styling
- **Output:** Inline pills with hover states and click interactions
- **Priority:** Must Have

**FR4: Fuzzy Matching Layer**
- **Description:** Secondary matching for misspellings and variations
- **Input:** Unmatched query segments
- **Processing:** RapidFuzz similarity matching with confidence thresholds
- **Output:** Suggested entities with confidence scores
- **Priority:** Should Have

**FR5: Entity Artifact Generation**
- **Description:** Automated artifact creation from data warehouse schema
- **Input:** Databricks warehouse metadata
- **Processing:** ETL pipeline generating gazetteer.json and aliases.jsonl
- **Output:** Optimized matching artifacts with entity mappings
- **Priority:** Must Have

**FR6: Performance Optimization**
- **Description:** Sub-100ms entity recognition through caching and indexing
- **Input:** Query text and cached artifacts
- **Processing:** In-memory matching with Redis result caching
- **Output:** Cached responses for repeated queries
- **Priority:** Must Have

### Conversational Interactions

**Example Queries with Highlighting:**
- "Show me market share for `<manufacturer>Thorntons</manufacturer>` for `<time>Q1 2025</time>`"
- "What's the `<metric>revenue growth</metric>` for `<product>Dairy Milk</product>` vs `<product>Galaxy</product>`?"
- "Compare `<metric>promotion effectiveness</metric>` across `<manufacturer>Cadbury</manufacturer>` brands"
- "Analyze `<time>last quarter</time>` `<metric>price elasticity</metric>` for `<category>chocolate bars</category>`"

**Entity Types and Visual Treatment:**
1. **Manufacturers:** Blue pills with brand icon
2. **Products:** Green pills with product category indicator
3. **Metrics:** Purple pills with metric type badge
4. **Time Periods:** Orange pills with calendar icon
5. **Retailers:** Teal pills with store icon
6. **Categories:** Gray pills with hierarchy indicator

### Data Requirements

**Input Data:**
- Source: Databricks Data Warehouse schema and metadata
- Format: SQL tables, JSON metadata
- Frequency: Daily artifact regeneration
- Volume: ~100K entities, ~500K aliases

**Artifact Structure:**

```json
// gazetteer.json
{
  "entities": {
    "manufacturers": ["Cadbury", "Thorntons", "Galaxy", ...],
    "products": ["Dairy Milk", "Roses", "Heroes", ...],
    "metrics": ["revenue", "market_share", "growth_rate", ...],
    "timewords": ["quarter", "month", "YTD", "Q1", ...]
  },
  "special_tokens": ["vs", "compare", "analyze", ...]
}

// aliases.jsonl
{"type": "manufacturer", "name": "Cadbury", "alias": "cadburys", "id": "MFR_001"}
{"type": "manufacturer", "name": "Cadbury", "alias": "cadbury's", "id": "MFR_001"}
{"type": "product", "name": "Dairy Milk", "alias": "dairy milk chocolate", "id": "PRD_101"}
```

**Output Data:**
- API Response with tagged text
- Entity metadata (type, confidence, position)
- Alternative suggestions for fuzzy matches

---

## Technical Architecture

### Component Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Frontend (Next.js)                    │
├─────────────────────────────────────────────────────────┤
│  ChatInput Component                                     │
│  ├── Debounced Input Handler (500ms)                    │
│  ├── Entity Highlighting Renderer                       │
│  └── Pill Component Library                            │
├─────────────────────────────────────────────────────────┤
│                    Backend (FastAPI)                     │
├─────────────────────────────────────────────────────────┤
│  Entity Recognition Service                              │
│  ├── Artifact Loader (startup)                          │
│  ├── Aho-Corasick Matcher                              │
│  ├── Fuzzy Matching Layer (RapidFuzz)                  │
│  └── Response Formatter                                │
├─────────────────────────────────────────────────────────┤
│                  Caching Layer (Redis)                   │
├─────────────────────────────────────────────────────────┤
│  ├── Query Result Cache (15min TTL)                     │
│  └── Artifact Cache (24hr TTL)                         │
├─────────────────────────────────────────────────────────┤
│              Data Pipeline (Python/Databricks)           │
├─────────────────────────────────────────────────────────┤
│  Artifact Generator                                      │
│  ├── Schema Extractor                                   │
│  ├── Alias Generator                                    │
│  └── Index Builder                                      │
└─────────────────────────────────────────────────────────┘
```

### API Contracts

**Entity Recognition Endpoint:**
```
POST /api/chat/recognize-entities
{
  "text": "Show me Cadbury revenue for Q1 2025",
  "options": {
    "fuzzy_matching": true,
    "confidence_threshold": 0.8
  }
}

Response:
{
  "tagged_text": "Show me <manufacturer>Cadbury</manufacturer> <metric>revenue</metric> for <time>Q1 2025</time>",
  "entities": [
    {
      "text": "Cadbury",
      "type": "manufacturer",
      "start": 8,
      "end": 15,
      "confidence": 1.0,
      "id": "MFR_001",
      "metadata": {
        "full_name": "Cadbury UK Limited",
        "parent": "Mondelez International"
      }
    },
    {
      "text": "revenue",
      "type": "metric",
      "start": 16,
      "end": 23,
      "confidence": 1.0,
      "id": "MTR_REV",
      "metadata": {
        "unit": "GBP",
        "aggregation": "sum"
      }
    },
    {
      "text": "Q1 2025",
      "type": "time_period",
      "start": 28,
      "end": 35,
      "confidence": 1.0,
      "normalized": "2025-Q1",
      "metadata": {
        "start_date": "2025-01-01",
        "end_date": "2025-03-31"
      }
    }
  ],
  "suggestions": [],
  "processing_time_ms": 45
}
```

**Artifact Update Endpoint:**
```
POST /api/admin/update-artifacts
{
  "source": "databricks",
  "force_refresh": true
}

Response:
{
  "status": "success",
  "artifacts_updated": {
    "gazetteer": {
      "entities": 15234,
      "size_kb": 2456
    },
    "aliases": {
      "mappings": 45678,
      "size_kb": 5678
    }
  },
  "generation_time_ms": 3456,
  "next_update": "2025-09-01T00:00:00Z"
}
```

### Performance Requirements

**Latency Targets:**
- Entity recognition: < 100ms (P95)
- Fuzzy matching: < 200ms (P95)
- UI highlighting update: < 50ms after API response
- Artifact loading (startup): < 5 seconds

**Scalability:**
- Support 1000 concurrent users
- Handle 100 requests/second
- Artifact size up to 50MB
- Cache hit ratio > 80%

**Optimization Strategies:**
1. **Aho-Corasick Automaton:** O(n) complexity for exact matching
2. **Word Boundary Optimization:** Reduce false positives
3. **Stopword Guards:** Skip common words
4. **Result Caching:** 15-minute TTL for identical queries
5. **Connection Pooling:** Reuse database connections
6. **Lazy Loading:** Load artifacts on first request

---

## Business Rules & Constraints

### Business Logic

1. **Entity Priority Rule:** When overlapping entities detected, prioritize by type: Manufacturer > Product > Metric > Time
2. **Confidence Threshold:** Only highlight entities with >80% confidence for fuzzy matches
3. **Context Awareness:** Consider surrounding words for disambiguation (e.g., "Cadbury share" → market_share metric)
4. **Alias Resolution:** Always resolve to canonical entity name for backend processing
5. **Time Period Normalization:** Convert relative times ("last quarter") to absolute dates
6. **Case Insensitivity:** All matching should be case-insensitive
7. **Boundary Detection:** Respect word boundaries to avoid partial matches

### Data Governance

1. **Artifact Freshness:** Regenerate artifacts daily at 2 AM UTC
2. **Entity Validation:** Only include entities with valid IDs in source systems
3. **Alias Deduplication:** Remove duplicate aliases pointing to same entity
4. **Performance Monitoring:** Log queries taking >200ms for optimization

---

## User Experience Requirements

### Interface Design

**Visual Design Specifications:**

**Entity Pill Styling:**
```css
/* Base pill style */
.entity-pill {
  display: inline-flex;
  padding: 2px 8px;
  border-radius: 12px;
  font-weight: 500;
  transition: all 0.2s ease;
  cursor: pointer;
}

/* Type-specific colors */
.entity-manufacturer {
  background: linear-gradient(135deg, #1FC1F0, #1821ED);
  color: white;
}

.entity-product {
  background: linear-gradient(135deg, #10B981, #059669);
  color: white;
}

.entity-metric {
  background: linear-gradient(135deg, #A034F9, #7C3AED);
  color: white;
}

.entity-time {
  background: linear-gradient(135deg, #FF8E00, #F59E0B);
  color: white;
}

/* Hover state */
.entity-pill:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

/* Low confidence indicator */
.entity-uncertain {
  border: 2px dashed currentColor;
  background: transparent;
}
```

**Input Field Behavior:**
- Preserve cursor position during highlighting
- Maintain focus during entity processing
- Show subtle loading indicator during API calls
- Support keyboard navigation between pills

### Interaction Patterns

- **Input Methods:** 
  - Natural language typing with auto-highlighting
  - Click pills to see alternatives
  - Tab key to navigate between entities
  - Delete key to remove pills
  
- **Feedback Mechanisms:** 
  - Subtle pulse animation during processing
  - Confidence indicators for fuzzy matches
  - Tooltip on hover showing entity details
  - Success checkmark when entity confirmed
  
- **Error Handling:** 
  - Graceful degradation if entity service unavailable
  - Show plain text if highlighting fails
  - Indicate low-confidence matches with dashed borders
  - Provide manual entity selection fallback

### Accessibility Requirements

- **Screen Reader Support:** Announce entity recognition to screen readers
- **Keyboard Navigation:** Full keyboard support for pill interaction
- **Color Contrast:** WCAG AA compliant color combinations
- **Focus Indicators:** Clear focus states for keyboard users
- **Alternative Text:** Descriptive labels for entity types

---

## Implementation Phases

### Phase 1: Core Infrastructure (Week 1-2)
- [ ] Set up artifact generation pipeline
- [ ] Implement Aho-Corasick matcher
- [ ] Create basic API endpoint
- [ ] Build artifact loader with caching

### Phase 2: Frontend Integration (Week 2-3)
- [ ] Implement debounced input handler
- [ ] Create pill component library
- [ ] Integrate API calls
- [ ] Add basic highlighting renderer

### Phase 3: Fuzzy Matching (Week 3-4)
- [ ] Integrate RapidFuzz library
- [ ] Implement confidence scoring
- [ ] Add suggestion UI
- [ ] Create fallback mechanisms

### Phase 4: Performance Optimization (Week 4-5)
- [ ] Implement Redis caching
- [ ] Optimize artifact structure
- [ ] Add connection pooling
- [ ] Performance testing and tuning

### Phase 5: Polish & Testing (Week 5-6)
- [ ] Comprehensive testing
- [ ] Accessibility audit
- [ ] Documentation
- [ ] User acceptance testing

---

## Testing Strategy

### Unit Testing
- Entity matcher accuracy tests
- Artifact generation validation
- API response format tests
- Component rendering tests

### Integration Testing
- End-to-end highlighting flow
- API latency under load
- Cache invalidation scenarios
- Artifact update process

### Performance Testing
- Load testing with 1000 concurrent users
- Latency benchmarking for various query lengths
- Memory usage profiling
- Cache hit ratio analysis

### User Testing
- A/B testing with/without highlighting
- Usability testing with target personas
- Accessibility testing with screen readers
- Mobile device testing

---

## Release Criteria

### Definition of Done
- [ ] Entity recognition < 100ms for 95% of queries
- [ ] All entity types properly highlighted
- [ ] Fuzzy matching with >80% accuracy
- [ ] Artifact generation pipeline automated
- [ ] Redis caching implemented
- [ ] Accessibility WCAG AA compliant
- [ ] Documentation complete
- [ ] Performance benchmarks met
- [ ] User testing completed

### Launch Readiness
- [ ] Production artifacts generated
- [ ] Monitoring dashboards configured
- [ ] Error tracking enabled
- [ ] Feature flags configured
- [ ] Rollback plan documented
- [ ] Support team trained
- [ ] Performance baseline established

---

## Risk Assessment

### Technical Risks
1. **Risk:** Artifact size grows beyond memory limits
   - **Mitigation:** Implement artifact sharding and lazy loading
   
2. **Risk:** Latency spikes during high load
   - **Mitigation:** Horizontal scaling and aggressive caching
   
3. **Risk:** Entity ambiguity causes incorrect matches
   - **Mitigation:** Context-aware matching and user confirmation

### Business Risks
1. **Risk:** Users find highlighting distracting
   - **Mitigation:** Provide toggle to disable feature
   
2. **Risk:** Incomplete entity coverage
   - **Mitigation:** Continuous artifact improvement process

---

## Appendices

### A. Glossary
- **Aho-Corasick:** Efficient string matching algorithm for multiple patterns
- **Gazetteer:** Dictionary of known entities and their types
- **Entity Recognition:** Process of identifying and classifying named entities in text
- **Fuzzy Matching:** Approximate string matching allowing for variations
- **Debouncing:** Technique to limit function execution rate
- **Pills:** UI components showing tagged/categorized items
- **RapidFuzz:** High-performance fuzzy string matching library
- **TTL:** Time To Live - cache expiration period

### B. Performance Benchmarks

**Target Metrics:**
```
Entity Recognition Latency:
- P50: < 50ms
- P95: < 100ms
- P99: < 200ms

Throughput:
- Requests/second: 100
- Concurrent users: 1000

Cache Performance:
- Hit ratio: > 80%
- Miss penalty: < 150ms

Artifact Size:
- Gazetteer: < 10MB
- Aliases: < 20MB
- Index: < 5MB
```

### C. Sample Entity Mappings

```json
{
  "manufacturers": {
    "Cadbury": ["cadburys", "cadbury's", "cadbury uk"],
    "Thorntons": ["thornton", "thorntons chocolates"],
    "Galaxy": ["galaxy chocolate", "mars galaxy"]
  },
  "metrics": {
    "revenue": ["sales", "turnover", "income"],
    "market_share": ["share", "market share", "share of market"],
    "growth": ["growth rate", "increase", "yoy growth"]
  },
  "time_periods": {
    "Q1": ["first quarter", "quarter 1", "jan-mar"],
    "YTD": ["year to date", "ytd", "this year so far"],
    "LTM": ["last twelve months", "trailing twelve months", "ttm"]
  }
}
```