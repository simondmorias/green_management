# T1 - Conversational UI - Backend API Setup

## Document Control
**Feature Name & Link:** [Conversational UI Foundation](../feature.md)
**Plan Name & Link:** [Simplified Plan](../plan-simplified.md)
**Date Created:** August 31, 2025  
**Status:** Not Started

---

## Overview
Create the basic FastAPI backend structure following the gain repo layout with a single chat endpoint that returns static responses for the conversational interface MVP.

---

## Task Requirements

### Task: Backend API Setup
**Status:** Not Started

**Description:**
Set up the basic FastAPI application structure in the gain repository following the documented repo layout. Create a single chat endpoint that accepts user messages and returns static responses based on simple keyword matching.

**Technical Details:**
- Files to create/modify:
  - `gain/apps/api/app/main.py` - FastAPI app initialization
  - `gain/apps/api/app/routes/chat.py` - Chat endpoint
  - `gain/apps/api/app/services/static_responses.py` - Static response service
  - `gain/apps/api/pyproject.toml` - Python dependencies
  - `gain/apps/api/requirements.txt` - Pip requirements

**Key Implementation Points:**
- Follow exact repo layout from `/management/docs/components/gain/repo_layout.md`
- Single POST endpoint: `/api/chat/message`
- Accept JSON: `{"message": "user text"}`
- Return JSON: `{"response": "assistant text", "data": {...}}`
- Static responses for common revenue queries
- Basic input validation (message length, content)
- Simple keyword-based response matching
- No authentication required
- No database connection needed

**Static Response Examples:**
- "revenue" → Revenue summary with sample data
- "sales" → Sales performance data
- "promotion" → Promotion analysis
- Default → Helpful guidance message

**Acceptance Criteria:**
- [x] FastAPI app starts successfully
- [x] POST /api/chat/message endpoint accepts messages
- [x] Returns appropriate static responses based on keywords
- [x] Input validation prevents empty/oversized messages
- [x] CORS enabled for frontend integration
- [x] Basic error handling returns user-friendly messages
- [x] Follows gain repo layout structure exactly

## Developer Completion Details

*To be completed by the developer at the end of the task implementation*

### Metadata

- Repo Name: Gain
- Branch Name: [Branch Name]

### Summary of Changes

**Overview**
*Write 500 words to summarise what was done and any issues discovered*

**Testing Notes:**
- Test endpoint with curl/Postman
- Verify different keyword responses
- Test input validation edge cases

**Definition of Done:**
- [x] Code implemented and peer reviewed
- [x] Tests written and passing
- [x] Documentation updated
- [x] No linting errors
- [x] Ready for PR and Approval