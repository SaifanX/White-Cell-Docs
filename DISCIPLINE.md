# Discipline Module

**File Path:** `src/pages/Discipline.tsx` (1,470 lines)

## Overview

The Discipline Engine is a comprehensive daily habit and goal management system centered around building unbreakable streaks and maintaining commitment. It uniquely combines practical productivity tools (Pomodoro, task management, goal tracking) with existential motivation through the "Death Clock" (Memento Mori) feature—a constant visual reminder of mortality to catalyze action.

## Page Purpose

- **Primary Function:** Daily habit and routine management with streak tracking
- **Target Users:** All users (core module, essential to platform)
- **Core Feature:** Death Clock (Memento Mori) + Streak System
- **Integration:** Unified calendar, AI routine generation, Pomodoro timer, reward system

## Key Features

### 1. Daily Task Management

**Create Task:**
```typescript
{
  title: string,
  category: "routine" | "focus" | "pmo-blocker",
  difficulty: 1-5 (default: 3),
  date: Date,
  completed: boolean,
  streak: number,
  pomodoroCount: number,
  energyLevel: 1-5 (optional)
}
```

**Task Categories:**
- **Routine:** Regular daily habits (meditation, cold shower, journaling)
- **Focus:** Deep work tasks (coding, writing, studying)
- **PMO-Blocker:** Accountability tasks for no-PMO goals

**Task Operations:**
- Create with title, category, difficulty
- Mark complete/incomplete (updates streak)
- Delete with confirmation
- Update difficulty (adaptive)
- Track energy level (1-5 scale)
- View completion count (X/Y completed today)

**Task Visualization:**
- Difficulty badges (1-5 stars)
- Pomodoro count (number of pomodoro sessions)
- Completion status (checkbox)
- Time remaining estimate
- Color-coded by category

### 2. Pomodoro Timer Integration

**Start Session:**
```typescript
{
  taskId: string (optional),
  duration: number (minutes, default: 25),
  startTime: Date,
  completed: boolean,
  endTime: Date (optional)
}
```

