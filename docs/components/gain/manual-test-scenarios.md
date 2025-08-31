# Manual Testing Scenarios - Conversational UI MVP

## Overview
This document provides comprehensive manual testing scenarios for the Gain conversational UI MVP. These tests should be performed to validate the complete user experience and ensure all acceptance criteria are met.

## Test Environment Setup

### Prerequisites
- Node.js 18+ installed
- Python 3.9+ installed
- Both frontend and backend services running

### Setup Instructions
1. **Backend Setup:**
   ```bash
   cd apps/api
   python -m venv .venv
   source .venv/bin/activate  # On Windows: .venv\Scripts\activate
   pip install -e .
   uvicorn app.main:app --reload --port 8000
   ```

2. **Frontend Setup:**
   ```bash
   cd apps/web
   pnpm install
   pnpm dev
   ```

3. **Verify Setup:**
   - Backend health check: http://localhost:8000/health
   - Frontend: http://localhost:3000
   - API docs: http://localhost:8000/docs

## Test Scenarios

### 1. Happy Path Testing

#### 1.1 Initial Page Load
**Objective:** Verify the homepage loads correctly with the chat interface

**Steps:**
1. Open browser and navigate to http://localhost:3000
2. Wait for page to fully load

**Expected Results:**
- [ ] Page loads within 3 seconds
- [ ] "Gain" header is visible with gradient styling
- [ ] "AI Assistant Platform" subtitle is displayed
- [ ] Chat interface is visible with "AI Assistant Chat" title
- [ ] "Start a conversation" empty state is shown
- [ ] Message input field is present with placeholder text
- [ ] Send button is visible and enabled
- [ ] Sidebar shows "Welcome to Gain" section
- [ ] Quick tips section is displayed
- [ ] Footer shows platform information

#### 1.2 Basic Message Flow
**Objective:** Test sending and receiving messages

**Steps:**
1. Click in the message input field
2. Type: "What's our revenue this quarter?"
3. Click Send button (or press Enter)
4. Wait for response

**Expected Results:**
- [ ] User message appears immediately in chat
- [ ] User message has blue background and white text
- [ ] Loading indicator appears ("Thinking..." with animation)
- [ ] Assistant response appears within 3 seconds
- [ ] Assistant message has gray background and dark text
- [ ] Response contains revenue-related information
- [ ] Response mentions specific dollar amounts
- [ ] Suggestions appear below the response
- [ ] Loading indicator disappears

#### 1.3 Follow-up Conversation
**Objective:** Test conversation continuity

**Steps:**
1. After receiving revenue response, click on a suggestion or type: "Show me sales breakdown"
2. Send the message
3. Wait for response

**Expected Results:**
- [ ] Second user message appears in chat
- [ ] Both previous messages remain visible
- [ ] New loading indicator appears
- [ ] Sales-related response is received
- [ ] Response is contextually relevant
- [ ] New suggestions are provided
- [ ] Conversation history is maintained

### 2. Keyword Category Testing

#### 2.1 Revenue Queries
**Test Messages:**
- "What's our revenue performance?"
- "Show me quarterly revenue"
- "How are sales doing?"
- "Revenue breakdown please"

**Expected Results for Each:**
- [ ] Response contains "revenue summary" or similar
- [ ] Data includes total_revenue field
- [ ] Growth rate information is provided
- [ ] Top performing categories mentioned
- [ ] Realistic dollar amounts (millions range)
- [ ] Relevant suggestions provided

#### 2.2 Promotion Queries
**Test Messages:**
- "How are our promotions performing?"
- "Show me campaign results"
- "Promotion ROI analysis"
- "Marketing effectiveness"

**Expected Results for Each:**
- [ ] Response contains "promotion analysis" or similar
- [ ] Data includes active_promotions count
- [ ] ROI information is provided
- [ ] Campaign performance details
- [ ] Realistic promotion metrics
- [ ] Marketing-related suggestions

