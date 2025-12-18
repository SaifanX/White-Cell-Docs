# Convex API Reference - Complete Documentation

**Version:** 1.0
**Framework:** Convex (Backend as a Service)
**Last Updated:** December 18, 2025

---

## Overview

Complete API reference for all Convex queries, mutations, and actions in the White Cell Protocol application. Each function signature includes parameters, return types, error handling, and usage examples.

---

## Query Functions

Queries are read-only operations that fetch data from the database. They run in real-time and update client state automatically when data changes.

### Study Module Queries

#### `getFlashcardsDueForReview`

Retrieves flashcards due for spaced repetition review today.

```typescript
Query: convex/study.ts

Parameters:
- userId: ID (required)

Returns:
Array<{
  _id: ID,
  question: String,
  answer: String,
  easeFactor: Number,
  interval: Number,
  nextReviewDate: Date,
  status: "new" | "learning" | "review" | "mastered"
}>

Error Cases:
- UserNotFound: User ID doesn't exist
- Unauthorized: User not authenticated

Example Usage:
const flashcards = await convex.query(api.study.getFlashcardsDueForReview, {
  userId: currentUser._id
});

Performance:
- Complexity: O(n) where n = due flashcards
- Caching: Real-time, auto-updates on changes
- Typical response time: <100ms
```

---

#### `getNotes`

Retrieves user's notes with optional filtering.

```typescript
Query: convex/study.ts

Parameters:
- userId: ID (required)
- subject?: String (optional, filter by subject)
- searchQuery?: String (optional, full-text search)
- limit?: Number (optional, default: 50)
- offset?: Number (optional, for pagination)

Returns:
{
  notes: Array<{
    _id: ID,
    title: String,
    subject: String,
    wordCount: Number,
    tags: Array<String>,
    createdAt: Date,
    updatedAt: Date,
    isPinned: Boolean
  }>,
  totalCount: Number,
  hasMore: Boolean
}

Error Cases:
- InvalidSubject: Unknown subject category
- SearchLimit: Query string too long (max 500 chars)

Example Usage:
const { notes, totalCount } = await convex.query(api.study.getNotes, {
  userId: currentUser._id,
  subject: "Mathematics",
  limit: 25
});

Performance:
- Complexity: O(log n) with indexes
- Response time: <150ms for typical queries
- Supports pagination for large datasets
```

---

#### `getQuizzesForSubject`

Retrieves quiz history for a specific subject.

```typescript
Query: convex/study.ts

Parameters:
- userId: ID (required)
- subject: String (required)
- limit?: Number (optional, default: 20)

Returns:
Array<{
  _id: ID,
  title: String,
  score: Number,
  correctAnswers: Number,
  totalQuestions: Number,
  difficulty: "easy" | "medium" | "hard",
  timeSpent: Number,
  createdAt: Date
}>

Example Usage:
const quizzes = await convex.query(api.study.getQuizzesForSubject, {
  userId: currentUser._id,
  subject: "Physics",
  limit: 10
});
```

---

#### `getStudyStats`

Retrieves comprehensive study statistics.

```typescript
Query: convex/study.ts

Parameters:
- userId: ID (required)
- dateRange?: { start: Date, end: Date } (optional)

Returns:
{
  totalNotes: Number,
  totalFlashcards: Number,
  masteredFlashcards: Number,
  totalQuizzesTaken: Number,
  averageQuizScore: Number,
  totalStudyHours: Number,
  streakDays: Number,
  subjectBreakdown: {
    [subject: String]: {
      noteCount: Number,
      flashcardCount: Number,
      averageScore: Number
    }
  }
}

Example Usage:
const stats = await convex.query(api.study.getStudyStats, {
  userId: currentUser._id,
  dateRange: {
    start: new Date(Date.now() - 30*24*60*60*1000),
    end: new Date()
  }
});
```

---

### Fitness Module Queries

#### `getWorkoutHistory`

Retrieves user's workout history with filtering options.

