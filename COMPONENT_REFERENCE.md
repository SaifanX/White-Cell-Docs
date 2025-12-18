# Component Reference - All Module Pages

**Document:** Detailed UI Components, Appearance, and Backend for All Pages
**Version:** 1.0
**Coverage:** Discipline, Creator, Sentinel AI, Dashboard, Landing, Empire, Onboarding, Settings, Admin, Auth, Features, Warrior Library, Documentation, Support, 404

---

## Discipline Module Components

### Daily Task Manager Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Today's Tasks                        │
│ 12 of 15 completed (80%)             │ ← Progress bar
├──────────────────────────────────────┤
│                                      │
│ Morning Routine:                     │
│ ☑ Wake up at 6am (yellow medal 🏅)  │
│ ☑ Exercise (45 min)                  │
│ ☐ Cold shower                        │
│ ☐ Meditation (10 min)                │
│                                      │
│ [+ Add Task]                         │
└──────────────────────────────────────┘
```

**Task Item:**
- Checkbox: Toggle completion
- Title: Font-size 16px, strikethrough if complete
- Time: Optional, next to title
- Category badge: Color-coded (Morning: blue, Work: green, Evening: orange)
- Actions: [Edit] [Delete] (on hover)

**Backend:**
```typescript
async function logTask(task: {
  title: string,
  category: string,
  time?: string,
  completed: boolean
}) {
  return await mutation('createDailyTask', {
    userId: currentUser.id,
    ...task,
    date: new Date()
  })
}

const dailyTasks = useFetch('getDailyTasks', {
  userId: currentUser.id,
  date: new Date()
})
```

### Habit Stack Builder Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Habit Stack: Morning Routine         │
│ Streak: 🔥 18 days                  │
├──────────────────────────────────────┤
│                                      │
│ After I [wake up], I will:           │ ← Anchor
│                                      │
│ 1. Drink water (500ml)               │
│ 2. Exercise (push-ups 20)            │
│ 3. Cold shower (2 min)               │
│ 4. Meditate (deep breaths 10)        │
│                                      │
│ [✓ Track Today] [Edit] [View History]│
│                                      │
└──────────────────────────────────────┘
```

**Stack Display:**
- Anchor habit: "After I [trigger]"
- Numbered list: Stacked habits
- Streak badge: Fire emoji + days
- Progress: Today completed? (checkmark or pending)
- Buttons: [Track Today] [Edit] [History]

**Backend:**
```typescript
async function createHabitStack(stack: {
  name: string,
  anchorHabit: string,
  habits: string[],
  difficulty: 'easy' | 'medium' | 'hard'
}) {
  return await mutation('createHabitStack', {
    userId: currentUser.id,
    ...stack
  })
}

async function trackHabitStack(stackId: string) {
  return await mutation('trackHabitStackCompletion', {
    userId: currentUser.id,
    stackId,
    date: new Date()
  })
  // Awards 25 XP
  // Updates streak
}
```

### PMO Tracker Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ PMO Tracker - Recovery Support       │
│                                      │
│ ⏱ Current Streak: 42 Days 🔥         │ ← Streak
│   Longest Streak: 89 Days            │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Timeline (90 days)               │ │
│ │ ███░░░░░░░░░░░░░░░░░░░░░░░░░░░  │ │ ← Green/gray
│ │ Green = Clean, Gray = No data    │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Recovery Benefits:                   │
│ ✓ Energy: [████░░░░░] 4/5           │
│ ✓ Focus: [█████░░░░░] 5/5           │
│ ✓ Mood: [██████░░░░] 5/5            │
│ ✓ Confidence: [█████░░░░░] 4/5      │
│                                      │
│ [Log Relapse] [Recovery Resources]   │
│                                      │
└──────────────────────────────────────┘
```

**Streak Display:**
- Large fire emoji (🔥)
- Bold number: Days
- Personal best underneath
- Milestone badges: 7, 30, 90, 365 days

**Calendar Heatmap:**
- Green squares: Clean days
- Gray squares: No data
- Hover: Show day details
- Interactive: Click to add notes

**Recovery Scales:**
- 1-5 point sliders
- Self-reported metrics
- Animated on update
- Trend line (improving/stable/declining)

**Backend:**
```typescript
async function logPMORelapse() {
  return await mutation('logPMORelapse', {
    userId: currentUser.id,
    timestamp: new Date(),
    resetStreak: true
  })
  // Resets streak to 0
  // Triggers support resources notification
}

