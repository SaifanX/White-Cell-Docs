# Sentinel AI Module

**File Path:** `src/pages/SentinelAI.tsx` (455 lines)

## Overview

Sentinel AI is the application's intelligent multi-purpose assistant and research engine. It serves as both a conversational companion for disciplined personal development and an advanced research tool leveraging web-connected AI for deep investigation. The system maintains conversation memory, supports context-aware responses, and switches between normal (fast, cost-effective) and research modes based on user needs.

## Page Purpose

- **Primary Function:** Intelligent AI assistant with dual-mode operation
- **Target Users:** All users seeking personalized coaching and research
- **Core Feature:** Dual-mode AI (Normal vs Research) with context memory
- **Integration:** Access to user data for personalized responses

## Key Features

### 1. Dual-Mode AI Operation

**Normal Mode:**
- Fast response time (sub-second)
- Groq API (Llama 3.3 70B model)
- Cost-effective (~$0.001-0.005 per query)
- Suitable for general questions, advice, analysis
- No web searches (knowledge cutoff ~April 2024)
- Default mode for routine questions

**Research Mode:**
- Deep web-connected research
- Perplexity API (Sonar model)
- Expensive ($0.50-1.00 per query)
- Real-time current information
- Slower response (3-10 seconds)
- Suitable for trends, current events, latest research

**Mode Selection:**
- Large toggle button: "Normal" ↔ "Research"
- Status display: "Fast responses" vs "Deep research"
- Keyboard shortcut: Press 'M' to toggle
- Toast notification on mode switch
- Visual feedback (button color change)

### 2. Conversation Management & History

**Chat History System:**
```typescript
{
  conversationId: string,
  userId: string,
  messages: Message[],
  createdAt: Date,
  lastActiveAt: Date,
  mode: "normal" | "research",
  topic: string (inferred)
}
```

**Message Object:**
```typescript
{
  role: "user" | "sentinel",
  content: string,
  timestamp: number,
  tokens: number (optional)
}
```

**History Features:**
- All conversations automatically saved
- Toggle history sidebar: "History" button (Cmd+H)
- Load past conversations to resume
- Conversation preview (first message)
- Chronological order (newest first)
- Search conversations (planned)
- Delete conversation (planned)

**History UI:**
- Sidebar on desktop (lg:col-span-1)
- Collapse on mobile to maximize chat
- Smooth transitions (Framer Motion)
- Click to select conversation
- Current conversation highlighted

### 3. AI Context Memory System

**Context Purpose:**
- Sentinel AI "remembers" user's situation
- Enables personalized, relevant responses
- Reduces need to repeat background info
- Improves coaching accuracy

**Context Types:**

| Category | Example |
|----------|---------|
| Personal Background | Age 22, college student, software engineer |
| Goals | 3.5+ GPA, run 5K in 25 min, side project portfolio |
| Preferences | Direct communication, tough love, fast feedback |
| Current Situation | Busy exam period, stressed, 6 hours sleep/night |
| Constraints | Limited budget, timezone, family obligations |
| Values | Privacy, autonomy, continuous learning |

**Context Memory Tab:**

Large textarea for freeform context entry:
```
Example Context:
I'm a 22-year-old computer science student at UC Berkeley.
Main goals: maintain 3.5+ GPA, get fit (5K < 25 min), 
build portfolio project, improve public speaking.
I wake at 6am, classes 9am-2pm, project work 2-6pm.
Introvert, prefer written communication. 
Respond well to direct feedback without sugar-coating.
```

**Saving Context:**
- Manual click "Save Context" button
- Keyboard shortcut: Ctrl+S (or Cmd+S)
- Save triggers mutation: `updateContext({ manualContext })`
- Success toast: "Context saved successfully!"
- Context persists across sessions

### 4. Conversation Tabs

**Two-Tab Interface:**

**Chat Tab:**
- Full chat display with all messages
- AIChatbot component with:
  - `moduleType="sentinel"`
  - `title="Sentinel AI Command Interface"`
  - `mode={mode}` (normal/research)
  - `conversationId={selectedConversationId}`
- Input box at bottom
- Message history scrollable