```typescript
Query: convex/fitness.ts

Parameters:
- userId: ID (required)
- type?: "strength" | "cardio" | "flexibility" | "sports" (optional)
- startDate?: Date (optional)
- endDate?: Date (optional)
- limit?: Number (optional, default: 30)

Returns:
Array<{
  _id: ID,
  date: Date,
  type: String,
  name: String,
  duration: Number,
  calories: Number,
  intensity: String,
  exercises: Array<Exercise>
}>

Example Usage:
const workouts = await convex.query(api.fitness.getWorkoutHistory, {
  userId: currentUser._id,
  type: "strength",
  startDate: new Date(Date.now() - 7*24*60*60*1000),
  limit: 20
});
```

---

#### `getMeasurements`

Retrieves body measurement history for trend analysis.

```typescript
Query: convex/fitness.ts

Parameters:
- userId: ID (required)
- limit?: Number (optional, default: 50)

Returns:
Array<{
  _id: ID,
  date: Date,
  weight: Number,
  unit: "kg" | "lbs",
  bodyFatPercentage?: Number,
  muscleMass?: Number,
  chest?: Number,
  waist?: Number,
  hips?: Number
}>

Note: Ordered by date DESC (most recent first) for trend analysis.

Example Usage:
const measurements = await convex.query(api.fitness.getMeasurements, {
  userId: currentUser._id,
  limit: 12
});
```

---

#### `getPersonalRecords`

Retrieves all-time personal records.

```typescript
Query: convex/fitness.ts

Parameters:
- userId: ID (required)
- exercise?: String (optional, filter by specific exercise)

Returns:
Array<{
  _id: ID,
  exercise: String,
  value: Number,
  unit: String,
  date: Date,
  previousPR?: Number,
  improvement?: Number
}>

Example Usage:
const prs = await convex.query(api.fitness.getPersonalRecords, {
  userId: currentUser._id,
  exercise: "Bench Press"
});
```

---

#### `getTodayNutrition`

Retrieves nutritional data for the current day.

```typescript
Query: convex/fitness.ts

Parameters:
- userId: ID (required)
- date?: Date (optional, defaults to today)

Returns:
{
  meals: Array<Meal>,
  dailyTotals: {
    calories: Number,
    protein: Number,
    carbs: Number,
    fat: Number,
    fiber?: Number
  },
  dailyGoals: {
    calories: Number,
    protein: Number,
    carbs: Number,
    fat: Number
  },
  remainingCalories: Number,
  macroBreakdown: {
    proteinPercentage: Number,
    carbsPercentage: Number,
    fatsPercentage: Number
  }
}

Example Usage:
const nutrition = await convex.query(api.fitness.getTodayNutrition, {
  userId: currentUser._id
});
```

---

#### `getFitnessStats`

Retrieves comprehensive fitness statistics.

```typescript
Query: convex/fitness.ts

Parameters:
- userId: ID (required)

Returns:
{
  totalWorkouts: Number,
  totalCaloriesBurned: Number,
  averageWorkoutDuration: Number,
  currentWeightTrend: "up" | "down" | "stable",
  personalRecordCount: Number,
  workoutStreak: Number,
  favoriteExercises: Array<String>,
  weeklyActivityBreakdown: {
    [day: String]: Number (workouts that day)
  }
}

Example Usage:
const stats = await convex.query(api.fitness.getFitnessStats, {
  userId: currentUser._id
});
```

---

### Mind Module Queries

#### `getChessPuzzleProgress`

Retrieves chess puzzle solving statistics.

```typescript
Query: convex/mind.ts

Parameters:
- userId: ID (required)

Returns:
{
  totalPuzzlesSolved: Number,
  successRate: Number,
  averageTimePerPuzzle: Number,
  difficultyDistribution: {
    easy: Number,
    medium: Number,
    hard: Number,
    veryHard: Number
  },
  recentPuzzles: Array<ChessPuzzle>,
  ratingTrend: {
    current: Number,
    previous: Number,
    change: Number
  }
}

Example Usage:
const progress = await convex.query(api.mind.getChessPuzzleProgress, {
  userId: currentUser._id
});
```

---

#### `getCubeTimes`

Retrieves speedcubing session times.

