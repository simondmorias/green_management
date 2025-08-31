# T3 - Conversational UI - API Integration

## Document Control
**Feature Name & Link:** [Conversational UI Foundation](../feature.md)
**Plan Name & Link:** [Simplified Plan](../plan-simplified.md)
**Date Created:** August 31, 2025  
**Status:** Not Started

---

## Overview
Connect the frontend chat interface to the backend API endpoint, implementing the complete message flow from user input to assistant response display.

---

## Task Requirements

### Task: API Integration
**Status:** Not Started

**Description:**
Implement the API client functionality to connect the React chat interface with the FastAPI backend. Handle the complete request/response cycle including loading states, error handling, and message state management.

**Technical Details:**
- Files to create/modify:
  - `gain/apps/web/src/utils/api.ts` - API client utilities
  - `gain/apps/web/src/hooks/useChat.ts` - Chat functionality hook
  - `gain/apps/web/src/components/chat/ChatInterface.tsx` - Update with API integration
  - `gain/apps/web/src/types/api.ts` - API response types
  - `gain/apps/web/.env.local` - Environment variables

**Key Implementation Points:**
- Create API client for POST /api/chat/message
- Implement useChat hook for state management
- Handle loading states during API calls
- Display error messages for failed requests
- Update message list with API responses
- Basic retry logic for failed requests
- Environment variable for API base URL
- TypeScript interfaces for API requests/responses

**API Integration Flow:**
1. User types message and clicks send
2. Add user message to chat immediately (optimistic update)
3. Show loading indicator
4. Call API endpoint with user message
5. On success: Add assistant response to chat
6. On error: Show error message with retry option
7. Hide loading indicator

**Error Handling:**
- Network errors: "Connection failed. Please try again."
- Server errors: "Something went wrong. Please try again."
- Validation errors: Display specific error message
- Timeout: "Request timed out. Please try again."

**Acceptance Criteria:**
- [x] Frontend successfully calls backend API
- [x] User messages appear immediately in chat
- [x] Loading indicator shows during API calls
- [x] Assistant responses display correctly
- [x] Error messages show for failed requests
- [x] Retry functionality works for failed requests
- [x] API base URL configurable via environment variable
- [x] TypeScript types defined for all API interactions
- [x] Complete end-to-end message flow working

## Developer Completion Details

*To be completed by the developer at the end of the task implementation*

### Metadata

- Repo Name: Gain
- Branch Name: [Branch Name]

### Summary of Changes

**Overview**
*Write 500 words to summarise what was done and any issues discovered*

**Testing Notes:**
- Test successful message exchange
- Test error scenarios (network failure, server error)
- Test retry functionality
- Verify loading states work correctly
- Test with different message types/keywords

**Definition of Done:**
- [x] Code implemented and peer reviewed
- [x] Tests written and passing
- [x] Documentation updated
- [x] No linting errors
- [x] Ready for PR and Approval