const pmoStats = useFetch('getPMOStats', {
  userId: currentUser.id,
  days: 90
})
// Returns: {
//   currentStreak: number,
//   longestStreak: number,
//   cleanDays: number[],
//   recoveryScores: { energy, focus, mood, confidence }
// }
```

---

## Creator Module Components

### Content Calendar - Kanban Board Component

**Appearance:**
```
┌──────────────────────────────────────────────┐
│ Content Calendar (Kanban View)               │
│ [Month] [Kanban] [List] [Analytics]          │
├──────────────────────────────────────────────┤
│                                              │
│ ┌─────────┬─────────┬──────────┬─────────┐  │
│ │IDEATION │SCRIPTING│PRODUCTION│PUBLISH  │  │
│ ├─────────┼─────────┼──────────┼─────────┤  │
│ │ ┌─────┐ │ ┌─────┐ │ ┌──────┐ │┌──────┐ │  │
│ │ │Title│ │ │Title│ │ │Title │ ││Pub   │ │  │
│ │ │Desc │ │ │Scr  │ │ │Video │ ││Tue   │ │  │
│ │ │Due  │ │ │Tips │ │ │Post  │ ││10am  │ │  │
│ │ └─────┘ │ └─────┘ │ └──────┘ ││2.4K  │ │  │
│ │         │         │          │└──────┘ │  │
│ │ ┌─────┐ │ ┌─────┐ │ ┌──────┐ │         │  │
│ │ │[NEW]│ │ │[NEW]│ │ │[NEW] │ │[NEW]   │  │
│ │ └─────┘ │ └─────┘ │ └──────┘ │        │  │
│ └─────────┴─────────┴──────────┴─────────┘  │
│                                              │
└──────────────────────────────────────────────┘
```

**Columns:** Ideation, Scripting, Production, Publishing

**Card:**
- Title: Font 16px, bold
- Preview: Platform badges (Instagram, YouTube, TikTok)
- Due date: Timestamp
- Metrics (if published): Views, likes, comments
- Drag handle: Grab icon on left

**Drag & Drop:**
- Reorder within column
- Move between columns
- Auto-save on drop
- Undo available

**Backend:**
```typescript
// Convex Query - Get content items
const contentItems = useFetch('getContentItems', {
  userId: currentUser.id,
  status: selectedStatus
})

// Convex Mutation - Update status
async function updateContentStatus(itemId: string, newStatus: string) {
  return await mutation('updateContentItemStatus', {
    userId: currentUser.id,
    itemId,
    newStatus,
    timestamp: new Date()
  })
}
```

### Content Idea Vault Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Idea Vault                           │
│ [List] [Grid] [Search]               │
│ Category: [All ▼]                    │
├──────────────────────────────────────┤
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ 5 Tips for Productivity          │ │
│ │ Category: Tutorial               │ │
│ │ Potential: 🔥 High               │ │
│ │ Ideas: • Fast cuts               │ │
│ │        • B-roll                  │ │
│ │        • Viewer examples         │ │
│ │ Tags: #productivity #tips #time  │ │
│ │ [Move to Kanban] [Edit] [Delete] │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ New Idea]                         │
│                                      │
└──────────────────────────────────────┘
```

**Idea Card:**
- Title: 18px, bold
- Category badge: Color-coded
- Potential indicator: 🔥 (High), 🌟 (Medium), ○ (Low)
- Brainstorm list: Bullet points
- Tags: Hashtag pills
- Action buttons: [Move] [Edit] [Delete]

**Backend:**
```typescript
async function createContentIdea(idea: {
  title: string,
  category: string,
  brainstormPoints: string[],
  tags: string[],
  potential: 'low' | 'medium' | 'high'
}) {
  return await mutation('createContentIdea', {
    userId: currentUser.id,
    ...idea
  })
}

const ideas = useFetch('getContentIdeas', {
  userId: currentUser.id,
  category: selectedCategory || undefined
})
```