**Memory Tab:**
- Context memory editor (textarea)
- "Save Context" button
- Conversation memory display
- Shows last 10 messages in conversation
- Scrollable memory list
- Update context without affecting chat

**Tab Navigation:**
- Click tab buttons to switch
- Swipe left/right on mobile
- Alt+← → keyboard shortcuts
- Visual indication of current tab
- Haptic feedback on mobile

### 5. Status Bar & Branding

**Sentinel AI Header:**
```
┌──────────────────────────────────────────────┐
│  🛡️ SENTINEL AI          🎯 pulsing          │
│  OPERATIONAL INTELLIGENCE SYSTEM              │
│  ONLINE | AGENTIC | READY                     │
└──────────────────────────────────────────────┘
```

**Design Elements:**
- Shield icon (🛡️) in accent-colored pill
- "SENTINEL AI" in large bold text
- "OPERATIONAL INTELLIGENCE SYSTEM" subtitle
- Three status badges: ONLINE, AGENTIC, READY
- Target icon (🎯) pulsing animation
- Interactive hover effect (scale 1.01x)
- Accent color matching theme

### 6. Mode Selector UI

**Visual Design:**
```
AI Mode: [Normal] [Research]
         └─ Fast responses  (or "Deep research")
```

**Features:**
- Side-by-side buttons
- Current mode highlighted (default button)
- Non-current mode as outline button
- Status text shows implications
- Mobile: Full-width buttons, stacked
- Desktop: Side-by-side, inline

**Interaction:**
- Click button to switch mode
- Haptic feedback on click
- Success toast notification
- Visual transition (0.2s)

### 7. Keyboard Shortcuts

**Global Shortcuts (when not typing):**
- `Ctrl+H` / `Cmd+H`: Toggle history panel visibility
- `M`: Toggle between Normal and Research modes
- `Alt+ArrowLeft`: Switch to previous tab
- `Alt+ArrowRight`: Switch to next tab

**Memory Tab Specific:**
- `Ctrl+S` / `Cmd+S`: Save context

**Restrictions:**
- Shortcuts disabled while typing in input/textarea
- Check: `if (e.target instanceof HTMLInputElement)` before trigger
- Show hints: "(Ctrl+S)" on Save button

### 8. Mobile & Responsive Design

**Pull-to-Refresh:**
- Swipe down to refresh data
- Indicator shows at top with opacity scaling
- Haptic success feedback
- Toast: "Data refreshed!"
- 1-second timeout delay

**Mobile Optimizations:**
- Compact header with back button
- History button toggles sidebar
- Full-width chat area
- Font scaling: 10px-base (breakpoint-based)
- Bottom padding for mobile nav
- Large touch targets (44px minimum)

**Responsive Breakpoints:**
- Mobile: < 640px (single column, sidebar collapses)
- Tablet: 640px-1024px (2 columns, medium padding)
- Desktop: > 1024px (full layout, max-width)

### 9. AI Capabilities & Context Integration

**Context-Aware Responses:**
- Access to user's XP level, streaks, goals
- Reference discipline metrics in advice
- "I see you have a 23-day streak, let's discuss..."
- Personalized recommendations based on data

**Multi-Module Awareness:**
- Understand all five modules (Study, Fitness, Discipline, Mind, Creator)
- Give module-specific advice
- Cross-module insights: "Your discipline streaks correlate with study improvement"
- Integrated perspective on total life situation

**Response Styles:**
- **Normal Mode:** Direct, tactical, no-nonsense
- **Research Mode:** In-depth, data-backed, current information
- **Tone:** Professional, respecting user's time
- **Approach:** Listen to preferences in context memory

### 10. Conversation Memory Display

**Recent Messages:**
- Shows last 10 messages (auto-scrolled)
- Scrollable if more than screen height
- Message format:
  ```
  [User]
  "Can you help me improve my chess tactics?"
  
  [Sentinel AI]
  "Based on your puzzle stats, here are 3 areas..."
  ```
- Color-coded: User (primary/10), AI (card background)
- Timestamp shown (relative: "5 min ago")
- Whitespace preserved for code/formatting

### 11. Data Persistence