```typescript
Query: convex/mind.ts

Parameters:
- userId: ID (required)
- cubeType: "2x2" | "3x3" | "4x4" | "pyraminx" (required)
- limit?: Number (optional, default: 50)

Returns:
{
  times: Array<{
    _id: ID,
    time: Number,
    scramble: String,
    date: Date,
    dnf: Boolean,
    plusTwo: Boolean
  }>,
  statistics: {
    bestTime: Number,
    averageOf5: Number,
    averageOf12: Number,
    averageOf50: Number,
    averageOf100: Number,
    totalSolves: Number,
    totalTime: Number,
    improvementRate: Number (% improvement over time)
  }
}

Example Usage:
const times = await convex.query(api.mind.getCubeTimes, {
  userId: currentUser._id,
  cubeType: "3x3",
  limit: 100
});
```

---

#### `getWisdomQuote`

Retrieves daily or random stoic wisdom quote.

```typescript
Query: convex/mind.ts

Parameters:
- date?: Date (optional, defaults to today)
- random?: Boolean (optional, get random instead of daily)

Returns:
{
  _id: ID,
  quote: String,
  author: String,
  source: String,
  reflection: String,
  theme: String,
  savedByCount: Number
}

Example Usage:
const quote = await convex.query(api.mind.getWisdomQuote, {
  date: new Date()
});
```

---

#### `getStrategies`

Retrieves uploaded strategy documents.

```typescript
Query: convex/mind.ts

Parameters:
- userId: ID (required)
- category?: String (optional)

Returns:
Array<{
  _id: ID,
  title: String,
  category: "chess" | "business" | "military" | "game_theory",
  description: String,
  pdfUrl: String,
  uploadedAt: Date,
  rating?: Number
}>

Example Usage:
const strategies = await convex.query(api.mind.getStrategies, {
  userId: currentUser._id,
  category: "chess"
});
```

---

### Discipline Module Queries

#### `getTodayTasks`

Retrieves today's task list.

```typescript
Query: convex/discipline.ts

Parameters:
- userId: ID (required)
- date?: Date (optional, defaults to today)

Returns:
Array<{
  _id: ID,
  title: String,
  category: String,
  completed: Boolean,
  priority: Number,
  dueTime?: String,
  completedAt?: Date
}>

Example Usage:
const tasks = await convex.query(api.discipline.getTodayTasks, {
  userId: currentUser._id
});
```

---

#### `getHabitStacks`

Retrieves all habit stacks with streak information.

```typescript
Query: convex/discipline.ts

Parameters:
- userId: ID (required)

Returns:
Array<{
  _id: ID,
  name: String,
  anchorHabit: String,
  habits: Array<String>,
  currentStreak: Number,
  longestStreak: Number,
  difficulty: String,
  completedDatesThisWeek: Number
}>

Example Usage:
const stacks = await convex.query(api.discipline.getHabitStacks, {
  userId: currentUser._id
});
```

---

#### `getPMOStats`

Retrieves PMO recovery statistics.

```typescript
Query: convex/discipline.ts

Parameters:
- userId: ID (required)

Returns:
{
  currentStreak: Number,
  longestStreak: Number,
  totalDaysTracked: Number,
  relapseCount: Number,
  averageEnergyLevel: Number,
  averageFocusLevel: Number,
  averageMoodLevel: Number,
  trendLine: "improving" | "declining" | "stable",
  motivationalMessage: String
}

Example Usage:
const stats = await convex.query(api.discipline.getPMOStats, {
  userId: currentUser._id
});
```

---

### Creator Module Queries

#### `getContentBoard`

Retrieves Kanban board content in columns.

```typescript
Query: convex/creator.ts

Parameters:
- userId: ID (required)

Returns:
{
  ideation: Array<ContentItem>,
  scripting: Array<ContentItem>,
  production: Array<ContentItem>,
  publishing: Array<ContentItem>
}

Where ContentItem includes:
- _id: ID
- title: String
- platform: Array<String>
- dueDate?: Date
- category?: String

Example Usage:
const board = await convex.query(api.creator.getContentBoard, {
  userId: currentUser._id
});
```

---