**Pomodoro Features:**
- 25-minute focus sessions (customizable)
- Multiple sessions per day allowed
- Link to specific task (update task's pomodoro count)
- Pause/resume functionality
- Sound notification on completion
- Auto-rest period (5 minutes)

**XP Integration:**
- Complete session: 10 XP (via internal scheduler)
- Task linked: +5 XP bonus
- Streak: +2 XP per consecutive pomodoro day

### 3. Habit & Routine Templates

**Habit Template:**
```typescript
{
  name: string,
  description: string,
  tasks: [
    {
      title: string,
      category: string,
      difficulty: 1-5
    }
  ]
}
```

**Create Habit Template:**
- Define multiple tasks
- Set difficulty per task
- Name and describe habit
- Save for reuse
- Export as JSON
- Copy to clipboard

**Routine Template:**
```typescript
{
  name: string,
  description: string,
  steps: [
    {
      title: string,
      duration: number (minutes)
    }
  ]
}
```

**Create Routine Template:**
- Add step-by-step tasks with durations
- Drag-to-reorder steps
- Delete individual steps
- Show total estimated duration
- Generate ICS for calendar export

**AI Routine Generation:**
- Select routine type: morning, evening, workout, study, productivity, wellness
- Generate complete routine with 5-10 steps
- AI estimates realistic durations
- Review and customize
- Save directly to database

**Template Application:**
- Apply habit template to today: creates all tasks
- Apply routine template: creates scheduled events
- Modify tasks before applying
- Track which template was applied

### 4. Study Goals & Milestones

**Create Goal:**
```typescript
{
  title: string,
  description: string,
  category: string,
  targetDate: Date,
  progress: 0-100,
  completed: boolean
}
```

**Goal Features:**
- Cross-domain goals (discipline, fitness, study, mind, creator)
- Progress tracking (0-100%)
- Target date with countdown
- Increment progress (+10%, +25%, custom)
- Completion notifications
- Goal completion: 100 XP award

### 5. Death Clock (Memento Mori)

**The Ultimate Motivation Engine:**
- Visual countdown showing remaining lifespan
- Based on current age, life expectancy, current date
- Shows: Years, Months, Days, Hours, Minutes remaining
- Conic gradient circle: filled (lived) → unfilled (remaining)
- Percentage of life already lived prominently displayed

**Display:**
```
68 years, 4 months, 21 days, 17 hours, 42 minutes remaining
██████████░░░░░░░░░ 47.3% lived
```

**Motivational Quotes:**
- Rotating Stoic and existential quotes
- "Memento Mori—Remember, you must die"
- "Your time is limited, use it well"
- Quotes from Marcus Aurelius, Seneca, Steve Jobs
- Refresh on interaction or time interval (5 min)

**Philosophical Integration:**
- Forces honest daily assessment: "Am I spending time on what matters?"
- Creates urgency for prioritization
- Aligns with Stoic philosophy
- Emotionally impactful without nihilism
- Visible on dashboard for constant awareness

### 6. Reward System & Achievement Badges

**Default Rewards (Unlocked by Streak):**

| Badge | Streak | Description |
|-------|--------|-------------|
| First Steps | 1 day | Complete your first task |
| Week Warrior | 7 days | Maintain 7-day streak |
| Fortnight Fighter | 14 days | Maintain 14-day streak |
| Monthly Master | 30 days | Maintain 30-day streak |
| Quarterly Champion | 90 days | Maintain 90-day streak |
| Centurion | 100 days | Maintain 100-day streak |
| Half Year Hero | 180 days | Maintain 180-day streak |
| Annual Achiever | 365 days | Full year streak |

**Milestone Rewards:**

| Badge | Requirement | Description |
|-------|-------------|-------------|
| Pomodoro Pro | 50 pomodoros | Complete 50 sessions |
| Goal Getter | 5 goals | Complete 5 goals |
| Template Creator | 3 templates | Create 3 custom templates |
| Discipline Elite | 1000 tasks | Complete 1000 total tasks |

**Reward Unlocking:**
- Automatic when condition met
- Database updates with unlock date
- Visual change: "Locked 🔒" → "Unlocked! 🎉"
- XP award (25 XP per unlock)
- Notification and celebration

### 7. Streak System & Insurance

**Streak Mechanics:**
- Consecutive days of task completion
- Visual fire icon 🔥 with streak number
- Resets if user skips a day
- Notifications when approaching break
- Break analysis: log reason (tiredness, busy, illness, laziness)

**Streak Break Recording:**
```typescript
{
  taskId: string,
  date: Date,
  reason: string (optional),
  notes: string (optional)
}
```

**Streak Insurance (Planned):**
- Costs 50 XP to purchase
- Protects one day's break (activates automatically)
- Non-renewable per purchase
- Prevents perfectionist burnout
- Enables strategic skip days

### 8. Advanced Features Tab

**Features:**

| Feature | Status | Description |
|---------|--------|-------------|
| Streak Insurance | Planned | Buy streak protection |
| Time Capsule | Planned | Message to future self |
| Accountability Partners | Planned | Partner matching system |
| Habit Marketplace | Planned | Buy/sell templates |
| Progress NFTs | Planned | Blockchain achievement |
| Reverse Bucket List | Planned | Track eliminated habits |
| Energy Forecasting | Planned | AI energy prediction |
| AI Roast Mode | Planned | Brutal accountability |

### 9. Unified Calendar Integration

**Calendar Features:**
- Display all discipline events
- Tasks as calendar events
- Pomodoro sessions marked
- Goal deadlines highlighted
- Routine events scheduled
- Color-coded by type

**Calendar Export:**
- Generate ICS files (iCalendar format)
- Routine templates export as recurring events
- Import to Google Calendar, Outlook, Apple Calendar
- Sync enable with external calendars

### 10. Habit Stacks

**Stack System:**
```typescript
{
  name: string,
  description: string,
  habits: [
    {
      taskId: string,
      order: number
    }
  ]
}
```

**Benefits:**
- Group related habits
- Enforce sequential execution
- Build momentum (harder to stop once started)
- Leverage habit loops

**Example Stack:**
```
Morning Stack:
  1. Meditation (5 min)
  2. Cold shower (3 min)
  3. Breakfast (10 min)
  4. Review goals (2 min)
  Total: ~20 minutes
```

### 11. Heatmap Visualization

**GitHub-Style Heatmap:**
- Daily task completion shown as grid
- Light (0 tasks) to dark (all completed)
- Click date to see details
- Mouse hover shows completion count
- Filter by date range
- Identify patterns (Monday weak, weekend strong, etc.)

### 12. Adaptive Difficulty System

**Dynamic Adjustment:**
- Increase difficulty (+1) if user completes easily
- Decrease difficulty (-1) if user struggles
- Bounds: 1-5 scale
- Adjustable anytime (before task or after)
- Creates personalized challenge level

### 13. Energy Level Tracking

**Post-Task Energy Logging:**
- Rate energy expenditure 1-5 immediately after task
- Identify high-drain vs energizing tasks
- Correlate with circadian rhythms
- Optimize schedule around energy peaks

## UI Layout

**Tab Structure (Swipe Navigation):**

```
Discipline Tabs:
├── Dashboard (Death Clock + Tasks)
├── Daily Tasks
├── Habits & Templates
├── Goals
├── Rewards
├── Advanced Features
├── Calendar
├── AI Coach
```

## State Management

**Major State Variables:**

```typescript
// Daily tasks
const [newTask, setNewTask] = useState("");
const [category, setCategory] = useState("routine");
const [difficulty, setDifficulty] = useState(3);

// Templates
const [templateName, setTemplateName] = useState("");
const [routineTemplateName, setRoutineTemplateName] = useState("");
const [routineTemplateSteps, setRoutineTemplateSteps] = useState([]);

// Goals
const [goalTitle, setGoalTitle] = useState("");
const [goalDescription, setGoalDescription] = useState("");
const [goalCategory, setGoalCategory] = useState("discipline");
const [goalTargetDate, setGoalTargetDate] = useState("");

// Death Clock
const [currentAge, setCurrentAge] = useState(25);
const [lifeExpectancy, setLifeExpectancy] = useState(80);
const [currentQuoteIndex, setCurrentQuoteIndex] = useState(0);
```

## Convex Queries & Mutations

**Queries:**
- `api.tasks.getDailyTasks` - Today's tasks
- `api.discipline.getHabitStacks` - Habit stacks
- `api.discipline.getHabitTemplates` - Saved templates
- `api.discipline.getGoals` - All goals
- `api.discipline.getRewards` - Achievements
- `api.discipline.getStreakBreaks` - Break analysis
- `api.discipline.getHabitHeatmapData` - Heatmap data

**Mutations:**
- `api.tasks.createTask` - Create task
- `api.tasks.toggleTask` - Mark complete/incomplete
- `api.tasks.deleteTask` - Delete task
- `api.discipline.createGoal` - Create goal
- `api.discipline.updateGoalProgress` - Update progress
- `api.discipline.createHabitTemplate` - Save template
- `api.discipline.applyTemplate` - Apply template
- `api.discipline.unlockReward` - Unlock achievement

## XP & Gamification

**XP Awards:**
- Complete task: 5-50 XP (based on difficulty)
- Complete Pomodoro: 10 XP
- Unlock reward: 25 XP
- Complete goal: 100 XP
- Create template: 15 XP (creativity bonus)
- Maintain streak: +2 XP per day
- Achieve streak milestone: 100-500 XP

**Streaks:**
- Task completion streak
- Pomodoro streak
- Goal completion streaks per category

**Badges:**
- "Discipline Initiate" (first task)
- "Habit Stacker" (create habit stack)
- "Template Master" (create 5 templates)
- "Death Clock Warrior" (use for 30 days)

## Keyboard Shortcuts

- `Ctrl+T`: Create new task
- `Ctrl+G`: Create new goal
- `Ctrl+P`: Start Pomodoro
- `Alt+← →`: Navigate tabs
- `M`: Toggle Memento Mori (Death Clock) visibility

## Mobile Features

- **Pull-to-Refresh:** Swipe to refresh tasks
- **Haptic Feedback:** Task completion, button clicks
- **Swipe Navigation:** Between tabs
- **Bottom Sheet:** Modals for task/goal creation

## Accessibility

- Large buttons for task operations
- High contrast for status colors
- Keyboard navigation for all features
- Screen reader support for Death Clock numbers
- Color not only indicator (text + icons)

## Performance Optimizations

- Real-time Convex queries auto-sync
- Task list virtualization for large lists
- Heatmap renders once (cached on date range change)
- Scheduler manages async XP awards (no blocking)

## Related Components

- **PomodoroTimer:** Integrated timer
- **UnifiedCalendar:** Event display
- **AIChatbot:** Motivation and advice

## Integration Points

1. **Study Module:** Link study goals to discipline goals
2. **Fitness Module:** Log workouts as discipline tasks
3. **Mind Module:** Meditation tracked as discipline
4. **Creator Module:** Content creation goals
5. **Sentinel AI:** Accountability coaching
6. **XP System:** All XP awards through discipline tasks

## Future Features

1. **Advanced Streaks:**
   - Insurance system fully implemented
   - Streak milestone celebrations
   - Public streak badges

2. **Social Features:**
   - Accountability partnerships
   - Challenge friends (compete on streaks)
   - Leaderboards

3. **AI Enhancements:**
   - Roast mode (brutal feedback)
   - Energy forecasting
   - Optimal schedule generation

4. **Marketplace:**
   - Buy/sell habit templates
   - Community sharing
   - Paid premium templates

## Technical Stack

- **Frontend:** React 18, TypeScript
- **Backend:** Convex (mutations, queries, scheduler)
- **AI:** Groq LLM (routine generation)
- **Real-time:** Convex auto-sync queries
- **Scheduler:** Convex internal scheduler (XP awards)
- **UI:** shadcn/ui components
- **Animations:** Framer Motion
- **Database:** Convex (Death Clock uses local state)