#### 2.3 Pricing Queries
**Test Messages:**
- "What's our pricing strategy?"
- "Competitive pricing analysis"
- "Price optimization opportunities"
- "How do our prices compare?"

**Expected Results for Each:**
- [ ] Response contains "pricing analysis" or similar
- [ ] Data includes price_optimization_opportunities
- [ ] Competitive position information
- [ ] Product pricing details
- [ ] Actionable pricing recommendations
- [ ] Pricing-related suggestions

#### 2.4 Product Queries
**Test Messages:**
- "Which products are top performers?"
- "Product performance analysis"
- "Show me category breakdown"
- "Brand performance review"

**Expected Results for Each:**
- [ ] Response contains "product performance" or similar
- [ ] Data includes total_products count
- [ ] Category performance breakdown
- [ ] Growth opportunities identified
- [ ] Realistic product metrics
- [ ] Product-related suggestions

#### 2.5 Help Queries
**Test Messages:**
- "What can you help me with?"
- "I need assistance"
- "What are my options?"
- "Help me understand"

**Expected Results for Each:**
- [ ] Response contains helpful guidance
- [ ] Available topics are listed
- [ ] Example queries are provided
- [ ] Clear instructions given
- [ ] Encouraging tone used
- [ ] Relevant help suggestions

### 3. Error Handling Testing

#### 3.1 Empty Message Handling
**Steps:**
1. Leave message input empty
2. Click Send button

**Expected Results:**
- [ ] No message is sent
- [ ] No API call is made
- [ ] Input field remains focused
- [ ] No error message displayed

#### 3.2 Whitespace-Only Messages
**Steps:**
1. Type only spaces in message input
2. Click Send button

**Expected Results:**
- [ ] Message is not sent
- [ ] Input is cleared or trimmed
- [ ] No API call is made

#### 3.3 Very Long Messages
**Steps:**
1. Type a message longer than 2000 characters
2. Click Send button

**Expected Results:**
- [ ] Message is sent (client allows it)
- [ ] Server returns validation error
- [ ] Error message is displayed
- [ ] User can retry with shorter message

#### 3.4 Network Failure Simulation
**Steps:**
1. Disconnect internet or stop backend server
2. Send a message
3. Wait for timeout

**Expected Results:**
- [ ] Loading state appears
- [ ] After timeout, error message appears
- [ ] Error mentions connection failure
- [ ] Retry button is available
- [ ] Dismiss button is available
- [ ] Error styling is appropriate (red/warning colors)

#### 3.5 Server Error Simulation
**Steps:**
1. Modify backend to return 500 error (or use network tools)
2. Send a message

**Expected Results:**
- [ ] Error message appears
- [ ] Error mentions server issue
- [ ] Retry functionality available
- [ ] User can continue after error

#### 3.6 Retry Functionality
**Steps:**
1. Trigger any error scenario above
2. Click Retry button
3. Ensure backend is working
4. Wait for response

**Expected Results:**
- [ ] Retry button triggers new request
- [ ] Loading state appears again
- [ ] Original message is resent
- [ ] Success response is received
- [ ] Error state is cleared

### 4. UI/UX Testing