### Analytics Dashboard Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Analytics Dashboard                  │
│ [This Week] [This Month] [All Time]  │
├──────────────────────────────────────┤
│                                      │
│ Total Views: 45,234 (↑12%)           │
│ Total Likes: 3,201 (↑8%)             │
│ Total Comments: 487 (↓2%)            │
│ Total Shares: 156 (↑5%)              │
│                                      │
│ Platform Breakdown:                  │
│ Instagram: 28,342 (62%) [████████]   │
│ YouTube: 12,456 (28%) [████░░░░░░]   │
│ TikTok: 3,891 (9%) [██░░░░░░░░]     │
│ Twitter: 545 (1%) [░░░░░░░░░░]      │
│                                      │
│ Top Content:                         │
│ 1. "Productivity Tips" (8,234 views) │
│ 2. "Morning Routine" (6,123 views)   │
│ 3. "Gym Motivation" (5,456 views)    │
│                                      │
│ [Export Report]                      │
│                                      │
└──────────────────────────────────────┘
```

**Metric Cards:**
- Large number (32px)
- Trend arrow (↑ green, ↓ red)
- Percentage change
- Sub-label (metric name)

**Charts:**
- Bar chart: Platform comparison
- Line graph: Trend over time
- Pie chart: Category breakdown

**Backend:**
```typescript
const analytics = useFetch('getContentAnalytics', {
  userId: currentUser.id,
  timeRange: 'week' | 'month' | 'all'
})
// Returns: {
//   totalViews, totalLikes, totalComments, totalShares,
//   byPlatform: { instagram, youtube, tiktok, twitter },
//   topContent: Content[]
// }
```

---

## Sentinel AI Module Components

### Chat Interface - Main Component

**See COMPONENTS.md - Chat Interface for full styling details**

**Message Bubbles:**
- User: Right-aligned, primary color background, rounded
- AI: Left-aligned, card background, rounded
- Timestamp: Small, muted gray
- Avatar: User photo (user), robot icon (AI)

**Input Area:**
- Text field: "Type message..."
- Send button: Paper plane icon, enabled when text present
- Character count: "X/5000"
- Typing indicator: "Sentinel is typing..."

**Keyboard Support:**
- Enter: Send message
- Shift+Enter: New line
- Ctrl+Up/Down: History navigation

**Backend:**
```typescript
// Convex Action - Stream AI response
const response = await action('sentinelAI', {
  message: userMessage,
  mode: 'normal' | 'research',
  conversationHistory: messages,
  context: userContext,
  userId: currentUser.id
})

// Stream via SSE or WebSocket
// Tokens appear in real-time
```

### Mode Selector Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Mode: [Normal Mode ▼]                │
│                                      │
│ 🟢 Normal Mode                       │
│   Fast, cost-effective               │
│   Uses Groq Llama 3.3                │
│   Best for: Quick answers            │
│                                      │
│ 🟠 Research Mode                     │
│   Deep, web-connected                │
│   Uses Perplexity Sonar              │
│   Best for: In-depth research        │
│   [Requires Premium]                 │
│                                      │
│ [Switch to Research]                 │
│                                      │
└──────────────────────────────────────┘
```

**Mode Indicator:**
- Badge: "Normal Mode" or "Research Mode"
- Color: Green (normal), Orange (research)
- Status icon: 🟢 or 🟠

**Mode Comparison Table:**
```
Feature        | Normal    | Research
─────────────────────────────────────
Speed          | Instant   | 2-5 sec
Web Access     | No        | Yes
Sources        | Training  | Live
Cost           | Low       | Higher
Citations      | No        | Yes
```

**Backend:**
```typescript
async function switchMode(newMode: 'normal' | 'research') {
  // Check if user has premium for research mode
  const isPremium = await userSubscription(currentUser.id)
  if (newMode === 'research' && !isPremium) {
    showUpgradePrompt()
    return
  }
  
  return await mutation('setAIMode', {
    userId: currentUser.id,
    mode: newMode
  })
}
```

### Conversation Tabs Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ ┌────────┐ ┌────────┐ ┌─────────┐   │
│ │Fitness │ │Study   │ │General  │ + │
│ │ ✕      │ │ ✕      │ │ ✕       │   │
│ └────────┘ └────────┘ └─────────┘   │
├──────────────────────────────────────┤
│ (Conversation content)                │
└──────────────────────────────────────┘
```

**Tab Features:**
- Title: Conversation name
- Close button: × (delete)
- Active state: Underline + highlight
- New tab: + button
- Drag to reorder (future)

**Backend:**
```typescript
async function createConversation(topic: string) {
  return await mutation('createAIConversation', {
    userId: currentUser.id,
    topic,
    timestamp: new Date()
  })
}

