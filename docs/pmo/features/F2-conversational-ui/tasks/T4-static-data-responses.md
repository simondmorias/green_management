# T4 - Conversational UI - Static Data Responses

## Document Control
**Feature Name & Link:** [Conversational UI Foundation](../feature.md)
**Plan Name & Link:** [Simplified Plan](../plan-simplified.md)
**Date Created:** August 31, 2025  
**Status:** Not Started

---

## Overview
Enhance the static response service with realistic revenue data responses and improve the keyword matching to provide more meaningful interactions for user testing.

---

## Task Requirements

### Task: Static Data Responses
**Status:** Not Started

**Description:**
Create a comprehensive static response system that provides realistic revenue data responses based on user queries. Implement keyword matching and response templates that simulate real RGM insights.

**Technical Details:**
- Files to create/modify:
  - `gain/apps/api/app/services/static_responses.py` - Enhanced response service
  - `gain/apps/api/app/data/sample_data.py` - Sample revenue data
  - `gain/apps/api/app/utils/keyword_matcher.py` - Keyword matching logic
  - `gain/apps/api/tests/test_static_responses.py` - Unit tests

**Key Implementation Points:**
- Keyword-based response matching (revenue, sales, promotion, pricing, etc.)
- Realistic sample data for CPG revenue scenarios
- Response templates with data + explanatory text
- Context-aware responses based on previous queries
- Fallback responses for unrecognized queries
- Sample data includes: revenue figures, growth rates, product performance, promotion ROI

**Sample Response Categories:**
1. **Revenue Queries** ("revenue", "sales performance")
   - Total revenue figures with growth rates
   - Period-over-period comparisons
   - Top performing products/regions

2. **Promotion Queries** ("promotion", "promo", "campaign")
   - Promotion ROI analysis
   - Campaign performance metrics
   - Recommended optimization actions

3. **Pricing Queries** ("price", "pricing", "competitor")
   - Price positioning analysis
   - Competitive pricing comparison
   - Price elasticity insights

4. **Product Queries** ("product", "brand", "category")
   - Product performance breakdown
   - Market share analysis
   - Growth opportunities

5. **Default/Help** (unrecognized queries)
   - Helpful guidance on available queries
   - Example questions users can ask

**Response Format:**
```json
{
  "response": "Here's your Q3 revenue performance...",
  "data": {
    "revenue": 1250000,
    "growth": 0.15,
    "period": "Q3-2025"
  },
  "suggestions": [
    "Show me promotion performance",
    "How are my top products doing?"
  ]
}
```

**Acceptance Criteria:**
- [x] Keyword matching works for all major query types
- [x] Responses include realistic revenue data
- [x] Each response includes explanatory text + data
- [x] Fallback responses guide users to valid queries
- [x] Response suggestions help continue conversation
- [x] Sample data reflects typical CPG scenarios
- [x] Unit tests cover all response categories
- [x] Response time under 100ms for all queries

## Developer Completion Details

*To be completed by the developer at the end of the task implementation*

### Metadata

- Repo Name: Gain
- Branch Name: [Branch Name]

### Summary of Changes

**Overview**
*Write 500 words to summarise what was done and any issues discovered*

**Testing Notes:**
- Test all keyword categories
- Verify realistic data in responses
- Test fallback for unrecognized queries
- Verify response format consistency

**Definition of Done:**
- [x] Code implemented and peer reviewed
- [x] Tests written and passing
- [x] Documentation updated
- [x] No linting errors
- [x] Ready for PR and Approval