#### `getContentAnalytics`

Retrieves analytics for published content.

```typescript
Query: convex/creator.ts

Parameters:
- userId: ID (required)
- timeframe?: "week" | "month" | "year" (optional, default: "month")

Returns:
{
  totalPosts: Number,
  totalViews: Number,
  totalEngagement: Number (likes + comments + shares),
  averageViews: Number,
  averageEngagementRate: Number,
  topContent: Array<ContentItem>,
  platformBreakdown: {
    [platform: String]: {
      posts: Number,
      views: Number,
      engagement: Number
    }
  },
  trendLine: Array<{
    date: Date,
    views: Number,
    engagement: Number
  }>
}

Example Usage:
const analytics = await convex.query(api.creator.getContentAnalytics, {
  userId: currentUser._id,
  timeframe: "month"
});
```

---

### AI & Chat Queries

#### `getConversationHistory`

Retrieves AI conversation messages.

```typescript
Query: convex/ai.ts

Parameters:
- userId: ID (required)
- conversationId?: ID (optional, if not provided gets active conversation)
- limit?: Number (optional, default: 50)

Returns:
{
  conversationId: ID,
  topic: String,
  module: String,
  mode: "normal" | "research",
  messages: Array<{
    id: String,
    role: "user" | "assistant",
    content: String,
    timestamp: Date,
    tokens?: Number
  }>,
  context: Object
}

Example Usage:
const history = await convex.query(api.ai.getConversationHistory, {
  userId: currentUser._id,
  limit: 30
});
```

---

#### `getAllConversations`

Retrieves list of all user conversations.

```typescript
Query: convex/ai.ts

Parameters:
- userId: ID (required)
- limit?: Number (optional, default: 20)

Returns:
Array<{
  _id: ID,
  topic: String,
  module: String,
  messageCount: Number,
  createdAt: Date,
  updatedAt: Date,
  isPinned: Boolean,
  preview: String (first 100 chars)
}>

Example Usage:
const conversations = await convex.query(api.ai.getAllConversations, {
  userId: currentUser._id,
  limit: 50
});
```

---

#### `getContextMemory`

Retrieves stored context for AI personalization.

```typescript
Query: convex/ai.ts

Parameters:
- userId: ID (required)
- category?: String (optional)

Returns:
Array<{
  _id: ID,
  contextItem: String,
  category: "goals" | "preferences" | "activities" | "learning",
  confidence: Number,
  lastUsed: Date
}>

Example Usage:
const context = await convex.query(api.ai.getContextMemory, {
  userId: currentUser._id
});
```

---

## Mutation Functions

Mutations modify data in the database. They are not real-time and return confirmation of changes.

### Study Module Mutations

#### `createNote`

Creates a new note.

```typescript
Mutation: convex/study.ts

Parameters:
{
  userId: ID,
  title: String (required, 1-200 chars),
  content: String (required),
  subject: String (required),
  tags?: Array<String>,
  color?: String
}

Returns:
{
  success: Boolean,
  noteId: ID,
  message: String
}

Error Cases:
- InvalidTitle: Title empty or too long
- InvalidSubject: Unknown subject
- ContentTooLarge: Content exceeds 100KB
- UserNotFound: User doesn't exist
- QuotaExceeded: User hit note creation limit

Example Usage:
const result = await convex.mutation(api.study.createNote, {
  userId: currentUser._id,
  title: "Newton's Laws",
  content: "<h1>Newton's Laws</h1><p>...</p>",
  subject: "Physics",
  tags: ["mechanics", "forces"],
  color: "blue"
});

Validation:
- Title: 1-200 characters
- Content: Max 100KB
- Subject: Predefined categories only
- Tags: Max 10 tags, 30 chars each
```

---

#### `updateNote`

Updates an existing note.

```typescript
Mutation: convex/study.ts

Parameters:
{
  noteId: ID (required),
  userId: ID (required),
  title?: String,
  content?: String,
  subject?: String,
  tags?: Array<String>,
  isPinned?: Boolean
}

Returns:
{
  success: Boolean,
  updatedAt: Date,
  message: String
}

Error Cases:
- NoteNotFound: Note ID doesn't exist
- Unauthorized: User doesn't own note
- InvalidInput: Invalid field values

Example Usage:
const result = await convex.mutation(api.study.updateNote, {
  noteId: note._id,
  userId: currentUser._id,
  title: "Newton's Laws - Updated",
  isPinned: true
});
```