const conversations = useFetch('getAIConversations', {
  userId: currentUser.id
})
```

### Context Memory Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Context Memory                       │
│ (What I know about you)              │
├──────────────────────────────────────┤
│                                      │
│ • Goal: Improve fitness              │
│ • Focus area: Leg strength           │
│ • Current streak: 42 days            │
│ • Preferred workout time: 6am        │
│ • Dietary preference: High protein   │
│ • Work focus: Web development        │
│ • Learning goal: Master React        │
│                                      │
│ [+ Add Context] [Clear Memory]       │
│ [Auto-Update] Toggle                 │
│                                      │
└──────────────────────────────────────┘
```

**Memory Items:**
- Bullet points
- Editable (click to modify)
- Deletable (× button)
- Add new: [+ Add Context] button
- Clear all: [Clear Memory] with confirmation

**Auto-Update:**
- Automatically learn from: Module activities, conversations, profile updates
- User can toggle on/off
- Last updated: Timestamp

**Backend:**
```typescript
async function addContextMemory(contextItem: string) {
  return await mutation('addAIContextMemory', {
    userId: currentUser.id,
    context: contextItem,
    autoLearned: false
  })
}

// Auto-learn from user activities
const activityContext = await action('extractContextFromActivities', {
  userId: currentUser.id,
  activities: recentActivities
})
```

---

## Dashboard Component

### Welcome Card Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Welcome back, [User]! 👋             │
│                                      │
│ You're on a 🔥 42-day streak!       │
│ Next XP milestone: 5,000 XP (500 away)
│                                      │
│ Your stats today:                    │
│ • 8 tasks completed                  │
│ • 30 min study                       │
│ • 1 workout logged                   │
│                                      │
│ [Resume Activity] [View Goals]       │
│                                      │
└──────────────────────────────────────┘
```

**Elements:**
- Greeting: "Welcome back, [User]! 👋"
- Streak badge: 🔥 [days] streak
- Next milestone: Progress to XP goal
- Daily summary: Bullet points of activities
- Call-to-action buttons

**Backend:**
```typescript
const userWelcome = useFetch('getWelcomeData', {
  userId: currentUser.id
})
// Returns: {
//   userName: string,
//   currentStreak: number,
//   nextMilestone: { xp, remaining },
//   todayActivities: string[]
// }
```

### Module Quick Access Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Quick Access                         │
│                                      │
│ ┌──────────┐ ┌──────────┐ ┌────────┐│
│ │ 🎯 Start │ │ 📚 Study │ │ 💪 Fit ││
│ │Discipline│ │ Session  │ │Workout ││
│ │ Routine  │ │ (45 min) │ │(45 min)││
│ │  [→]     │ │  [→]     │ │ [→]    ││
│ └──────────┘ └──────────┘ └────────┘│
│                                      │
│ ┌──────────┐ ┌──────────┐ ┌────────┐│
│ │ 🧠 Chess │ │ 🏆Empire│ │ 🤖 Ask ││
│ │ Puzzle   │ │ Check   │ │ AI     ││
│ │ (Rating  │ │ Stats   │ │Coach  ││
│ │ 1650)    │ │         │ │        ││
│ │  [→]     │ │  [→]    │ │ [→]    ││
│ └──────────┘ └──────────┘ └────────┘│
│                                      │
└──────────────────────────────────────┘
```

**Quick Action Cards:**
- Icon: Module-specific emoji
- Title: Action name
- Subtitle: Details or hint
- Arrow: [→] indicates clickable
- Click: Navigate to module/start activity

**Backend:**
```typescript
const quickActions = useFetch('getQuickActions', {
  userId: currentUser.id,
  preferences: userPreferences
})
// Returns recommended actions based on:
// - User goals and streaks
// - Time of day
// - Recent activities
```

### Recent Activity Feed Component

**See XPActivityFeed in COMPONENTS.md for detailed styling**

---

