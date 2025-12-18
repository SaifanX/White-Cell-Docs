# Database Schema - Complete Reference

**Version:** 1.0
**Database:** Convex (Real-time database)
**Last Updated:** December 18, 2025

---

## Overview

This document provides complete database schema specifications for the White Cell Protocol application. All tables, fields, types, relationships, and indexes are documented here.

---

## User & Authentication Tables

### Table: `users`

**Purpose:** Store user account information and profile data

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Unique user identifier (auto-generated) |
| `email` | String | Yes | User email address (unique) |
| `name` | String | Yes | Full name |
| `username` | String | Yes | Unique username for profile |
| `avatar` | String | No | Profile picture URL |
| `bio` | String | No | User biography (max 500 chars) |
| `role` | Enum | Yes | "user" \| "creator" \| "admin" (default: "user") |
| `subscription` | String | Yes | "free" \| "basic" \| "premium" |
| `subscriptionExpiry` | Date | No | Premium expiration date |
| `createdAt` | Date | Yes | Account creation timestamp |
| `updatedAt` | Date | Yes | Last profile update |
| `lastLogin` | Date | No | Last login timestamp |
| `isActive` | Boolean | Yes | Account active status (default: true) |
| `isBanned` | Boolean | Yes | Account ban status (default: false) |
| `totalXP` | Number | Yes | Cumulative XP earned (default: 0) |
| `currentLevel` | Number | Yes | User level (1-100, default: 1) |
| `currentStreak` | Number | Yes | Current daily streak (default: 0) |
| `longestStreak` | Number | Yes | Longest streak achieved (default: 0) |

**Indexes:**
- Primary: `_id`
- Unique: `email`, `username`
- Search: `name`, `username`

**Relationships:**
- One-to-Many: users → notes
- One-to-Many: users → workouts
- One-to-Many: users → flashcards
- One-to-Many: users → ai_conversations

---

### Table: `sessions`

**Purpose:** Manage user sessions and authentication tokens

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Session ID |
| `userId` | ID | Yes | Reference to users._id |
| `token` | String | Yes | JWT or OTP token |
| `expiresAt` | Date | Yes | Token expiration time |
| `ipAddress` | String | No | IP address of login |
| `userAgent` | String | No | Browser/device info |
| `type` | Enum | Yes | "otp" \| "password" \| "oauth" |
| `createdAt` | Date | Yes | Session start time |
| `isValid` | Boolean | Yes | Session validity (default: true) |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Expiration: `expiresAt` (TTL index for auto-cleanup)

---

### Table: `otp_tokens`

**Purpose:** Store one-time passwords for passwordless authentication

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Token ID |
| `email` | String | Yes | Email address for OTP |
| `token` | String | Yes | 6-digit OTP code |
| `expiresAt` | Date | Yes | OTP expiration (usually 10 mins) |
| `attempts` | Number | Yes | Failed attempt count (default: 0) |
| `maxAttempts` | Number | Yes | Max allowed attempts (default: 3) |
| `createdAt` | Date | Yes | Creation timestamp |
| `isUsed` | Boolean | Yes | Whether OTP was used (default: false) |

**Indexes:**
- Primary: `_id`
- Search: `email`
- Expiration: `expiresAt`

---

## Study Module Tables

### Table: `notes`

**Purpose:** Store user notes with rich text content

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Note ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Note title |
| `content` | String | Yes | Rich text content (HTML/Markdown) |
| `subject` | String | Yes | Subject/category (Math, Physics, etc.) |
| `tags` | Array<String> | No | Array of tags for organization |
| `linkedNotes` | Array<ID> | No | Array of linked note IDs |
| `color` | String | No | Color code for categorization |
| `isArchived` | Boolean | Yes | Archive status (default: false) |
| `isPinned` | Boolean | Yes | Pin to top status (default: false) |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update timestamp |
| `wordCount` | Number | Yes | Number of words in note |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Search: `title`, `content`, `subject`
- Filter: `subject`, `isArchived`

**Relationships:**
- Many-to-One: notes → users
- One-to-Many: notes → flashcards (generated from notes)
- Many-to-Many: notes → notes (linked notes)

---

### Table: `flashcards`

