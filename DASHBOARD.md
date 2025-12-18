# Dashboard Page

**File Path:** `src/pages/Dashboard.tsx`

## Overview

The Dashboard is the main hub for navigating the White Cell Protocol application. It serves as a launchpad to all six major modules and displays key engagement metrics including daily check-in status and XP activity feed.

## Page Purpose

- **Primary Function:** Module navigator and entry point for authenticated users
- **User Role:** All authenticated users (first-time users redirected to onboarding)
- **Key Feature:** Visual module cards with descriptions and direct navigation
- **Engagement Tools:** Daily check-in reminder, XP activity feed

## Components Included

### 1. Module Navigator Cards

**Six Major Module Cards:**

| Module | Icon | Color | Path | Description |
|--------|------|-------|------|-------------|
| **Discipline Engine** | Target | Red | `/discipline` | Track daily tasks and routines |
| **Study & Build** | BookOpen | Blue | `/study` | Notes, quizzes & learning |
| **Fitness & Combat** | Dumbbell | Green | `/fitness` | Workouts & tracking |
| **Mind & Strategy** | Brain | Yellow | `/mind` | Chess, cube & tactics |
| **Creator Hub** | Video | Pink | `/creator` | Content & money |
| **Sentinel AI** | Bot | Cyan | `/sentinel` | Personal AI assistant |

**Card Features:**
- Icon display with color-coded gradient backgrounds
- Hover effects: Scale to 1.02x, border highlight, shadow enhancement
- Click to navigate directly to module
- "Enter Module" button for explicit CTA
- Responsive grid: 1 column (mobile), 2 columns (tablet), 3 columns (desktop)

### 2. Daily Check-In Component

**Purpose:** Quick morning/evening routine completion tracking
- Pre-filled checklist of common habits
- One-click completion with haptic feedback
- XP awards for streak continuity
- Shows user's current streak
- Quick access to full Discipline module

**Integration:** Displays prominently after greeting to encourage engagement

### 3. XP Activity Feed

**Purpose:** Real-time social proof of user activity
- Shows recent XP gains across all modules
- Activity types: Task completion, pomodoro finish, note creation, workout logging, etc.
- Timestamp for recency
- Animated entry for new activities
- Encourages continued engagement through FOMO

**Features:**
- Refreshes on page load
- Scroll for older activities
- Grouped by date for clarity

## Layout Structure

```
Dashboard Page
├── Top Navigation
│   └── Pull-to-Refresh Indicator (when pulling)
├── Header Section
│   ├── Title: "Module Navigator"
│   └── Greeting: "Welcome back, [User Name]. Choose your domain."
├── Daily Check-In Card
│   └── Quick habit checklist with XP rewards
├── XP Activity Feed
│   └── Recent activity stream
├── Module Grid (Responsive: 1-3 columns)
│   ├── Discipline Engine Card
│   ├── Study & Build Card
│   ├── Fitness & Combat Card
│   ├── Mind & Strategy Card
│   ├── Creator Hub Card
│   └── Sentinel AI Card
└── Bottom Navigation Padding
```

## User Authentication & Redirects

**Authentication Flow:**
1. Check if user is loading (`isLoading`)
2. If not authenticated → Redirect to `/auth`
3. If needs onboarding → Redirect to `/onboarding`
4. If authenticated & onboarded → Show dashboard

**Auth State:**
```typescript
const { isLoading, isAuthenticated, user, needsOnboarding } = useAuth();
```

## Data Queries

**Real-time Data Fetched:**

```typescript
// Today's discipline tasks
const tasks = useQuery((api as any).tasks.getTodayTasks, { 
  date: new Date().toISOString().split('T')[0] 
});

// Recent workouts
const workouts = useQuery((api as any).workouts.getWorkouts);

// All goals
const goals = useQuery((api as any).discipline.getGoals);
```

These queries update in real-time as user completes activities, providing live feedback.

## Interactive Features

### Pull-to-Refresh

- Swipe down from top on mobile to refresh data
- Visual indicator shows pull distance with opacity scaling
- Haptic feedback on completion
- Toast notification: "Data refreshed!"
- Timeout: 1 second delay simulates API call

### Navigation

**Button Navigation:**
- Each module card navigates to specific route on click
- Smooth page transition with Framer Motion
- No confirmation dialogs (direct navigation)

## Visual Design

### Animations

- **Page Load:** Fade-in with 0.5s duration
- **Module Cards:** Staggered animation (each card delays by index * 0.1s)
- **Hover States:** Scale to 1.02x on hover, 0.98x on tap
- **XP Feed:** Slide-in animation for each activity item

### Color Scheme

**Module Colors (Tailwind):**
- Discipline: Red 500/Orange 500
- Study: Blue 500/Indigo 500
- Fitness: Green 500/Emerald 500
- Mind: Yellow 500/Amber 500
- Creator: Pink 500/Rose 500
- Sentinel: Cyan 500/Blue 500

**Gradient Backgrounds:**
Each card has a subtle gradient from module color to secondary:
```css
from-[color]-500/10 to-[secondary]-500/10
```

## Responsive Design

**Mobile (< 640px):**
- Single column module grid
- Large touch targets (56px minimum)
- Bottom padding for mobile nav (pb-20)
- Full-width cards
- Font scaling: text-sm → text-base

**Tablet (640px - 1024px):**
- 2-column grid
- Medium padding (px-4)
- Larger font sizes

**Desktop (> 1024px):**
- 3-column grid
- Max-width container (max-w-6xl)
- Padding: py-8 (top/bottom)
- Bottom padding removed (lg:pb-8)

## Keyboard Shortcuts

None for Dashboard. All navigation via mouse/touch.

## Accessibility

- Icons paired with text labels
- Sufficient color contrast (WCAG AA)
- Touch targets minimum 44px (mobile)
- Tooltip provider for additional context (planned)
- Semantic HTML structure

## Error Handling

**Loading State:**
```typescript
if (isLoading) {
  return (
    <div className="min-h-screen flex items-center justify-center">
      <Loader2 className="h-8 w-8 animate-spin" />
    </div>
  );
}
```

**Not Authenticated:**
Automatic redirect to `/auth` page (handled by useEffect)

## Performance Optimizations

- Real-time Convex queries auto-sync
- Module grid uses motion wrapper for efficient animations
- No unnecessary re-renders (query dependency tracking)
- Lazy load module components (imported only when needed)

## Related Pages

- **Parent:** Landing page (if not authenticated)
- **Child Pages:** All six module pages
- **Settings:** Not directly linked (accessible via Empire or sidebar)

## Future Enhancements

1. **Customizable Dashboard:**
   - Allow users to hide/show modules
   - Drag-to-reorder module cards
   - Widget customization (check-in, feed, etc.)

2. **Advanced Stats:**
   - Quick stat cards: Streaks, Level, XP
   - This week's summary
   - Performance trends

3. **Smart Navigation:**
   - AI recommendation: "Your fitness streak is 5 days, go longer!"
   - Most-visited module highlight
   - Time-based suggestions: "Morning is your study time"

4. **Module Quick Actions:**
   - Log workout button on Fitness card
   - Create note on Study card
   - Add task on Discipline card

## Technical Stack

- **Framework:** React 18 + TypeScript
- **UI Library:** shadcn/ui components
- **Animations:** Framer Motion
- **Backend:** Convex real-time queries
- **Notifications:** Sonner toast system
- **Responsive:** Tailwind CSS grid system