## Landing Page Components

### Hero Section Component

**Appearance:**
```
┌──────────────────────────────────────┐
│  WHITE CELL PROTOCOL                 │
│  Transform Your Life, Warrior        │
│                                      │
│  Complete platform for studying,     │
│  fitness, and personal mastery       │
│                                      │
│  [Get Started] [Watch Demo]          │
│                                      │
│  Trusted by 10,000+ warriors         │
│                                      │
└──────────────────────────────────────┘
```

**Elements:**
- Logo: Animated on scroll
- Headline: Large (48px+), bold
- Subheading: 20px, muted color
- CTA Buttons: [Get Started] (primary), [Watch Demo] (outline)
- Social proof: "10,000+ warriors trust us"
- Background: Gradient or hero image

**Backend:**
```typescript
// Static component, no backend call needed
// Analytics: Track button clicks
await mutation('trackLandingPageEvent', {
  event: 'hero_cta_click',
  ctaType: 'get_started' | 'watch_demo'
})
```

### Feature Showcase Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Features That Transform You          │
│                                      │
│ ┌──────────────┬──────────────────┐ │
│ │ 📚 Study     │ Master any       │ │
│ │ Module       │ subject with     │ │
│ │              │ spaced rep cards │ │
│ │ [Learn More] │ and AI tutoring  │ │
│ └──────────────┴──────────────────┘ │
│                                      │
│ ┌──────────────┬──────────────────┐ │
│ │ 💪 Fitness   │ Track workouts,  │ │
│ │ Module       │ nutrition, PRs   │ │
│ │              │ with macro       │ │
│ │ [Learn More] │ counting         │ │
│ └──────────────┴──────────────────┘ │
│                                      │
│ [View All Features]                  │
│                                      │
└──────────────────────────────────────┘
```

**Feature Cards:**
- Icon: Module emoji (📚, 💪, 🧠, etc.)
- Title: Module name
- Description: Key benefit
- Link: [Learn More] button

### Pricing Section Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Choose Your Plan                     │
│                                      │
│ Free           Basic       Premium    │
│ $0/month       $5/month    $15/month │
│ [Selected]     [Choose]    [Choose]  │
│                                      │
│ ✓ Core modules ✓ All modules        │
│ ✓ Basics       ✓ Offline mode       │
│               ✓ Analytics           │
│                                      │
└──────────────────────────────────────┘
```

**Price Cards:**
- Plan name
- Price: "$X/month"
- Feature list: Checkmarks for included features
- CTA button: [Choose Plan] or [Current Plan]
- Highlight: Recommended plan has different styling

**Backend:**
```typescript
// Static pricing, updated via CMS
// Tracking:
await mutation('trackPricingPageEvent', {
  event: 'pricing_plan_selected',
  plan: 'free' | 'basic' | 'premium'
})
```

---

## Empire Module Component