#### 4.1 Design System Colors
**Verification Points:**
- [ ] Header uses gradient text styling
- [ ] Cards have white background with gray borders
- [ ] User messages have blue background (#3B82F6 or similar)
- [ ] Assistant messages have gray background (#F3F4F6 or similar)
- [ ] Error messages use red/warning colors
- [ ] Success states use green colors
- [ ] Loading states use appropriate animation

#### 4.2 Message Bubble Styling
**Verification Points:**
- [ ] User messages align to the right
- [ ] Assistant messages align to the left
- [ ] Messages have rounded corners
- [ ] Proper padding and margins
- [ ] Text is readable with good contrast
- [ ] Timestamps are formatted correctly
- [ ] Message bubbles have appropriate shadows

#### 4.3 Loading States
**Verification Points:**
- [ ] "Thinking..." text appears during loading
- [ ] Loading animation is smooth (pulse effect)
- [ ] Loading indicator is positioned correctly
- [ ] Loading state doesn't interfere with other UI elements
- [ ] Loading state clears when response arrives

#### 4.4 Responsive Design (Desktop)
**Test at Different Screen Sizes:**
- [ ] 1920x1080 (Full HD)
- [ ] 1366x768 (Common laptop)
- [ ] 1024x768 (Minimum desktop)

**Verification Points:**
- [ ] Layout adapts appropriately
- [ ] Text remains readable
- [ ] Chat interface uses available space well
- [ ] Sidebar content is accessible
- [ ] No horizontal scrolling required

#### 4.5 Accessibility Testing
**Verification Points:**
- [ ] Tab navigation works through all interactive elements
- [ ] Message input has proper aria-label
- [ ] Send button is keyboard accessible
- [ ] Screen reader can read messages
- [ ] Color contrast meets WCAG guidelines
- [ ] Focus indicators are visible
- [ ] Heading structure is logical (H1, H2, etc.)

### 5. Performance Testing

#### 5.1 Page Load Performance
**Steps:**
1. Open browser developer tools
2. Navigate to http://localhost:3000
3. Check Network and Performance tabs

**Expected Results:**
- [ ] Initial page load < 2 seconds
- [ ] First Contentful Paint < 1 second
- [ ] Largest Contentful Paint < 2.5 seconds
- [ ] No JavaScript errors in console
- [ ] CSS loads without issues

#### 5.2 API Response Times
**Steps:**
1. Send various types of messages
2. Monitor network requests in developer tools

**Expected Results:**
- [ ] API responses < 3 seconds consistently
- [ ] Average response time < 1 second
- [ ] No failed requests
- [ ] Proper HTTP status codes (200)

#### 5.3 Memory Usage
**Steps:**
1. Open browser task manager
2. Send 20+ messages in conversation
3. Monitor memory usage

**Expected Results:**
- [ ] Memory usage remains stable
- [ ] No significant memory leaks
- [ ] Page remains responsive
- [ ] All messages remain visible

#### 5.4 Concurrent Usage
**Steps:**
1. Open multiple browser tabs
2. Send messages simultaneously from different tabs

**Expected Results:**
- [ ] All requests are handled
- [ ] No interference between tabs
- [ ] Responses are received correctly
- [ ] No server errors

### 6. Data Validation Testing

#### 6.1 Response Data Structure
**For Each Message Type, Verify:**
- [ ] Response contains "response" field (string)
- [ ] Response contains "data" field (object)
- [ ] Response contains "suggestions" field (array)
- [ ] Response contains "metadata" field (object)
- [ ] All fields have appropriate data types
- [ ] No null or undefined values in required fields

#### 6.2 Revenue Data Validation
**Send Revenue Query and Verify:**
- [ ] total_revenue is a positive number
- [ ] growth_rate is a reasonable percentage
- [ ] top_categories is an array of strings
- [ ] breakdown data adds up logically
- [ ] Currency formatting is consistent
- [ ] Time periods are clearly specified

#### 6.3 Realistic Data Values
**Verify Across All Categories:**
- [ ] Dollar amounts are in realistic ranges (millions)
- [ ] Percentages are reasonable (not 1000%+)
- [ ] Product names sound realistic
- [ ] Category names are appropriate for CPG industry
- [ ] Dates and time periods make sense
- [ ] Growth rates are believable

#### 6.4 Suggestion Quality
**For Each Response, Verify:**
- [ ] Suggestions are relevant to the query
- [ ] Suggestions are actionable
- [ ] Suggestions lead to meaningful follow-ups
- [ ] No duplicate suggestions
- [ ] Suggestions are properly formatted
- [ ] Clicking suggestions works (if implemented)

### 7. Cross-Browser Testing

#### 7.1 Chrome Testing
**Version:** Latest stable
**Test All Core Scenarios:**
- [ ] Page loads correctly
- [ ] Chat functionality works
- [ ] Styling appears correct
- [ ] No console errors
- [ ] Performance is acceptable

#### 7.2 Firefox Testing
**Version:** Latest stable
**Test All Core Scenarios:**
- [ ] Page loads correctly
- [ ] Chat functionality works
- [ ] Styling appears correct
- [ ] No console errors
- [ ] Performance is acceptable

#### 7.3 Safari Testing (if on Mac)
**Version:** Latest stable
**Test All Core Scenarios:**
- [ ] Page loads correctly
- [ ] Chat functionality works
- [ ] Styling appears correct
- [ ] No console errors
- [ ] Performance is acceptable

### 8. Edge Cases and Stress Testing

#### 8.1 Special Characters
**Test Messages:**
- "What's our revenue & growth?"
- "Revenue (Q4) performance"
- "Sales data: 2024 analysis"
- "Promotion ROI - how effective?"

**Expected Results:**
- [ ] All messages are handled correctly
- [ ] Special characters don't break parsing
- [ ] Responses are appropriate
- [ ] No encoding issues

#### 8.2 Case Sensitivity
**Test Messages:**
- "REVENUE PERFORMANCE"
- "revenue performance"
- "Revenue Performance"
- "ReVeNuE pErFoRmAnCe"

**Expected Results:**
- [ ] All variations trigger revenue responses
- [ ] Case doesn't affect keyword matching
- [ ] Responses are consistent

#### 8.3 Multiple Keywords
**Test Messages:**
- "Revenue and sales performance"
- "Promotion pricing strategy"
- "Product revenue analysis"

**Expected Results:**
- [ ] System handles multiple keywords appropriately
- [ ] Primary keyword is identified correctly
- [ ] Response addresses the main intent

#### 8.4 Long Conversation
**Steps:**
1. Send 50+ messages in a single session
2. Mix different query types
3. Monitor performance and functionality

**Expected Results:**
- [ ] All messages are handled
- [ ] Performance remains good
- [ ] Memory usage is stable
- [ ] Conversation history is maintained

## Test Completion Checklist

### Environment Verification
- [ ] Backend API is running and healthy
- [ ] Frontend application is accessible
- [ ] All dependencies are installed
- [ ] Test data is available

### Core Functionality
- [ ] All happy path scenarios pass
- [ ] All keyword categories work correctly
- [ ] Error handling works as expected
- [ ] Performance meets requirements

### User Experience
- [ ] UI/UX meets design requirements
- [ ] Accessibility standards are met
- [ ] Cross-browser compatibility verified
- [ ] Mobile responsiveness (if applicable)

### Data Quality
- [ ] All response data is realistic
- [ ] Data structures are consistent
- [ ] Suggestions are helpful and relevant
- [ ] No data corruption or errors

### Edge Cases
- [ ] Special characters handled correctly
- [ ] Case sensitivity works properly
- [ ] Long conversations function well
- [ ] Concurrent usage is supported

## Issues and Observations

### Bugs Found
*Document any bugs discovered during testing*

| Issue | Severity | Description | Steps to Reproduce | Expected vs Actual |
|-------|----------|-------------|-------------------|-------------------|
| | | | | |

### Performance Notes
*Document performance characteristics observed*

### Usability Observations
*Note any usability issues or improvements*

### Browser-Specific Issues
*Document any browser-specific problems*

## Test Sign-off

**Tester:** ___________________  
**Date:** ___________________  
**Environment:** ___________________  
**Overall Status:** [ ] Pass [ ] Pass with Issues [ ] Fail  

**Notes:**
_____________________________________________________________________
_____________________________________________________________________
_____________________________________________________________________