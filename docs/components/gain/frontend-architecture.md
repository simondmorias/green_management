# Frontend Architecture (apps/web)

## Technology Stack

### Core Framework
- **Next.js 13.5+**: React framework with SSR/SSG capabilities
- **React 18**: UI library with concurrent features
- **TypeScript 5+**: Type-safe JavaScript

### Styling & UI
- **Tailwind CSS 3.3+**: Utility-first CSS framework
- **CSS Modules**: Component-scoped styles
- **shadcn/ui patterns**: Accessible component patterns
- **PostCSS**: CSS processing

### State Management
- **React Hooks**: Built-in state management
- **Custom Hooks**: Reusable business logic
- **Context API**: For cross-component state (if needed)

## Project Structure

```
apps/web/src/
├── components/
│   ├── chat/                   # Chat-specific components
│   │   ├── ChatInterface.tsx   # Main chat container
│   │   ├── EnhancedMessageInput.tsx # Input with entity recognition
│   │   ├── MessageBubble.tsx   # Message display
│   │   ├── MessageList.tsx     # Chat history
│   │   ├── MessageInput.tsx    # Basic input (deprecated)
│   │   ├── EntityPill.tsx      # Entity pill component
│   │   ├── EntityPill.module.css # Entity pill styles
│   │   └── HighlightedText.tsx # Text with entity highlighting
│   │
│   └── ui/                      # Reusable UI components
│       ├── button.tsx           # Button component
│       ├── card.tsx             # Card component
│       └── input.tsx            # Input component
│
├── hooks/                       # Custom React hooks
│   ├── useChat.ts              # Chat functionality hook
│   └── useEntityRecognition.ts # Entity recognition hook
│
├── pages/                       # Next.js pages
│   ├── _app.tsx                # App wrapper
│   ├── index.tsx               # Home page
│   └── api/                    # API routes (proxy)
│       ├── chat.ts             # Chat endpoint proxy
│       └── chat/
│           └── recognize-entities.ts # Entity recognition proxy
│
├── services/                    # API client services
│   └── entityApi.ts            # Entity API client
│
├── styles/                      # Global styles
│   └── globals.css             # Global CSS with Tailwind
│
├── types/                       # TypeScript types
│   ├── api.ts                  # API response types
│   ├── chat.ts                 # Chat-related types
│   └── entities.ts             # Entity types
│
└── utils/                       # Utility functions
    ├── api.ts                  # API helper functions
    └── debounce.ts             # Debounce utility
```

## Key Components

### ChatInterface Component
Main container for the chat feature.

```typescript
// Key responsibilities:
- Manages chat state (messages, loading)
- Handles message sending
- Integrates entity recognition
- Renders message list and input
```

### EnhancedMessageInput Component
Advanced input with real-time entity recognition.

```typescript
interface EnhancedMessageInputProps {
  onSendMessage: (message: string) => void;
  isLoading?: boolean;
  disabled?: boolean;
  enableEntityRecognition?: boolean;
}

// Features:
- Real-time entity recognition
- Debounced API calls (500ms)
- Character limit (2000)
- Shift+Enter for newlines
- Entity display with tooltips
```

### useEntityRecognition Hook
Custom hook for entity recognition functionality.

```typescript
interface UseEntityRecognitionOptions {
  enabled?: boolean;
  debounceMs?: number;
}

// Returns:
{
  entities: RecognizedEntity[];
  isLoading: boolean;
  error: string | null;
  processingTime: number;
  recognize: (text: string) => void;
  clear: () => void;
}
```

## API Integration

### Next.js API Routes
Proxy endpoints to handle CORS and authentication.

```typescript
// pages/api/chat.ts
export default async function handler(req, res) {
  const response = await fetch(`${BACKEND_URL}/api/chat`, {
    method: req.method,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(req.body),
  });
  
  const data = await response.json();
  res.status(response.status).json(data);
}
```

### Service Layer
Abstracted API calls for components.

```typescript
// services/entityApi.ts
export const recognizeEntities = async (text: string) => {
  const response = await fetch('/api/chat/recognize-entities', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ text }),
  });
  
  if (!response.ok) throw new Error('Recognition failed');
  return response.json();
};
```

## Type System

### Core Types

```typescript
// Entity Types
interface RecognizedEntity {
  text: string;
  type: EntityType;
  start: number;
  end: number;
  confidence: number;
  id?: string;
  metadata: EntityMetadata;
}

type EntityType = 
  | 'brand' 
  | 'manufacturer' 
  | 'product' 
  | 'category'
  | 'metric' 
  | 'time_period' 
  | 'special';

// Chat Types
interface Message {
  id: string;
  text: string;
  sender: 'user' | 'assistant';
  timestamp: Date;
  entities?: RecognizedEntity[];
}

interface ChatResponse {
  response: string;
  entities?: RecognizedEntity[];
  processing_time_ms?: number;
}
```