**Database Schema:**

| Table | Fields | Purpose |
|-------|--------|---------|
| `aiConversations` | userId, messages[], mode, createdAt, lastActiveAt | Conversation history |
| `users` (extended) | aiContext { manualContext, conversationHistory[] } | Memory storage |

**Query & Mutation:**
```typescript
const aiContext = useQuery((api as any).users.getAIContext);
const updateContext = useMutation((api as any).users.updateAIContext);
```

**Auto-Caching:**
- Convex auto-caches queries
- Context updates reflected immediately
- No manual cache invalidation

### 12. Error Handling

**API Failures:**
- Network error → "Connection failed, please retry"
- API timeout → "Request took too long, try again"
- Rate limit → "Too many requests, please wait"
- Invalid context → "Context format issue, please review"

**User Feedback:**
- Toast notifications for errors
- Error messages clear and actionable
- Haptic error feedback (mobile)
- Retry button available

## State Management

**Major State Variables:**

```typescript
const [mode, setMode] = useState<"normal" | "research">("normal");
const [showHistory, setShowHistory] = useState(false);
const [selectedConversationId, setSelectedConversationId] = useState<Id<"aiConversations"> | undefined>();
const [activeTab, setActiveTab] = useState<"chat" | "memory">("chat");
const [manualContext, setManualContext] = useState("");
```

## Convex Queries & Mutations

**Queries:**
- `(api as any).users.getAIContext` - Fetch user's context and conversation history

**Mutations:**
- `(api as any).users.updateAIContext` - Save manual context

**No direct AI mutations** - AIChatbot component handles message sending internally

## XP & Gamification

**XP Awards:**
- Ask question: 1 XP (encourages engagement)
- Quality question (5+ words): 3 XP
- Follow-up questions: 2 XP each
- Save context: 5 XP (self-reflection)
- Use research mode (expensive): 25 XP (effort bonus)

**Streaks:**
- Daily Sentinel interaction streak
- Weekly research mode usage
- Context update streak (keep memory fresh)

**Badges:**
- "AI Friend" (first conversation)
- "Memory Master" (save context 5 times)
- "Researcher" (use research mode 10 times)
- "Trusted Confidant" (1000 messages in conversation)

## Performance Optimizations

- Real-time Convex queries with auto-caching
- Modal conversations lazy-load
- Message rendering virtualized for long chats
- Context updates debounced (500ms)
- Conversation switching instant (cached)

## Accessibility

- High contrast for mode buttons
- Focus management in modal dialogs
- Screen reader support for status badges
- Keyboard navigation for all tabs
- Color not-only indicator (icons + text)

## Related Components

- **AIChatbot:** Main chat interface
- **ChatHistory:** Conversation selector
- **Textarea:** Context memory input

## Integration Points

1. **All Modules:** Sentinel can discuss any module
2. **Discipline:** Task accountability
3. **Study:** Learning strategy
4. **Fitness:** Workout programming
5. **Mind:** Strategic thinking
6. **Creator:** Content strategy
7. **XP System:** Awards for engagement

## Future Features

1. **Advanced Memory:**
   - Automatic context learning from conversations
   - Preference detection and adaptation
   - Emotional tone awareness

2. **Data Analysis:**
   - Run statistical analysis on user data
   - Identify patterns (habits, preferences)
   - Predict performance based on history

3. **Voice & Multimodal:**
   - Voice input (speak to Sentinel)
   - Voice output (listen to responses)
   - Upload images for analysis

4. **Collaborative AI:**
   - Multiple users share Sentinel instance
   - Team strategy discussions
   - Shared context mode

5. **Advanced Integrations:**
   - Export conversations as PDFs
   - Email summaries of insights
   - Sync with calendar for reminders

## Technical Stack

- **Frontend:** React 18, TypeScript
- **AI Models:**
  - Normal: Groq (Llama 3.3 70B)
  - Research: Perplexity (Sonar online)
- **Backend:** Convex (queries, mutations)
- **Real-time:** Convex auto-sync
- **UI:** shadcn/ui components
- **Animations:** Framer Motion
- **Styling:** Tailwind CSS
- **Routing:** React Router