---

#### `deleteNote`

Deletes a note permanently.

```typescript
Mutation: convex/study.ts

Parameters:
{
  noteId: ID (required),
  userId: ID (required)
}

Returns:
{
  success: Boolean,
  message: String
}

Error Cases:
- NoteNotFound: Note doesn't exist
- Unauthorized: User doesn't own note

Example Usage:
const result = await convex.mutation(api.study.deleteNote, {
  noteId: note._id,
  userId: currentUser._id
});

Note: Deletion is permanent. Consider archiving instead.
```

---

#### `createFlashcard`

Creates a new flashcard.

```typescript
Mutation: convex/study.ts

Parameters:
{
  userId: ID (required),
  question: String (required, 1-500 chars),
  answer: String (required, 1-2000 chars),
  subject: String (required),
  difficulty: Number (1-5, default: 3),
  sourceNoteId?: ID
}

Returns:
{
  success: Boolean,
  flashcardId: ID,
  nextReviewDate: Date,
  message: String
}

Validation:
- Question: 1-500 characters
- Answer: 1-2000 characters
- Difficulty: 1-5 only

Example Usage:
const result = await convex.mutation(api.study.createFlashcard, {
  userId: currentUser._id,
  question: "What is photosynthesis?",
  answer: "Process by which plants convert light energy...",
  subject: "Biology",
  difficulty: 4
});
```

---

#### `reviewFlashcard`

Records a flashcard review with SM-2 calculation.

```typescript
Mutation: convex/study.ts

Parameters:
{
  flashcardId: ID (required),
  userId: ID (required),
  quality: Number (0-5, required), // 0=fail, 1=hard, 2=ok, 3=good, 4=easy, 5=trivial
  timeSpent?: Number (seconds)
}

Returns:
{
  success: Boolean,
  newInterval: Number,
  newEaseFactor: Number,
  nextReviewDate: Date,
  xpEarned: Number,
  message: String
}

SM-2 Algorithm Notes:
- Quality 0-2: Resets interval to 1 day, reduces easeFactor
- Quality 3-5: Increases interval, maintains or increases easeFactor
- easeFactor minimum: 1.3, maximum: 2.5

Example Usage:
const result = await convex.mutation(api.study.reviewFlashcard, {
  flashcardId: flashcard._id,
  userId: currentUser._id,
  quality: 4,
  timeSpent: 45
});

XP Rewards:
- Each review: 5-15 XP based on difficulty
- First review bonus: +10 XP
```

---

#### `generateQuiz`

Creates an AI-generated quiz from notes.

```typescript
Mutation: convex/study.ts

Parameters:
{
  userId: ID (required),
  subject: String (required),
  difficulty: "easy" | "medium" | "hard",
  questionCount: Number (5-50, default: 10),
  sourceNoteIds?: Array<ID>
}

Returns:
{
  success: Boolean,
  quizId: ID,
  questions: Array<QuizQuestion>,
  estimatedDuration: Number,
  message: String
}

Error Cases:
- InsufficientNotes: Not enough notes for quiz
- AIGenerationFailed: LLM request failed
- QuotaExceeded: User hit generation limit

Example Usage:
const result = await convex.mutation(api.study.generateQuiz, {
  userId: currentUser._id,
  subject: "Physics",
  difficulty: "hard",
  questionCount: 15
});

Rate Limiting:
- Free users: 5 quizzes/day
- Premium users: Unlimited
```

---

#### `submitQuiz`

Submits completed quiz and calculates results.