**Purpose:** Store flashcard deck items with SM-2 spaced repetition data

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Flashcard ID |
| `userId` | ID | Yes | Reference to users._id |
| `sourceNoteId` | ID | No | Parent note if generated from note |
| `question` | String | Yes | Front side (question) |
| `answer` | String | Yes | Back side (answer) |
| `subject` | String | Yes | Subject category |
| `difficulty` | Number | Yes | 1-5 difficulty rating |
| `nextReviewDate` | Date | Yes | SM-2 next review date |
| `reviewCount` | Number | Yes | Times reviewed (default: 0) |
| `easeFactor` | Number | Yes | SM-2 ease factor (default: 2.5, min: 1.3) |
| `interval` | Number | Yes | Days between reviews (default: 1) |
| `lastReviewDate` | Date | No | Last review timestamp |
| `reviewHistory` | Array<Object> | No | Array of review attempts |
| `status` | Enum | Yes | "new" \| "learning" \| "review" \| "mastered" |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update |

**Review History Item Structure:**
```typescript
{
  date: Date,
  quality: Number (0-5),
  easeFactor: Number,
  interval: Number,
  timeSpent: Number (seconds)
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `nextReviewDate`, `userId`, `subject`
- Filter: `status`

---

### Table: `quizzes`

**Purpose:** Store quiz sessions and results

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Quiz ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Quiz title |
| `subject` | String | Yes | Subject category |
| `questions` | Array<Object> | Yes | Array of quiz questions |
| `userAnswers` | Array<String> | Yes | User's answers (parallel to questions) |
| `score` | Number | Yes | Quiz score (0-100) |
| `correctAnswers` | Number | Yes | Count of correct answers |
| `totalQuestions` | Number | Yes | Total question count |
| `difficulty` | Enum | Yes | "easy" \| "medium" \| "hard" |
| `timeSpent` | Number | Yes | Total time in seconds |
| `timeLimit` | Number | No | Quiz time limit in seconds |
| `feedback` | Array<String> | No | Feedback for each question |
| `createdAt` | Date | Yes | Quiz completion timestamp |
| `isAIGenerated` | Boolean | Yes | Whether AI generated the quiz |

**Question Object Structure:**
```typescript
{
  id: String,
  type: "multiple_choice" | "short_answer" | "matching",
  text: String,
  options?: Array<String>,
  correctAnswer: String,
  category?: String
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `userId`, `subject`, `createdAt`

---

### Table: `textbooks`

**Purpose:** Store PDF textbooks and reading progress

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Textbook ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Book title |
| `subject` | String | Yes | Subject category |
| `author` | String | No | Author name |
| `isbn` | String | No | ISBN if available |
| `pdfUrl` | String | Yes | URL to stored PDF (Convex file storage) |
| `pages` | Number | Yes | Total page count |
| `currentPage` | Number | Yes | Current reading position (default: 1) |
| `annotations` | Array<Object> | No | Array of page annotations |
| `bookmarks` | Array<Number> | No | Array of bookmarked page numbers |
| `readingProgress` | Number | Yes | Percentage read (0-100) |
| `uploadedAt` | Date | Yes | Upload timestamp |
| `lastReadAt` | Date | No | Last reading timestamp |
| `course` | String | No | Course name if applicable |

**Annotation Object Structure:**
```typescript
{
  pageNumber: Number,
  text: String,
  type: "highlight" | "note",
  color?: String,
  createdAt: Date
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Search: `title`, `subject`, `author`

---

### Table: `study_goals`

**Purpose:** Track study goals and progress

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Goal ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Goal title |
| `description` | String | No | Goal description |
| `subject` | String | Yes | Subject area |
| `goalType` | Enum | Yes | "grade" \| "concept" \| "time" \| "reading" |
| `targetValue` | Number | Yes | Target value (grade %, hours, etc.) |
| `currentValue` | Number | Yes | Current progress value |
| `unit` | String | Yes | Unit of measurement |
| `targetDate` | Date | Yes | Goal deadline |
| `status` | Enum | Yes | "active" \| "completed" \| "abandoned" |
| `priority` | Number | Yes | 1-5 priority level (default: 3) |
| `createdAt` | Date | Yes | Creation timestamp |
| `completedAt` | Date | No | Completion timestamp |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `status`, `targetDate`

---

### Table: `study_sessions`

**Purpose:** Track time spent studying

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Session ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Session date |
| `subject` | String | Yes | Subject studied |
| `duration` | Number | Yes | Duration in minutes |
| `notesCreated` | Number | Yes | Notes created during session |
| `flashcardsReviewed` | Number | Yes | Flashcards reviewed |
| `quizzesTaken` | Number | Yes | Quizzes completed |
| `xpEarned` | Number | Yes | XP earned this session |
| `quality` | String | No | User's self-rating of session |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `subject`

---

## Fitness Module Tables

### Table: `workouts`

**Purpose:** Store completed workouts with exercise details

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Workout ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Workout date |
| `type` | Enum | Yes | "strength" \| "cardio" \| "flexibility" \| "sports" |
| `name` | String | No | Workout name (e.g., "Chest Day") |
| `duration` | Number | Yes | Duration in minutes |
| `calories` | Number | Yes | Estimated calories burned |
| `intensity` | Enum | Yes | "low" \| "moderate" \| "high" \| "very-high" |
| `exercises` | Array<Object> | Yes | Array of exercise details |
| `notes` | String | No | Workout notes |
| `templateId` | ID | No | Reference to template used |
| `createdAt` | Date | Yes | Timestamp |

**Exercise Object Structure:**
```typescript
{
  name: String,
  sets: Number,
  reps: Number,
  weight?: Number,
  unit?: "kg" | "lbs",
  distance?: Number,
  time?: Number,
  rpe?: Number (Rate of Perceived Exertion 1-10),
  notes?: String
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `type`

---

### Table: `measurements`

**Purpose:** Track body metrics and weight

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Measurement ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Measurement date |
| `weight` | Number | Yes | Body weight value |
| `unit` | String | Yes | "kg" \| "lbs" |
| `bodyFatPercentage` | Number | No | Body fat % |
| `muscleMass` | Number | No | Muscle mass value |
| `chest` | Number | No | Chest circumference |
| `waist` | Number | No | Waist circumference |
| `hips` | Number | No | Hip circumference |
| `thighs` | Number | No | Thigh circumference |
| `biceps` | Number | No | Bicep circumference |
| `notes` | String | No | Any notes about measurement |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`
- Sorting: `date` DESC for trend

---

### Table: `personal_records`

**Purpose:** Track PRs for exercises

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | PR ID |
| `userId` | ID | Yes | Reference to users._id |
| `exercise` | String | Yes | Exercise name |
| `value` | Number | Yes | PR value (weight, reps, distance, time) |
| `unit` | String | Yes | "kg", "lbs", "reps", "seconds", "km", etc. |
| `date` | Date | Yes | Date achieved |
| `previousPR` | Number | No | Previous PR value for comparison |
| `improvement` | Number | No | Improvement value |
| `notes` | String | No | Notes about the PR |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `exercise`, `date`

---

### Table: `meals`

**Purpose:** Store logged meals and nutritional information

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Meal entry ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Meal date |
| `mealType` | Enum | Yes | "breakfast" \| "lunch" \| "dinner" \| "snack" |
| `time` | String | Yes | Time of meal (HH:MM format) |
| `foods` | Array<Object> | Yes | Array of food items |
| `totalCalories` | Number | Yes | Total calories |
| `totalProtein` | Number | Yes | Total protein (grams) |
| `totalCarbs` | Number | Yes | Total carbs (grams) |
| `totalFat` | Number | Yes | Total fat (grams) |
| `totalFiber` | Number | No | Total fiber (grams) |

**Food Object Structure:**
```typescript
{
  name: String,
  quantity: Number,
  unit: String (grams, ml, servings, etc.),
  calories: Number,
  protein: Number,
  carbs: Number,
  fat: Number,
  fiber?: Number,
  barcode?: String (for scanned items)
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`

---

### Table: `nutrition_goals`

**Purpose:** Store daily nutrition targets

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Goal ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Goal date |
| `dailyCalories` | Number | Yes | Target calories |
| `proteinGrams` | Number | Yes | Target protein (g) |
| `carbsGrams` | Number | Yes | Target carbs (g) |
| `fatsGrams` | Number | Yes | Target fat (g) |
| `proteinPercentage` | Number | No | Protein % of calories |
| `carbsPercentage` | Number | No | Carbs % of calories |
| `fatsPercentage` | Number | No | Fat % of calories |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`

---

### Table: `progress_photos`

**Purpose:** Store progress photo tracking

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Photo ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Photo date |
| `imageUrl` | String | Yes | URL to stored image (Convex file storage) |
| `angle` | Enum | Yes | "front" \| "side" \| "back" \| "flexed" |
| `weight` | Number | No | Weight at time of photo |
| `notes` | String | No | Notes about photo |
| `tags` | Array<String> | No | Custom tags |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `angle`

---

### Table: `fitness_goals`

**Purpose:** Track fitness goals and milestones

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Goal ID |
| `userId` | ID | Yes | Reference to users._id |
| `goalType` | Enum | Yes | "weight" \| "strength" \| "endurance" \| "body_composition" |
| `name` | String | Yes | Goal name |
| `targetValue` | Number | Yes | Target value |
| `currentValue` | Number | Yes | Current progress |
| `unit` | String | Yes | "kg", "lbs", "minutes", "%", etc. |
| `deadline` | Date | Yes | Target completion date |
| `status` | Enum | Yes | "active" \| "completed" \| "abandoned" |
| `createdAt` | Date | Yes | Creation timestamp |
| `completedAt` | Date | No | Completion timestamp |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `status`

---

## Mind Module Tables

### Table: `chess_puzzles`

**Purpose:** Track chess puzzle attempts and progress

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Attempt ID |
| `userId` | ID | Yes | Reference to users._id |
| `lichessPuzzleId` | String | Yes | Lichess puzzle ID |
| `date` | Date | Yes | Attempt date |
| `solved` | Boolean | Yes | Whether puzzle was solved |
| `timeSpent` | Number | Yes | Time in seconds |
| `difficulty` | Number | Yes | Puzzle difficulty rating |
| `attempts` | Number | Yes | Number of attempts |
| `hintUsed` | Boolean | Yes | Whether hint was used |
| `userRating` | Number | No | User's estimated rating after |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `solved`

---

### Table: `cube_times`

**Purpose:** Store speedcubing solve times

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Time entry ID |
| `userId` | ID | Yes | Reference to users._id |
| `cubeType` | Enum | Yes | "2x2" \| "3x3" \| "4x4" \| "pyraminx" |
| `time` | Number | Yes | Solve time in milliseconds |
| `scramble` | String | Yes | WCA-standard scramble |
| `date` | Date | Yes | Date/time of solve |
| `dnf` | Boolean | Yes | Did Not Finish status |
| `plusTwo` | Boolean | Yes | +2 second penalty |
| `sessionId` | ID | No | Reference to session |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `cubeType`, `date`
- Sorting: `time` for statistics

---

### Table: `stoic_quotes`

**Purpose:** Store daily stoic wisdom

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Quote ID |
| `quote` | String | Yes | Quote text |
| `author` | String | Yes | Author name |
| `source` | String | No | Source (e.g., "Meditations") |
| `reflection` | String | No | Philosophical reflection |
| `theme` | String | No | Theme category |
| `createdAt` | Date | Yes | Creation timestamp |
| `savedBy` | Array<ID> | No | User IDs who saved this |

**Indexes:**
- Primary: `_id`
- Search: `author`, `theme`

---

### Table: `debate_sessions`

**Purpose:** Track debate practice sessions

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Session ID |
| `userId` | ID | Yes | Reference to users._id |
| `topic` | String | Yes | Debate topic |
| `userArgument` | String | Yes | User's argument |
| `aiCounterargument` | String | Yes | AI's response |
| `userRebuttal` | String | No | User's rebuttal |
| `quality` | Number | No | Quality rating (1-5) |
| `date` | Date | Yes | Session date |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`

---

### Table: `memory_palaces`

**Purpose:** Store memory palace structures

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Palace ID |
| `userId` | ID | Yes | Reference to users._id |
| `name` | String | Yes | Palace name |
| `description` | String | No | Palace description |
| `locations` | Array<Object> | Yes | Array of memory locations |
| `createdAt` | Date | Yes | Creation timestamp |

**Location Object Structure:**
```typescript
{
  id: String,
  name: String,
  description: String,
  items: Array<String> (items to remember)
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`

---

### Table: `logic_puzzles`

**Purpose:** Track logic puzzle attempts

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Attempt ID |
| `userId` | ID | Yes | Reference to users._id |
| `puzzleId` | String | Yes | Puzzle ID (external) |
| `puzzleType` | String | Yes | "sudoku" \| "logic_grid" \| "pattern" |
| `difficulty` | Enum | Yes | "easy" \| "medium" \| "hard" \| "expert" |
| `solved` | Boolean | Yes | Whether solved |
| `timeSpent` | Number | Yes | Time in seconds |
| `date` | Date | Yes | Attempt date |
| `hints` | Number | Yes | Hints used |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`

---

### Table: `code_projects`

**Purpose:** Store user code projects and snippets

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Project ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Project title |
| `description` | String | No | Project description |
| `code` | String | Yes | Source code |
| `language` | String | Yes | "javascript", "python", "ruby", etc. |
| `tags` | Array<String> | No | Project tags |
| `isPublic` | Boolean | Yes | Share publicly (default: false) |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Search: `title`, `language`

---

### Table: `strategies`

**Purpose:** Store uploaded strategy PDFs

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Strategy ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Strategy title |
| `description` | String | No | Description |
| `category` | String | Yes | "chess" \| "business" \| "military" \| "game_theory" |
| `pdfUrl` | String | Yes | URL to PDF (Convex file storage) |
| `uploadedAt` | Date | Yes | Upload timestamp |
| `rating` | Number | No | User rating (1-5) |
| `annotations` | Array<Object> | No | Highlighted sections |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Search: `title`, `category`

---

## Discipline Module Tables

### Table: `daily_tasks`

**Purpose:** Track daily to-do items

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Task ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Task date |
| `title` | String | Yes | Task description |
| `category` | String | Yes | Task category |
| `completed` | Boolean | Yes | Completion status (default: false) |
| `priority` | Number | Yes | 1-5 priority (default: 3) |
| `dueTime` | String | No | Due time (HH:MM) |
| `completedAt` | Date | No | Completion timestamp |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `completed`

---

### Table: `habit_stacks`

**Purpose:** Store compound habit stacks

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Stack ID |
| `userId` | ID | Yes | Reference to users._id |
| `name` | String | Yes | Stack name |
| `anchorHabit` | String | Yes | Trigger habit |
| `habits` | Array<String> | Yes | Ordered list of habits |
| `difficulty` | Enum | Yes | "easy" \| "medium" \| "hard" |
| `currentStreak` | Number | Yes | Days completed (default: 0) |
| `longestStreak` | Number | Yes | Best streak (default: 0) |
| `createdAt` | Date | Yes | Creation timestamp |
| `completedDates` | Array<Date> | Yes | Dates completed |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`

---

### Table: `pmo_tracker`

**Purpose:** Track PMO recovery and wellness

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Record ID |
| `userId` | ID | Yes | Reference to users._id |
| `date` | Date | Yes | Entry date |
| `currentStreak` | Number | Yes | Days since last relapse |
| `longestStreak` | Number | Yes | Longest streak achieved |
| `energyLevel` | Number | No | 1-5 energy rating |
| `focusLevel` | Number | No | 1-5 focus rating |
| `moodLevel` | Number | No | 1-5 mood rating |
| `confidenceLevel` | Number | No | 1-5 confidence rating |
| `relapsed` | Boolean | Yes | Relapse occurred (default: false) |
| `notes` | String | No | Session notes |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`

---

## Creator Module Tables

### Table: `content_items`

**Purpose:** Store content planning items for Kanban board

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Content ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Content title |
| `description` | String | No | Content description |
| `platform` | Array<String> | Yes | ["instagram", "youtube", "tiktok", "twitter"] |
| `status` | Enum | Yes | "ideation" \| "scripting" \| "production" \| "publishing" |
| `category` | String | No | Content category |
| `dueDate` | Date | No | Publishing deadline |
| `scheduledDate` | Date | No | Scheduled publish date |
| `links` | Array<String> | No | URLs to published content |
| `metrics` | Object | No | Views, likes, comments, shares |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `status`, `platform`
- Sort: `dueDate`

---

### Table: `content_ideas`

**Purpose:** Store brainstormed content ideas

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Idea ID |
| `userId` | ID | Yes | Reference to users._id |
| `title` | String | Yes | Idea title |
| `category` | String | Yes | "trending" \| "tutorial" \| "personal" \| "collab" \| "series" \| "evergreen" |
| `brainstormPoints` | Array<String> | No | Brainstorm ideas |
| `tags` | Array<String> | No | Hashtags |
| `potential` | Enum | Yes | "low" \| "medium" \| "high" |
| `createdAt` | Date | Yes | Creation timestamp |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `category`

---

### Table: `content_templates`

**Purpose:** Store reusable content templates

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Template ID |
| `userId` | ID | Yes | Reference to users._id |
| `name` | String | Yes | Template name |
| `category` | String | Yes | Content type |
| `outline` | Array<String> | Yes | Template outline |
| `scriptTemplate` | String | No | Script template with placeholders |
| `platforms` | Array<String> | Yes | Target platforms |
| `length` | String | No | Expected length |
| `reusageCount` | Number | Yes | Times used (default: 0) |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`

---

## AI & Chat Tables

### Table: `ai_conversations`

**Purpose:** Store AI conversation history

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Conversation ID |
| `userId` | ID | Yes | Reference to users._id |
| `topic` | String | Yes | Conversation topic |
| `module` | String | Yes | "study" \| "fitness" \| "mind" \| "general" |
| `mode` | Enum | Yes | "normal" \| "research" |
| `messages` | Array<Object> | Yes | Message history |
| `context` | Object | No | Conversation context |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last message timestamp |
| `isPinned` | Boolean | Yes | Pin important conversations (default: false) |

**Message Object Structure:**
```typescript
{
  id: String,
  role: "user" | "assistant",
  content: String,
  timestamp: Date,
  tokens?: Number,
  sources?: Array<String> (for research mode)
}
```

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `topic`, `module`, `createdAt`

---

### Table: `ai_context_memory`

**Purpose:** Store context memory for AI personalization

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Memory ID |
| `userId` | ID | Yes | Reference to users._id |
| `contextItem` | String | Yes | Single context fact |
| `category` | String | Yes | "goals" \| "preferences" \| "activities" \| "learning" |
| `source` | Enum | Yes | "manual" \| "auto_learned" |
| `confidence` | Number | Yes | 0-1 confidence score (default: 1) |
| `createdAt` | Date | Yes | Creation timestamp |
| `lastUsed` | Date | No | Last time referenced |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`

---

## Gamification Tables

### Table: `xp_logs`

**Purpose:** Track all XP earning events

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Log ID |
| `userId` | ID | Yes | Reference to users._id |
| `action` | String | Yes | Action type that earned XP |
| `xpAmount` | Number | Yes | XP earned |
| `module` | String | Yes | Module where action occurred |
| `sourceId` | ID | No | ID of source (note, workout, etc.) |
| `multiplier` | Number | Yes | XP multiplier applied (default: 1) |
| `description` | String | No | Human-readable description |
| `date` | Date | Yes | Timestamp |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Query: `date`, `module`

---

### Table: `badges`

**Purpose:** Track earned badges and achievements

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Badge earn ID |
| `userId` | ID | Yes | Reference to users._id |
| `badgeType` | String | Yes | Badge identifier |
| `name` | String | Yes | Badge display name |
| `description` | String | No | Badge description |
| `icon` | String | No | Badge icon URL or emoji |
| `earnedAt` | Date | Yes | Date earned |
| `tier` | Number | No | Badge tier if applicable |
| `rarity` | Enum | No | "common" \| "rare" \| "epic" \| "legendary" |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`
- Unique: userId + badgeType

---

### Table: `streaks`

**Purpose:** Track user streaks

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Streak ID |
| `userId` | ID | Yes | Reference to users._id |
| `streakType` | String | Yes | "daily_activity" \| "module_specific" \| "task" |
| `module` | String | No | Module if module-specific |
| `currentCount` | Number | Yes | Current streak length |
| `longestCount` | Number | Yes | Best streak achieved |
| `lastActiveDate` | Date | Yes | Last day of streak |
| `startDate` | Date | Yes | Streak start date |
| `completedDates` | Array<Date> | Yes | All active dates |

**Indexes:**
- Primary: `_id`
- Foreign Key: `userId`

---

## System Tables

### Table: `analytics`

**Purpose:** Track application analytics and usage

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Record ID |
| `userId` | ID | No | User ID if logged in |
| `event` | String | Yes | Event type |
| `module` | String | No | Module involved |
| `metadata` | Object | No | Additional event data |
| `timestamp` | Date | Yes | Event timestamp |
| `sessionId` | String | No | Session identifier |
| `ipAddress` | String | No | IP address |
| `userAgent` | String | No | Browser info |

**Indexes:**
- Primary: `_id`
- Query: `timestamp`, `event`, `userId`

---

### Table: `feature_flags`

**Purpose:** Control feature availability

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Flag ID |
| `name` | String | Yes | Feature flag name (unique) |
| `description` | String | No | What this flag controls |
| `isEnabled` | Boolean | Yes | Global enabled status |
| `rolloutPercentage` | Number | Yes | 0-100 gradual rollout (default: 0) |
| `targetUsers` | Array<ID> | No | Specific user IDs to enable for |
| `targetRoles` | Array<String> | No | Roles to enable for (admin, creator, etc.) |
| `createdAt` | Date | Yes | Creation timestamp |
| `updatedAt` | Date | Yes | Last update |

**Indexes:**
- Primary: `_id`
- Unique: `name`

---

### Table: `admin_logs`

**Purpose:** Track admin actions for security

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ID | Yes | Log ID |
| `adminId` | ID | Yes | Reference to admin user |
| `action` | String | Yes | Action performed |
| `targetId` | ID | No | ID of affected user/content |
| `targetType` | String | No | "user" \| "content" \| "system" |
| `changes` | Object | No | Before/after changes |
| `reason` | String | No | Reason for action |
| `timestamp` | Date | Yes | Action timestamp |
| `ipAddress` | String | No | IP address of admin |

**Indexes:**
- Primary: `_id`
- Foreign Key: `adminId`
- Query: `timestamp`, `targetId`

---

## Data Relationships Summary

```
users (1) ─── (*) notes
users (1) ─── (*) flashcards
users (1) ─── (*) quizzes
users (1) ─── (*) workouts
users (1) ─── (*) meals
users (1) ─── (*) measurements
users (1) ─── (*) ai_conversations
users (1) ─── (*) daily_tasks
users (1) ─── (*) xp_logs
users (1) ─── (*) badges

notes (1) ─── (*) flashcards
quizzes (1) ─── (*) quiz_questions (embedded)

workouts (1) ─── (*) exercises (embedded)
meals (1) ─── (*) foods (embedded)
ai_conversations (1) ─── (*) messages (embedded)
```

---

## Indexing Strategy

### High-Priority Indexes (Frequent Queries)
- `users._id` - All lookups
- `notes(userId, subject)` - Study filtering
- `flashcards(userId, nextReviewDate)` - Daily review
- `workouts(userId, date)` - Workout history
- `meals(userId, date)` - Nutrition tracking
- `sessions(expiresAt)` - TTL cleanup

### Performance Indexes
- Composite indexes for common filter+sort patterns
- Full-text search indexes for notes, content
- Geospatial indexes if location-based features added

### TTL (Time-To-Live) Indexes
- `otp_tokens.expiresAt` - Auto-delete expired OTPs
- `sessions.expiresAt` - Auto-delete expired sessions

---

## Migration & Backup

### Backup Strategy
- Daily backups (Convex handles automatically)
- Point-in-time recovery available
- Regular validation of data integrity

### Migration Path for Future Changes
- Create new table with modified schema
- Data migration script
- Update API queries/mutations
- Deprecate old table

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Backend Team
