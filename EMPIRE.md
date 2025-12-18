# Empire Page (Command Center)

**File Path:** `src/pages/Empire.tsx` (611 lines)

## Overview

The Empire page is the personalized command center dashboard that serves as the primary hub after the Dashboard. It displays comprehensive user statistics, achievements, streaks, level progression, daily challenges, and an interactive calendar. The Empire metaphor positions the user as a commander building and managing their life empire through consistent discipline.

## Page Purpose

- **Primary Function:** Unified stats and progress command center
- **Target Users:** All authenticated users (post-Dashboard redirect)
- **Key Actions:** View stats, complete daily check-in, accept challenges
- **Core Metaphor:** Building personal empire through daily discipline
- **Update Frequency:** Real-time (Convex queries)

## Main Statistics Display

### Power Index (Top Banner)

**Comprehensive Score Calculation:**

```typescript
const powerIndex = calculatePowerIndex({
  level: user.level,
  totalXP: user.totalXP,
  streaks: {
    taskStreak: stats.taskStreak.current,
    workoutStreak: stats.workoutStreak.current,
    studyStreak: stats.studyStreak.current,
  },
  achievements: user.achievements.length,
  consistency: calculateConsistency(user.activityHistory),
  discipline: calculateDisciplineScore(user.completedTasks)
});

// powerIndex: 0-10000
// Formula: Level*200 + (XP/100) + (streaks*50) + (achievements*10) + (consistency*100) + (discipline*150)
```

**Visual Display:**
- Large numeric value: "⚔️ POWER INDEX: 4,287"
- Color-coded status:
  - 0-1000: "Awakening" (Gray)
  - 1001-2500: "Rising" (Blue)
  - 2501-5000: "Dominant" (Purple)
  - 5001-7500: "Unstoppable" (Orange)
  - 7501-10000: "Legendary" (Red/Gold)
- Progress bar to next milestone
- Daily change indicator: "+145 since yesterday"

### User Level & XP

**Level Progression System:**

```typescript
{
  currentLevel: number,
  totalXP: number,
  xpForCurrentLevel: number,
  xpToNextLevel: number,
  xpProgress: number (percentage),
  estimatedDaysToNextLevel: number
}
```

**Display:**
- Mega-large title: "LEVEL 42"
- XP bar: [████████░░░░░░░] 7,450/10,000 XP
- Percentage: "(74.5%)"
- Estimated time: "→ 12 days to Level 43"
- Recent XP: "Recent gains: +850 XP"

### Leaderboard Position

**Global Ranking (if leaderboards enabled):**
```typescript
{
  globalRank: number,
  globalPercentile: number,
  friendsRank: number,
  weeklyRank: number,
  monthlyRank: number
}
```