### Power Index Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Power Index                          │
│                                      │
│ Current: 8,450 / 10,000              │
│ [████████░░░░░░] 84.5%              │
│                                      │
│ Composition:                         │
│ Discipline:  2,500 (30%) [████░░]   │
│ Fitness:     2,100 (25%) [████░░]   │
│ Study:       2,000 (24%) [███░░░░]   │
│ Mind:        1,200 (14%) [██░░░░░░]  │
│ Creator:      650 (8%) [█░░░░░░░░░] │
│ Sentinel:     0 (0%) [░░░░░░░░░░]   │
│                                      │
└──────────────────────────────────────┘
```

**Power Bar:**
- Total power: "8,450 / 10,000"
- Progress bar: Filled proportionally, primary color
- Percentage: "84.5%"

**Module Breakdown:**
- Horizontal bar charts per module
- Color-coded by module
- Percentage and power value
- Click to see breakdown

**Backend:**
```typescript
const powerIndex = useFetch('getUserPowerIndex', {
  userId: currentUser.id
})
// Returns: {
//   total: number,
//   max: 10000,
//   byModule: { discipline, fitness, study, mind, creator, sentinel }
// }
```

---

## All Other Page Components

**The remaining pages (Onboarding, Settings, Admin, Auth, Features, Warrior Library, Documentation, Support, 404) have comprehensive descriptions in their respective documentation files:**

- **ONBOARDING.md**: Wizard steps, form inputs, progress indicators
- **SETTINGS.md**: Form inputs, toggles, dropdown selectors, integration cards
- **ADMIN.md**: Data tables, charts, analytics displays, moderation tools
- **AUTH.md**: OTP input fields, login forms, guest selector
- **FEATURES.md**: Feature cards, comparison tables, CTA buttons
- **WARRIOR_LIBRARY.md**: Search, resource cards, filtering, bookmarking
- **DOCUMENTATION.md**: Accordion sections, code blocks, navigation sidebars
- **SUPPORT.md**: Contact form, FAQ accordion, resource links
- **NOT_FOUND.md**: Terminal/glitch styled error display, CTA buttons

**See respective markdown files for detailed component specifications.**

---

## Common Patterns Across All Components

### Card Components (shadcn/ui)
- Border-radius: 8px
- Padding: 24px (standard), 16px (compact)
- Shadow: 0 1px 3px (default), elevated on hover
- Border: 1px border-color
- Hover: Scale 1.02, shadow increase (if clickable)
- Transition: 200ms ease

### Button Components (shadcn/ui)
- Height: 40px (medium) standard
- Padding: 16px (horizontal), 8px (vertical)
- Variants: Primary, Secondary, Outline, Ghost, Destructive
- States: Default, Hover (scale 1.02), Active (scale 0.98), Disabled (opacity 50%)
- Loading: Show spinner, text hidden

### Input Fields (shadcn/ui)
- Height: 40px
- Padding: 12px
- Border: 1px border-color
- Focus: 2px ring primary, border primary
- Error: Ring destructive, border destructive
- Disabled: Opacity 50%, cursor not-allowed

### Progress Indicators
- Linear bars: Height 4-8px, border-radius 4px
- Circular rings: SVG circle, animated stroke
- Color: Green (on track), Yellow (warning), Red (behind)
- Animation: 300ms smooth width/stroke change

### Modals & Dialogs
- Max-width: 512px (standard)
- Backdrop: rgba(0,0,0,0.5) with blur
- Animations: Scale 0.9→1 (enter), fade 0→1
- Z-index: 50
- Close button: × (top right)

### Form Layouts
- Field spacing: 16px gap between fields
- Label: Font-size 14px, font-weight 500, color foreground
- Helper text: Font-size 12px, color muted-foreground
- Error message: Font-size 12px, color destructive
- Required indicator: * (red/destructive color)

---

## Backend Integration Pattern

### Standard Query Pattern
```typescript
const data = useFetch('getResource', {
  userId: currentUser.id,
  filters: selectedFilters,
  pagination: { page, limit }
})
// Returns: Data from Convex database
```

### Standard Mutation Pattern
```typescript
async function createOrUpdateResource(data: any) {
  return await mutation('createOrUpdateResource', {
    userId: currentUser.id,
    ...data,
    timestamp: new Date()
  })
  // Returns: Updated resource with ID
  // Awards XP if applicable
  // Triggers notifications if needed
}
```

### Standard Action Pattern (AI)
```typescript
const aiResponse = await action('sentinelAI', {
  message: userInput,
  mode: 'normal' | 'research',
  context: relevantContext,
  userId: currentUser.id
})
// Returns: Streamed response from LLM
// Supports: Groq (normal), Perplexity (research)
```

---

## Accessibility Standards

All components follow WCAG 2.1 AA:
- **Color Contrast:** 4.5:1 (normal), 3:1 (large text)
- **Focus Indicators:** 2px ring, primary color
- **Keyboard Navigation:** Tab through all interactive elements
- **Screen Reader:** Semantic HTML, ARIA labels
- **Touch Targets:** 44px minimum (mobile), 40px (desktop)
- **Motion:** Respects prefers-reduced-motion media query

---

## Performance Optimization

- **Queries:** Server-side filtering and pagination
- **Mutations:** Optimistic updates + server confirmation
- **Rendering:** React.memo for list items, virtualization for large lists
- **Images:** Compression on upload, lazy loading
- **Assets:** Code splitting by module, dynamic imports
- **Caching:** Convex auto-cache, local storage for preferences

---

**Document Last Updated:** December 18, 2025
**Maintained By:** Design & Development Team
**Status:** Complete - All pages documented with components