```typescript
Mutation: convex/study.ts

Parameters:
{
  quizId: ID (required),
  userId: ID (required),
  answers: Array<String> (required),
  timeSpent: Number (seconds)
}

Returns:
{
  success: Boolean,
  score: Number (0-100),
  correctAnswers: Number,
  totalQuestions: Number,
  xpEarned: Number,
  feedback: Array<String>,
  message: String
}

Example Usage:
const result = await convex.mutation(api.study.submitQuiz, {
  quizId: quiz._id,
  userId: currentUser._id,
  answers: ["A", "B", "C"],
  timeSpent: 180
});

XP Calculation:
- Base: 5 XP per correct answer
- Bonus: 10% if time < average
- Bonus: 10% if score > 80%
```

---

### Fitness Module Mutations

#### `logWorkout`

Creates a new workout entry.

```typescript
Mutation: convex/fitness.ts

Parameters:
{
  userId: ID (required),
  date: Date,
  type: "strength" | "cardio" | "flexibility" | "sports" (required),
  name?: String,
  duration: Number (minutes, required),
  calories: Number,
  intensity: "low" | "moderate" | "high" | "very-high" (required),
  exercises: Array<{
    name: String,
    sets: Number,
    reps: Number,
    weight?: Number,
    unit?: "kg" | "lbs"
  }>,
  notes?: String,
  templateId?: ID
}

Returns:
{
  success: Boolean,
  workoutId: ID,
  xpEarned: Number,
  streakUpdated: Boolean,
  message: String
}

Validation:
- Duration: 1-480 minutes (max 8 hours)
- Exercises: At least 1, max 20
- Calories: 0-5000

Example Usage:
const result = await convex.mutation(api.fitness.logWorkout, {
  userId: currentUser._id,
  date: new Date(),
  type: "strength",
  duration: 60,
  intensity: "high",
  exercises: [
    { name: "Bench Press", sets: 4, reps: 8, weight: 100, unit: "kg" },
    { name: "Squats", sets: 4, reps: 10, weight: 120, unit: "kg" }
  ]
});

XP Rewards:
- Base: 1 XP per minute
- Bonus: 5 XP if intensity is "high" or "very-high"
- Bonus: 10 XP if > 60 minutes
- Streak bonus: +5 XP if maintaining daily streak
```

---

#### `logMeasurement`

Records body measurements.

```typescript
Mutation: convex/fitness.ts

Parameters:
{
  userId: ID (required),
  date: Date,
  weight: Number (required),
  unit: "kg" | "lbs" (required),
  bodyFatPercentage?: Number,
  muscleMass?: Number,
  measurements?: {
    chest?: Number,
    waist?: Number,
    hips?: Number,
    thighs?: Number,
    biceps?: Number
  }
}

Returns:
{
  success: Boolean,
  measurementId: ID,
  weightTrend: "up" | "down" | "stable",
  message: String
}

Validation:
- Weight: 20-500 kg (44-1100 lbs)
- Body fat: 0-60%
- Measurements: Realistic values only

Example Usage:
const result = await convex.mutation(api.fitness.logMeasurement, {
  userId: currentUser._id,
  weight: 82,
  unit: "kg",
  bodyFatPercentage: 18.5,
  measurements: {
    waist: 85,
    chest: 105
  }
});
```

---

#### `logMeal`

Logs a meal with nutritional information.

```typescript
Mutation: convex/fitness.ts

Parameters:
{
  userId: ID (required),
  date: Date,
  mealType: "breakfast" | "lunch" | "dinner" | "snack" (required),
  time: String (HH:MM format),
  foods: Array<{
    name: String,
    quantity: Number,
    unit: String,
    calories: Number,
    protein: Number,
    carbs: Number,
    fat: Number,
    fiber?: Number,
    barcode?: String
  }> (required)
}

Returns:
{
  success: Boolean,
  mealId: ID,
  totalCalories: Number,
  macroBreakdown: {
    protein: Number,
    carbs: Number,
    fat: Number
  },
  message: String
}

Validation:
- At least 1 food item
- Quantities > 0
- Calories: 0-3000 per meal

Example Usage:
const result = await convex.mutation(api.fitness.logMeal, {
  userId: currentUser._id,
  mealType: "lunch",
  time: "12:30",
  foods: [
    {
      name: "Chicken Breast",
      quantity: 200,
      unit: "grams",
      calories: 330,
      protein: 62,
      carbs: 0,
      fat: 7
    }
  ]
});
```