## Styling System

### Tailwind Configuration
```javascript
// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        border: "hsl(var(--border))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
      },
    },
  },
  plugins: [],
}
```

### Component Styling Pattern
```tsx
// Utility classes with Tailwind
<div className="flex flex-col space-y-4 p-4 bg-white rounded-lg">
  <span className="text-sm font-semibold text-blue-600">
    {entity.text}
  </span>
</div>

// CSS Modules for complex styles
import styles from './EntityPill.module.css';
<span className={styles.entityPill}>...</span>
```

## Performance Optimizations

### 1. Debouncing
Entity recognition requests are debounced to reduce API calls.

```typescript
const debouncedRecognize = useMemo(
  () => debounce((text: string) => {
    recognizeEntities(text);
  }, debounceMs),
  [debounceMs]
);
```

### 2. React.memo
Prevent unnecessary re-renders of expensive components.

```typescript
export const MessageBubble = React.memo(({ message }) => {
  // Component implementation
});
```

### 3. Lazy Loading
Dynamic imports for code splitting.

```typescript
const ChatInterface = dynamic(
  () => import('@/components/chat/ChatInterface'),
  { ssr: false }
);
```

## State Management Patterns

### Local Component State
```typescript
const [message, setMessage] = useState('');
const [isLoading, setIsLoading] = useState(false);
```

### Custom Hooks for Business Logic
```typescript
const {
  entities,
  isLoading,
  recognize,
  clear
} = useEntityRecognition({ enabled: true });
```

### Prop Drilling Prevention
Using composition and context where needed.

## Error Handling

### API Error Handling
```typescript
try {
  const response = await fetch('/api/chat');
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  const data = await response.json();
  return data;
} catch (error) {
  console.error('API Error:', error);
  // Show user-friendly error message
  setError('Failed to send message. Please try again.');
}
```

### Component Error Boundaries
```typescript
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    console.error('Component error:', error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <div>Something went wrong.</div>;
    }
    return this.props.children;
  }
}
```

## Testing Strategy

### Unit Tests
```typescript
// __tests__/ChatInterface.test.tsx
describe('ChatInterface', () => {
  it('should send message on Enter key', () => {
    const { getByRole } = render(<ChatInterface />);
    const input = getByRole('textbox');
    
    fireEvent.change(input, { target: { value: 'Test message' } });
    fireEvent.keyDown(input, { key: 'Enter' });
    
    expect(mockSendMessage).toHaveBeenCalledWith('Test message');
  });
});
```

### Integration Tests
```typescript
// Test API integration
it('should recognize entities in text', async () => {
  const { result } = renderHook(() => 
    useEntityRecognition({ enabled: true })
  );
  
  act(() => {
    result.current.recognize('revenue for Q1 2025');
  });
  
  await waitFor(() => {
    expect(result.current.entities).toHaveLength(2);
  });
});
```

## Build & Bundle

### Next.js Build Configuration
```javascript
// next.config.js
module.exports = {
  reactStrictMode: true,
  swcMinify: true,
  output: 'standalone',
  
  webpack: (config) => {
    // Custom webpack config if needed
    return config;
  },
};
```

### Production Optimizations
- Automatic code splitting
- Image optimization
- Font optimization
- CSS purging with Tailwind
- Tree shaking
- Minification

## Development Tools

### VS Code Extensions
- ESLint
- Prettier
- Tailwind CSS IntelliSense
- TypeScript React code snippets

### Browser DevTools
- React Developer Tools
- Redux DevTools (if using Redux)
- Network tab for API debugging

## Accessibility

### ARIA Labels
```tsx
<textarea
  aria-label="Message input"
  aria-describedby="char-count"
  maxLength={2000}
/>
<span id="char-count">{message.length}/2000</span>
```

### Keyboard Navigation
- Tab navigation through interactive elements
- Enter to send messages
- Escape to close modals
- Arrow keys for list navigation

### Screen Reader Support
- Semantic HTML elements
- Proper heading hierarchy
- Alt text for images
- ARIA live regions for dynamic content

## Security Considerations

### XSS Prevention
- React automatically escapes content
- Avoid dangerouslySetInnerHTML
- Sanitize user input

### CSP Headers
```javascript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: "default-src 'self'; script-src 'self' 'unsafe-eval';"
          }
        ]
      }
    ];
  }
};
```

## Deployment Considerations

### Environment Variables
```bash
# .env.production
NEXT_PUBLIC_API_URL=https://api.gain.example.com
NEXT_PUBLIC_ENABLE_ANALYTICS=true
```

### Build Commands
```bash
# Development
npm run dev

# Production build
npm run build
npm run start

# Static export (if applicable)
npm run export
```

### Performance Metrics
- First Contentful Paint (FCP) < 1.8s
- Time to Interactive (TTI) < 3.9s
- Cumulative Layout Shift (CLS) < 0.1
- First Input Delay (FID) < 100ms