**Display:**
- "🏆 Global Rank: #1,247 (Top 2.1%)"
- "Friends Rank: #3 of 12"
- Badges: "🥇 Weekly Champion" (if rank #1 this week)

## Streak Management System

### Three Core Streaks

**1. Task Streak (Discipline):**
```typescript
{
  current: number (days),
  record: number (best),
  lastCompletedDate: Date,
  nextDeadline: Date,
  percentageToday: number (0-100, tasks completed today),
  status: "active" | "broken" | "recovering"
}
```

- Display: "🔥 42 DAY TASK STREAK"
- Record: "(Record: 89 days)"
- Visual: Flame emoji gets bigger with streaks
- Daily progress: "🔲 13/15 daily tasks completed (86%)"
- Warning if > 1 task remaining

**2. Workout Streak (Fitness):**
```typescript
{
  current: number (days),
  record: number (best),
  lastCompletedDate: Date,
  status: "active" | "broken" | "recovering"
}
```

- Display: "💪 18 DAY WORKOUT STREAK"
- Record: "(Record: 52 days)"
- Workout types logged today
- Progress: "1/1 workouts logged today ✓"

**3. Study Streak (Knowledge):**
```typescript
{
  current: number (days),
  record: number (best),
  lastCompletedDate: Date,
  studyMinutesGoal: number,
  studyMinutesCompleted: number,
  status: "active" | "broken" | "recovering"
}
```

- Display: "📚 28 DAY STUDY STREAK"
- Record: "(Record: 165 days)"
- Progress: "45/60 minutes studied today"
- Status bar showing percentage

### Streak Recovery System

**If Streak Broken:**
- Display: "❌ Streak broken on [date]"
- Recovery option: "🔄 Reclaim Streak (✨ 500 XP)"
- Warning: "You have 7 days to reclaim"
- Cost: 500 XP to restore

**Prevention System:**
- Alert if streak at risk (< 2 items remaining)
- Motivational message at 90%+ completion
- Quick-add buttons for remaining items

## Daily Check-In Widget

**Embedded Component (Mini Version):**

```typescript
{
  checkInDate: Date,
  completed: boolean,
  mood: "terrible" | "bad" | "neutral" | "good" | "amazing",
  energyLevel: number (1-10),
  motivation: number (1-10),
  focusQuality: number (1-10),
  notes: string,
  xpGained: number
}
```

**Display:**
- "✓ Daily Check-In Complete" (if done)
- Emoji reaction options: 😭😞😐😊🤩
- Input: "Energy: [████░░░░░░] 6/10"
- Input: "Motivation: [█████████░░] 9/10"
- Input: "Focus: [███░░░░░░░░] 3/10"
- Notes field: "How was your day?"
- Submit button: "Log Check-In (+50 XP)"
- Can edit until midnight

## Activities & Challenges Tab

### Daily Challenges

**3-5 rotating challenges daily:**

```typescript
[
  {
    id: string,
    title: string,
    description: string,
    reward: number (XP),
    difficulty: "easy" | "medium" | "hard",
    category: "study" | "fitness" | "discipline" | "mind" | "creator",
    completed: boolean,
    expiresAt: Date,
    hint: string (optional)
  }
]
```

**Challenge Examples:**

| Challenge | Category | Reward | Difficulty |
|-----------|----------|--------|------------|
| "Complete 5 Study Flashcards" | Study | 100 XP | Easy |
| "Do 20 Minutes of Workouts" | Fitness | 150 XP | Medium |
| "Solve 3 Chess Puzzles" | Mind | 120 XP | Medium |
| "Write 500 Word Essay" | Creator | 200 XP | Hard |
| "Complete All Daily Tasks" | Discipline | 250 XP | Medium |

**UI:**
- Challenge card with icon, title, reward
- Click to expand details + hint
- "Claim Reward" button (if completed)
- Progress bar if partially complete
- Timer showing time remaining
- Notification when completed

### Weekly Challenges

**5 challenges per week (harder):**
- Higher XP rewards (300-500 XP)
- Multi-day completion required
- Special badges upon completion
- Reset every Sunday/Monday

**Example:**
- "Maintain 7-day Task Streak" (500 XP, 🔥 Badge)
- "Complete 150 Minutes of Workouts" (400 XP)
- "Create Content in Creator Hub" (350 XP)

## Calendar Heatmap

### Activity Heatmap Visualization

**Grid Calendar:**
```
Mo Tu We Th Fr Sa Su
                   1
 2  3  4  5  6  7  8
 9 10 11 12 13 14 15
[Shows last 12 weeks]
```

**Color Intensity:**
- White/empty: No activity
- Light green: 1-3 tasks completed
- Medium green: 4-7 tasks completed
- Dark green: 8+ tasks completed (perfect day)
- Gray: Streak broken on this day
- Red (rare): Negative events (skipped workouts)

**Interactivity:**
- Hover shows day summary: "Tue, Dec 12: 12 tasks, 45 min study, 1 workout"
- Click day to see details: Task list, XP earned, check-in mood
- Toggle view: Past 12 weeks / 6 months / 1 year

**Statistics:**
- "Longest streak: 42 days"
- "Average tasks/day: 8.3"
- "Perfect days: 156/365 (42%)"
- "Current contribution: 12 days in a row"

## State Management

**Empire State:**
```typescript
const [empireData, setEmpireData] = useState({
  user: { level: 42, totalXP: 42500, streak: 42, achievements: 28 },
  powerIndex: 4287,
  streaks: { task: 42, workout: 18, study: 28 },
  dailyCheckIn: { completed: true, mood: "good", energy: 7 },
  dailyChallenges: [],
  weeklyChallenges: [],
  activityData: [],
  rank: { global: 1247, friends: 3 },
  recentXP: [{ amount: 100, source: "Task completed", date: Date }]
});

const [selectedDay, setSelectedDay] = useState<Date | null>(null);
const [showDetails, setShowDetails] = useState(false);
```

**Queries:**
```typescript
const user = useQuery(api.users.getUser, { userId: currentUserId });
const stats = useQuery(api.users.getStats, { userId: currentUserId });
const challenges = useQuery(api.challenges.getDailyChallenges, {});
const activityData = useQuery(api.users.getActivityHeatmap, { 
  weeks: 12 
});
```

## Features

### Real-Time Updates

```typescript
useEffect(() => {
  const unsubscribe = subscribeToUserStats((newStats) => {
    setEmpireData(prev => ({
      ...prev,
      streaks: newStats.streaks,
      powerIndex: recalculatePowerIndex(newStats)
    }));
  });
  
  return () => unsubscribe();
}, []);
```

- Streaks update when tasks completed
- XP changes reflect instantly
- Challenges update in real-time
- New achievements pop up
- Notifications for milestones

### Animations

- Power index number animates when XP gained
- Streak counters animate on task completion
- Challenge completion animation (confetti)
- Heatmap cells glow on hover
- Level-up animation: Fireworks + screen shake

### Mobile Responsiveness

**Mobile (< 640px):**
- Vertical layout (stats stacked)
- Single column for streaks
- Swipeable tabs for challenges/heatmap
- Compact statistics display
- Touch-optimized buttons

**Desktop (> 1024px):**
- Side-by-side layout with sidebar
- Full heatmap display
- Multi-window challenges
- Detailed tooltips

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `D` | Daily check-in |
| `C` | View challenges |
| `H` | View heatmap |
| `S` | View streaks |
| `L` | View leaderboard |
| `A` | View achievements |

## Integration with Other Modules

**Cross-Module Tracking:**
- Task completion updates task streak
- Workout logging updates workout streak
- Study session completion updates study streak
- Any XP-earning action updates power index
- Achievements synced from all modules
- Daily challenges pull from all module categories

**Embedded Components:**
- `DailyCheckIn.tsx` - Check-in widget
- `StreakWarning.tsx` - Streak alerts
- `XPFeedback.tsx` - XP notifications
- `AchievementBadges.tsx` - Achievement display
- `UnifiedCalendar.tsx` - Heatmap calendar

## Data Persistence

**Database Schema:**
```typescript
{
  userId: string,
  level: number,
  totalXP: number,
  powerIndex: number,
  streaks: {
    tasks: { current: number, record: number, lastDate: Date },
    workouts: { current: number, record: number, lastDate: Date },
    study: { current: number, record: number, lastDate: Date }
  },
  achievements: string[] (achievement IDs),
  badges: string[] (badge IDs),
  dailyCheckIns: Array<{ date: Date, mood: string, energy: number }>,
  activityHistory: Array<{ date: Date, tasksCompleted: number, xpEarned: number }>,
  leaderboardRank: number,
  weeklyRank: number
}
```

## Performance Optimization

- Virtualize long lists (challenges, heatmap)
- Cache heatmap data (recalculate only weekly)
- Lazy load leaderboard data
- Debounce real-time updates (500ms)
- Memoize streak calculations
- Preload challenge images

## Accessibility

- Semantic HTML for layout
- Color + text for status indicators
- Sufficient contrast (WCAG AA)
- ARIA labels for icons
- Keyboard navigation (Tab through sections)
- Screen reader support for heatmap
- Focus indicators on all interactive elements

## Future Features

- [ ] Leaderboard filters (friends, global, by category)
- [ ] Custom challenges (user-created)
- [ ] Seasonal events and tournaments
- [ ] Empire customization (banners, titles)
- [ ] Cooperative streaks (with friends)
- [ ] Achievement milestones notifications
- [ ] Trend analysis (weekly insights)
- [ ] Predictive success rates

## Technical Stack

- **Data:** Convex real-time queries
- **Animations:** Framer Motion (particles, confetti)
- **Calendar:** Custom React component + date-fns
- **Charts:** Optional: Recharts for statistics
- **Styling:** Tailwind CSS with gradient backgrounds
- **State:** React hooks + Convex
- **Performance:** React.memo, useMemo for expensive calculations