---

### Discipline Module Mutations

#### `createTask`

Creates a daily task.

```typescript
Mutation: convex/discipline.ts

Parameters:
{
  userId: ID (required),
  date: Date,
  title: String (required, 1-100 chars),
  category: String (required),
  priority: Number (1-5, default: 3),
  dueTime?: String (HH:MM format)
}

Returns:
{
  success: Boolean,
  taskId: ID,
  message: String
}

Example Usage:
const result = await convex.mutation(api.discipline.createTask, {
  userId: currentUser._id,
  title: "Complete homework",
  category: "Work",
  priority: 5
});
```

---

#### `completeTask`

Marks a task as complete.

```typescript
Mutation: convex/discipline.ts

Parameters:
{
  taskId: ID (required),
  userId: ID (required)
}

Returns:
{
  success: Boolean,
  xpEarned: Number,
  completedAt: Date,
  message: String
}

XP Rewards:
- Base: 10 XP
- High priority bonus: +5 XP if priority 4-5

Example Usage:
const result = await convex.mutation(api.discipline.completeTask, {
  taskId: task._id,
  userId: currentUser._id
});
```

---

#### `createHabitStack`

Creates a compound habit stack.

```typescript
Mutation: convex/discipline.ts

Parameters:
{
  userId: ID (required),
  name: String (required, 1-100 chars),
  anchorHabit: String (required),
  habits: Array<String> (required, 2-10 habits),
  difficulty: "easy" | "medium" | "hard"
}

Returns:
{
  success: Boolean,
  stackId: ID,
  message: String
}

Example Usage:
const result = await convex.mutation(api.discipline.createHabitStack, {
  userId: currentUser._id,
  name: "Morning Routine",
  anchorHabit: "Wake up at 6 AM",
  habits: ["Meditate 10 mins", "Cold shower", "Breakfast"],
  difficulty: "medium"
});
```

---

### Creator Module Mutations

#### `createContentItem`

Creates a content item for Kanban board.

```typescript
Mutation: convex/creator.ts

Parameters:
{
  userId: ID (required),
  title: String (required),
  description?: String,
  platform: Array<"instagram" | "youtube" | "tiktok" | "twitter"> (required),
  category?: String,
  dueDate?: Date,
  status: "ideation" | "scripting" | "production" | "publishing" (default: "ideation")
}

Returns:
{
  success: Boolean,
  contentId: ID,
  message: String
}

Example Usage:
const result = await convex.mutation(api.creator.createContentItem, {
  userId: currentUser._id,
  title: "Study Tips Video",
  platform: ["youtube", "instagram"],
  dueDate: new Date(Date.now() + 7*24*60*60*1000)
});
```

---

#### `updateContentStatus`

Moves content through Kanban workflow.

```typescript
Mutation: convex/creator.ts

Parameters:
{
  contentId: ID (required),
  userId: ID (required),
  status: "ideation" | "scripting" | "production" | "publishing" (required)
}

Returns:
{
  success: Boolean,
  message: String
}

Example Usage:
const result = await convex.mutation(api.creator.updateContentStatus, {
  contentId: content._id,
  userId: currentUser._id,
  status: "production"
});
```

---

## Action Functions

Actions can call external APIs and don't have real-time return values.

### AI Actions

#### `generateQuizWithAI`

Calls Groq API to generate quiz questions.

```typescript
Action: convex/actions/ai.ts

Parameters:
{
  subject: String,
  difficulty: "easy" | "medium" | "hard",
  questionCount: Number,
  notes?: Array<String>
}

External API: Groq LLM
Model: Llama 2 70B
Rate Limit: 30 requests/minute
Timeout: 30 seconds

Returns:
Array<{
  id: String,
  type: "multiple_choice",
  text: String,
  options: Array<String>,
  correctAnswer: String,
  explanation?: String
}>

Error Handling:
- APIError: Groq API failed, retry with exponential backoff
- RateLimitExceeded: User hit monthly quota
- TimeoutError: Request exceeded 30s

Example Usage:
const questions = await convex.action(api.actions.ai.generateQuizWithAI, {
  subject: "Physics",
  difficulty: "hard",
  questionCount: 10,
  notes: ["Newton's Laws...", "Force definition..."]
});
```

