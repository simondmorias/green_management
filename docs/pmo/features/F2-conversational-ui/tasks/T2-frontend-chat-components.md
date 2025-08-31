# T2 - Conversational UI - Frontend Chat Components

## Document Control
**Feature Name & Link:** [Conversational UI Foundation](../feature.md)
**Plan Name & Link:** [Simplified Plan](../plan-simplified.md)
**Date Created:** August 31, 2025  
**Status:** Not Started

---

## Overview
Create the basic React chat interface components for the homepage following the gain repo layout. Implement the design system colors and create a simple message exchange interface.

---

## Task Requirements

### Task: Frontend Chat Components
**Status:** Not Started

**Description:**
Build the core React components for the chat interface including message display, input handling, and basic state management. Apply the specified design system colors and create a desktop-focused layout.

**Technical Details:**
- Files to create/modify:
  - `gain/apps/web/src/components/chat/ChatInterface.tsx` - Main chat container
  - `gain/apps/web/src/components/chat/MessageList.tsx` - Message history display
  - `gain/apps/web/src/components/chat/MessageBubble.tsx` - Individual message component
  - `gain/apps/web/src/components/chat/MessageInput.tsx` - Text input with send button
  - `gain/apps/web/src/pages/index.tsx` - Homepage with chat interface
  - `gain/apps/web/src/styles/globals.css` - Design system colors
  - `gain/apps/web/src/types/chat.ts` - TypeScript interfaces

**Key Implementation Points:**
- Follow exact repo layout from `/management/docs/components/gain/repo_layout.md`
- Implement design system colors:
  - Primary: Spiro Disco Ball (#1FC1F0), Bluebonnet (#1821ED), Purple X11 (#A034F9)
  - Gradient: Spiro Disco Ball → Bluebonnet → Purple X11
  - Background: Black (#000000), Text: White (#FFFFFF)
- Simple React useState for message management
- Desktop-only responsive design (min-width: 1024px)
- Basic message validation before sending
- Loading states during API calls
- Simple error display for failed requests

**Component Structure:**
```
ChatInterface
├── MessageList
│   └── MessageBubble (user/assistant variants)
└── MessageInput
```

**Acceptance Criteria:**
- [x] Homepage displays chat interface prominently
- [x] Users can type and send messages
- [x] Messages display in conversation format (user right, assistant left)
- [x] Design system colors applied correctly
- [x] Gradient used for primary buttons/elements
- [x] Loading indicator shows during API calls
- [x] Basic error messages display for failures
- [x] Desktop layout works properly (1024px+)
- [x] TypeScript interfaces defined for all data structures

## Developer Completion Details

*To be completed by the developer at the end of the task implementation*

### Metadata

- Repo Name: Gain
- Branch Name: [Branch Name]

### Summary of Changes

**Overview**
*Write 500 words to summarise what was done and any issues discovered*

**Testing Notes:**
- Test message sending and display
- Verify design system colors render correctly
- Test loading and error states
- Verify desktop layout responsiveness

**Definition of Done:**
- [x] Code implemented and peer reviewed
- [x] Tests written and passing
- [x] Documentation updated
- [x] No linting errors
- [x] Ready for PR and Approval