---

#### `analyzeFitnessData`

Calls Gemini API for fitness insights.

```typescript
Action: convex/actions/ai.ts

Parameters:
{
  userId: ID,
  metricsType: "workout" | "nutrition" | "overall"
}

External API: Gemini API
Timeout: 20 seconds

Returns:
{
  insights: Array<String>,
  recommendations: Array<String>,
  summary: String
}

Example Usage:
const analysis = await convex.action(api.actions.ai.analyzeFitnessData, {
  userId: currentUser._id,
  metricsType: "nutrition"
});
```

---

#### `fetchLichessChessPuzzles`

Fetches chess puzzles from Lichess API.

```typescript
Action: convex/actions/chess.ts

Parameters:
{
  userId: ID,
  difficulty: Number (1-2700),
  count: Number (1-50)
}

External API: Lichess.org API
Endpoint: /api/puzzle/random
Rate Limit: 200 requests/hour
Authentication: OAuth token

Returns:
Array<{
  id: String,
  fen: String,
  moves: Array<String>,
  initialMove: String,
  rating: Number,
  themes: Array<String>
}>

Error Cases:
- Unauthorized: OAuth token expired
- RateLimitExceeded: Hit API rate limit

Example Usage:
const puzzles = await convex.action(api.actions.chess.fetchLichessChessPuzzles, {
  userId: currentUser._id,
  difficulty: 1400,
  count: 5
});
```

---

#### `uploadProgressPhoto`

Handles progress photo uploads with storage.

```typescript
Action: convex/actions/fitness.ts

Parameters:
{
  userId: ID,
  imageFile: Blob,
  angle: "front" | "side" | "back" | "flexed"
}

Returns:
{
  success: Boolean,
  imageUrl: String,
  photoId: ID,
  message: String
}

Validation:
- File size: Max 10MB
- Format: JPEG, PNG only
- Dimensions: Min 800x600, recommended 1080x1920

Example Usage:
const formData = new FormData();
formData.append("file", imageFile);
const result = await convex.action(api.actions.fitness.uploadProgressPhoto, {
  userId: currentUser._id,
  imageFile: imageFile,
  angle: "front"
});
```

---

## Error Handling

### Standard Error Responses

All API errors follow this format:

```typescript
{
  error: String,
  code: String,
  message: String,
  details?: Object,
  timestamp: Date
}

Error Codes:
- 400: BadRequest (invalid input)
- 401: Unauthorized (authentication failed)
- 403: Forbidden (insufficient permissions)
- 404: NotFound (resource doesn't exist)
- 409: Conflict (unique constraint violated)
- 429: TooManyRequests (rate limit exceeded)
- 500: InternalError (server error)
- 503: ServiceUnavailable (external API down)
```

### Error Recovery

Recommended retry strategy:

```typescript
async function retryQuery(queryFn, params, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await queryFn(params);
    } catch (error) {
      if (error.code === 429 && i < maxRetries - 1) {
        // Exponential backoff for rate limits
        await sleep(Math.pow(2, i) * 1000);
        continue;
      }
      throw error;
    }
  }
}
```

---

## Rate Limiting

### Query Rate Limits
- Default: 60 requests/minute per user
- Premium: Unlimited
- Burst: 10 requests/second

### Mutation Rate Limits
- Note creation: 100/day (free), unlimited (premium)
- Flashcard creation: 200/day (free), unlimited (premium)
- Quiz generation: 5/day (free), 50/day (premium)
- Meal logging: 3 per meal, per day
- Workout logging: Unlimited

### Action Rate Limits
- LLM requests: 100/day (free), 1000/day (premium)
- Lichess API: 200 requests/hour
- External APIs: Per provider limits

---

## Response Caching

- Queries: Real-time updates (no client caching)
- User stats: 5-minute cache
- Analytics: 1-hour cache
- Reference data: 24-hour cache

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Backend Team
