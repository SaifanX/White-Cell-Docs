# Study Module (Study Zone)

**File Path:** `src/pages/Study.tsx` (2,315 lines)

## Overview

The Study Zone is a comprehensive learning management system that combines note-taking, spaced repetition flashcards, textbook annotation, quiz generation, study goals, and AI-powered content generation. It's designed for students, professionals, and lifelong learners who want to optimize their knowledge acquisition using scientifically-backed learning techniques.

## Page Purpose

- **Primary Function:** Complete learning ecosystem with spaced repetition
- **Target Users:** Students, professionals, lifelong learners
- **Core Feature:** SM-2 algorithm for intelligent flashcard review scheduling
- **Integration:** Unified calendar, Spotify player, AI chatbot

## Key Features

### 1. Note Management System

**Create & Edit Notes:**
- Rich text editor (TipTap) for formatted content
- Title, subject, and tags for organization
- Support for code blocks, tables, and lists
- Auto-save to prevent data loss
- Edit and delete notes with confirmation

**Note Structure:**
```typescript
{
  title: string,
  content: string,
  subject: string,
  tags: string[],
  createdAt: Date,
  updatedAt: Date,
  linkedNotes: Note[]
}
```

**Note Organization:**
- Filter by subject (Math, Physics, History, etc.)
- Search by title or content
- Sort by date created/modified
- Archive old notes without deletion
- Bulk operations (select multiple for tagging)

### 2. Flashcard System with SM-2 Algorithm

**Spaced Repetition Science:**
- SM-2 (Supermemo 2) algorithm implementation
- Optimal intervals: 1 day, 3 days, 7 days, 14 days, 30 days, 90 days
- Difficulty-based scheduling (easier = longer interval)
- Review next date calculated automatically

**Flashcard Features:**
- Create from notes or manually
- Add flipped pairs (question ↔ answer)
- Difficulty rating (1-5)
- Review history tracking
- Mark as "easy," "good," or "hard" on review
- Statistics: total cards, due today, mastered

**Review Interface:**
- One card at a time
- Tap to flip card
- Rate difficulty (easy/good/hard buttons)
- Next review date shown
- Progress bar for today's reviews

**Query for Due Cards:**
```typescript
getFlashcardsDueForReview(date, subject?)
```

### 3. Textbook Management

**Upload & Annotate:**
- Upload PDF textbooks (file storage integration)
- Page-by-page reading view
- Highlight important sections
- Add annotations with timestamps
- Extract key quotes

**Textbook Features:**
- Track reading progress (pages completed)
- Add notes per page
- Link annotations to flashcards
- Export highlighted sections as notes
- Organize by subject and course

**Integration:**
- Search textbooks by title or subject
- Quick access from notes (reference textbooks)
- Link textbook sections to study goals

### 4. Quiz Generation & Assessment

**AI-Powered Quiz Creation:**
- Generate quizzes from note content
- Multiple choice, fill-in-the-blank, matching
- AI selects relevant content sections
- Difficulty levels (easy, medium, hard)
- Instant scoring on completion

**Quiz Features:**
- Time-limited or untimed quizzes
- Question randomization
- Performance tracking over time
- Identify weak areas automatically
- Link quiz results to review recommendations

**Assessment Types:**
- Self-quizzes (create own questions)
- AI-generated quizzes (from notes/textbooks)
- Subject mastery tests
- Cumulative reviews (multiple subjects)

### 5. Study Goals & Progress Tracking

**Goal Creation:**
```typescript
{
  title: string,
  description: string,
  subject: string,
  targetDate: Date,
  targetGrade: number (0-100),
  currentProgress: number,
  status: "active" | "completed" | "abandoned"
}
```

**Goal Features:**
- Set multiple concurrent goals per subject
- Track progress with percentage
- Update progress on quiz completion
- Deadline-based urgency tracking
- Celebration notifications when completed

**Goal Types:**
- Grade targets (e.g., "90% in Math")
- Concept mastery (e.g., "Master calculus by Dec 31")
- Reading goals (e.g., "Complete textbook chapter 5")
- Time-based (e.g., "Study 500 hours total")

### 6. Study Sessions & Time Tracking

**Session Management:**
```typescript
{
  date: Date,
  subject: string,
  duration: number (minutes),
  notesCreated: number,
  flashcardsReviewed: number,
  quizzesTaken: number,
  xpEarned: number
}
```

**Time Tracking:**
- Auto-detect when user is actively studying
- Manual session logging
- Daily/weekly/monthly summaries
- Compare planned vs actual time
- Identify productive vs unproductive sessions

### 7. AI-Powered Content Generation

**Generate Flashcards:**
- Input: note content or textbook section
- Output: 5-10 flashcard pairs with questions and answers
- Difficulty rating auto-calculated
- One-click import into flashcard deck

**Generate Quizzes:**
- Input: note content or study goal
- Output: 10-15 multiple choice questions
- Difficulty balanced
- Automatic answer key generation
- Instant scoring after completion

**Summarize Content:**
- AI summarizes long textbook chapters
- Extract key concepts as bullet points
- Identify learning objectives
- Generate study guide outline

**Actions Used:**
- `api.ai.generateFlashcardsFromContent` - Groq LLM
- `api.ai.generateQuizFromContent` - Groq LLM
- `api.ai.generateTextbookSummary` - Groq LLM

### 8. Note Linking & Knowledge Graphs

**Bi-directional Links:**
- Link related notes to each other
- Create knowledge web
- Navigate from one note to linked notes
- Visualize note relationships (planned)

**Example:**
- Biology note links to Chemistry note (shared concepts)
- History note links to Literature note (same time period)
- Math note links to Physics note (shared equations)

## UI Layout

**Tab Structure (Swipe Navigation):**

```
Study Zone Tabs:
├── Notes
├── Flashcards
├── Quizzes
├── Textbooks
├── Goals
├── Resources
├── AI Coach
└── Calendar
```

**Mobile:** Swipe left/right between tabs
**Desktop:** Click tab buttons
**Keyboard:** Alt+← → for tab navigation

### Dashboard Tab

**Component: Metrics Cards (shadcn/ui Card)**

**Appearance:**
- Container: Card component with 24px padding, border-radius 8px
- Background: Card background color
- Shadow: 0 1px 3px rgba(0,0,0,0.12) on default, elevated on hover
- Layout: Grid 2 columns (mobile: 1 column, tablet: 2 columns, desktop: 4 columns)
- Spacing: 16px gap between cards

**Metrics Layout:**
```
┌─────────────────────────────┐
│ Total Notes              [→]│  ← Card header
│ 42                          │  ← Large metric number
│ Created 3 new this week     │  ← Subtitle
└─────────────────────────────┘
```

**Card Components:**
- Title: Font size 14px, font-weight 500, color muted-foreground
- Metric: Font size 32px, font-weight bold, color primary
- Subtitle: Font size 12px, color muted-foreground
- Click → Icon: Right arrow icon (24px), color muted

**Key Metrics Cards:**
- Total notes created (Count + trend arrow)
- Flashcards mastered (Count green) vs due (Count yellow)
- Quizzes average score (Percentage + sparkline graph)
- Study hours this week (Hours + weekly bar chart)
- Current goals progress (Active/Completed count)

**Backend Connection:**
```typescript
// Convex Query
const metrics = useFetch('getStudyMetrics', {
  userId: currentUser.id,
  timeRange: 'week' | 'month' | 'all'
})
// Returns: {
//   totalNotes: number,
//   masteredCards: number,
//   dueCards: number,
//   quizAverage: number,
//   studyHours: number,
//   goalsActive: number,
//   goalsCompleted: number
// }
```

**Quick Actions:**
- Create new note button (Button component, primary variant, with icon)
- Start flashcard review button (Button component, secondary variant)
- Generate quiz button (Button component, outline variant)
- Set study goal button (Button component, ghost variant)

### Notes Tab

**Layout:**
- Left sidebar: Subject filter dropdown, search input
- Main area: Notes grid (card layout)
- Right sidebar (desktop): Note preview/editor with RichTextEditor

**Component: Search & Filter Bar (shadcn/ui Input + Select)**

**Appearance:**
- Container: Flex row, gap 12px, padding 16px
- Search Input:
  - Height: 40px
  - Border: 1px border-color
  - Icon: Search icon (16px) left side
  - Placeholder: "Search notes..."
  - Focus: 2px ring primary color, border primary
- Subject Dropdown:
  - Height: 40px
  - Trigger text: "All Subjects" | selected subject
  - Icon: ChevronDown icon
  - Options: List of user's subjects with count
  - Styling: Same as input

**Backend Connection:**
```typescript
// Convex Query
const searchResults = useFetch('searchNotes', {
  query: searchTerm,
  subject: selectedSubject,
  userId: currentUser.id
})
// Returns: Note[] with matching title/content
```

**Card Display (Note Card Component)**

**Appearance:**
```
┌──────────────────────────────────────┐
│ Algebra Fundamentals        [⋯ menu]│  ← Title + menu button
│                                      │
│ Subject: Math                        │  ← Subject badge
│ Created 2 weeks ago | Updated 3d ago │  ← Meta info
│                                      │
│ Tags: algebra functions poly...     │  ← Tag pills
│                                      │
│ [Edit] [Delete] [Link] [Generate FC]│  ← Action buttons
└──────────────────────────────────────┘
```

**Card Components:**
- Card container: shadcn/ui Card with hover effect (scale 1.02, shadow increase)
- Title: Font-size 18px, font-weight 600, color foreground
- Subject Badge: Badge component, background primary (light), text primary
- Meta Info: Font-size 12px, color muted-foreground
- Tags: Inline badge components, background secondary
- Action buttons: Button component, variant "ghost", size "sm"
- Overflow menu: Button icon "MoreVertical" (24px)

**Button States:**
- Edit: Opens dialog with RichTextEditor
- Delete: Shows confirmation dialog
- Link Notes: Opens note selector dialog with search
- Generate FC: Calls AI API to create flashcards from note

**Backend Connection:**
```typescript
// Convex Mutation
async function updateNote(noteId: string, updates: Partial<Note>) {
  return await mutation('updateNote', { noteId, updates })
}

// Convex Query - Pagination
const notesPage = useFetch('getNotesBySubject', {
  subject: selectedSubject,
  page: currentPage,
  limit: 12
})

// AI Generation
const flashcards = await mutation('generateFlashcardsFromNote', {
  noteId: selectedNoteId,
  difficulty: 'medium'
})
// Calls: api.ai.generateFlashcardsFromContent via Groq
```

### Flashcards Tab

**Component: Tab Navigation (shadcn/ui Tabs)**

**Appearance:**
- Tab buttons: Flex row, height 40px
- Active tab: Primary color text, underline (2px primary)
- Inactive tab: Muted text
- Hover: Background opacity 5%
- Transition: 200ms

**Modes:**
1. **Review Mode** - Study due cards
2. **Browse Mode** - Browse all cards by subject
3. **Stats Mode** - Progress and mastery tracking

**Review Interface Component (Flashcard Component)**

**Appearance:**
```
┌───────────────────────────────────────┐
│ Flashcard Review          [10/25 done]│  ← Header
├───────────────────────────────────────┤
│                                       │
│      ┌─────────────────────────┐     │
│      │                         │     │
│      │  What is calculus?      │     │  ← Front (400px height)
│      │                         │     │
│      │  [Click to reveal...]   │     │
│      │                         │     │
│      └─────────────────────────┘     │
│                                       │
│  Next Review: 3 days                 │
│  Difficulty: Medium                  │
│                                       │
│     [← Easy] [Good] [Hard →]         │
│                                       │
└───────────────────────────────────────┘
```

**Flashcard Container:**
- Width: 600px (mobile: 100%, tablet: 90%)
- Height: 400px
- Background: Linear gradient (background to secondary)
- Border: 1px border-color
- Border-radius: 12px
- Shadow: 0 4px 12px rgba(0,0,0,0.15)
- Padding: 32px
- Font-size: 24px
- Font-weight: 500
- Text alignment: Center
- Display: Flex, align-items center, justify-content center

**Front Side (Question):**
- Visible: Always
- Text color: Foreground
- Hint text (gray): "Click to reveal answer"

**Back Side (Answer) - On Flip Animation:**
- Animation: rotateY (500ms cubic-bezier)
- Visible after flip
- Text color: Primary (slightly brighter)
- Background: Slightly darker (2% darker)

**Rating Buttons:**
- Layout: Flex row, gap 16px, justify-content center
- Button 1: "← Easy" (secondary variant)
  - Background: Green (oklch(75% 0.15 142))
  - Icon: ThumbsUp left side
- Button 2: "Good" (primary variant)
  - Background: Blue (primary color)
  - Icon: Check left side
- Button 3: "Hard →" (destructive variant)
  - Background: Red (oklch(65% 0.22 29))
  - Icon: AlertCircle left side
- Size: 40px height, padding 12px 24px
- Hover: Scale 1.05, shadow increase
- Active: Scale 0.98
- Disabled after click until next card loads

**Progress Info:**
- Progress bar: Width 100%, height 4px, margin-bottom 16px
- Bar color: Primary with gradient
- Text: "X/Y cards reviewed today" (font-size 14px, muted-foreground)

**Backend Connection:**
```typescript
// Convex Query - Get due flashcards
const dueCards = useFetch('getFlashcardsDueForReview', {
  userId: currentUser.id,
  subject: selectedSubject || undefined,
  date: new Date()
})
// Returns: Flashcard[] sorted by priority

// Convex Mutation - Update flashcard after review
async function reviewFlashcard(cardId: string, rating: 1 | 2 | 3) {
  // Rating: 1=Easy, 2=Good, 3=Hard (maps to quality 3, 3.5, 2)
  return await mutation('updateFlashcardReview', {
    cardId,
    rating,
    timestamp: new Date(),
    timeSpent: reviewTime // seconds
  })
  // Backend calculates SM-2 metrics and updates nextReviewDate
}
```

**Browse Mode Component (Grid Layout)**

**Appearance:**
- Grid: 3 columns (mobile: 1, tablet: 2, desktop: 3)
- Gap: 16px
- Card size: 200px width × 150px height
- Card styling: Same as individual flashcard but smaller

**Card Display:**
- Front text truncated (max 3 lines)
- Subject badge: Bottom left
- Difficulty indicator: Bottom right (color-coded)
  - Easy: Green dot + "Easy"
  - Medium: Yellow dot + "Medium"
  - Hard: Red dot + "Hard"
- Click to preview/edit

**Stats Mode Component (Charts & Metrics)**

**Appearance:**
```
┌─────────────────────────────────────┐
│ Flashcard Statistics                │
├─────────────────────────────────────┤
│                                     │
│ Total Cards: 127                    │
│ Mastered: 45 (35%) [███░░░░░]      │
│ Learning: 65 (51%) [█████░░░░]     │
│ New: 17 (14%) [█░░░░░░░░]          │
│                                     │
│ This Week:                          │
│ Cards Reviewed: 42                  │
│ Accuracy: 78%                       │
│ Avg Review Time: 18s                │
│                                     │
│ Subject Breakdown:                  │
│ ┌────────────────────────────────┐ │
│ │ Math: 45 cards (35%)           │ │ (Pie chart)
│ │ Physics: 32 cards (25%)        │ │
│ │ Chemistry: 30 cards (24%)      │ │
│ │ Biology: 20 cards (16%)        │ │
│ └────────────────────────────────┘ │
│                                     │
│ [Export Stats] [Reset Data]         │
└─────────────────────────────────────┘
```

**Stats Display:**
- Metric cards: Similar to dashboard, font-size 20px
- Progress bars: Height 8px, border-radius 4px
- Pie chart: Recharts library, radius 120px, colors by subject
- Color mapping: Each subject gets a different color
- Click legend: Toggle subject visibility

**Backend Connection:**
```typescript
// Convex Query - Get flashcard statistics
const flashcardStats = useFetch('getFlashcardStats', {
  userId: currentUser.id,
  timeRange: 'week' | 'month' | 'all'
})
// Returns: {
//   totalCards: number,
//   masteredCount: number,
//   learningCount: number,
//   newCount: number,
//   weeklyReviews: number,
//   accuracy: number,
//   avgReviewTime: number,
//   bySubject: { [subject]: { count, percentage } }
// }
```

### Quizzes Tab

**Component: Quiz History List (Scrollable Card List)**

**Appearance:**
```
┌───────────────────────────────────────┐
│ Recent Quizzes                        │
├───────────────────────────────────────┤
│                                       │
│ ┌─────────────────────────────────┐  │
│ │ Calculus Basics           [menu] │  │
│ │ Score: 85% | 17/20 correct      │  │
│ │ Time: 15 min | Date: Dec 18     │  │
│ │ [View Results] [Retake] [Delete]│  │
│ └─────────────────────────────────┘  │
│                                       │
│ ┌─────────────────────────────────┐  │
│ │ Algebra Advanced          [menu] │  │
│ │ Score: 92% | 23/25 correct      │  │
│ │ Time: 20 min | Date: Dec 17     │  │
│ │ [View Results] [Retake] [Delete]│  │
│ └─────────────────────────────────┘  │
│                                       │
└───────────────────────────────────────┘
```

**Quiz Card Components:**
- Container: Card with border-left (4px accent color by subject)
- Title: Font-size 16px, bold
- Score: Large number (28px), color green if > 80%, yellow if 60-80%, red if < 60%
- Stats: Font-size 12px, muted-foreground
- Buttons: Ghost variant, size sm

**Backend Connection:**
```typescript
// Convex Query - Get quiz history
const quizHistory = useFetch('getQuizHistory', {
  userId: currentUser.id,
  limit: 20,
  subject: selectedSubject || undefined
})
// Returns: Quiz[] with scores and timestamps

// Convex Mutation - Delete quiz
async function deleteQuiz(quizId: string) {
  return await mutation('deleteQuiz', { quizId })
}
```

**Create Quiz Component (Modal Dialog with Stepper)**

**Appearance:**
```
┌────────────────────────────────────┐
│ Create New Quiz                [✕] │
├────────────────────────────────────┤
│ Step 1: Select Source              │
│ ○ From Notes                       │
│ ○ From Textbooks                   │
│ ○ Custom Questions                 │
│ [Continue]                         │
└────────────────────────────────────┘
```

**Dialog Structure:**
- Modal with max-width 600px
- Title: Font-size 20px, bold
- Content: Scrollable, padding 24px
- Footer: Buttons [Cancel] [Continue] / [Previous] [Create]

**Step 1: Source Selection**
- Radio buttons (custom component)
- Label text: Font-size 16px
- Description: Font-size 12px, muted
- Selected state: Background highlight, radio checked

**Step 2: Quiz Configuration**
- Difficulty selector: Dropdown [Easy / Medium / Hard / Mixed]
- Number of questions: Number input with +/- buttons
- Time limit: Toggle + time picker
- Question preview: Checkbox "Preview before starting"

**Step 3: Review & Create**
- Summary card showing quiz config
- Question list preview (scrollable)
- [Create Quiz] button (primary, full-width)

**Backend Connection:**
```typescript
// Convex Mutation - Create quiz
async function createQuiz(config: {
  source: 'notes' | 'textbooks' | 'custom',
  difficulty: 'easy' | 'medium' | 'hard',
  questionCount: number,
  timeLimit?: number,
  sourceIds: string[]
}) {
  return await mutation('createQuiz', config)
  // Calls AI to generate questions if needed
  // api.ai.generateQuizFromContent via Groq
}
```

### Textbooks Tab

**Component: Upload Area & File List (Drag & Drop)**

**Appearance:**
```
┌─────────────────────────────────────┐
│ Upload Textbooks                    │
├─────────────────────────────────────┤
│                                     │
│ ┌───────────────────────────────┐  │
│ │  [↓] Drop files or click      │  │ ← Upload area
│ │      Max 50MB per file        │  │
│ └───────────────────────────────┘  │
│                                     │
│ My Textbooks:                       │
│                                     │
│ ┌─────────────────────────────────┐│
│ │ Calculus I               [menu] ││
│ │ Subject: Math                   ││
│ │ Pages: 542 | Progress: 128/542  ││
│ │ Annotations: 23                 ││
│ │ [Read] [Edit] [Delete]          ││
│ └─────────────────────────────────┘│
│                                     │
└─────────────────────────────────────┘
```

**Upload Area:**
- Container: Dashed border (2px), border-color primary, border-radius 8px
- Padding: 32px
- Background: Background with 5% primary opacity
- On hover: Border solid, background opacity 10%
- Icon: FileUp (48px), color primary
- Text: "Drop files or click to upload"
- Support text: "Max 50MB per file"
- Input: Hidden file input, accept="application/pdf"

**Textbook Card:**
- Layout: Flex column
- Title: Font-size 16px, bold
- Subject badge: Badge component
- Metadata: "Pages: 542 | Progress: 128/542" (font-size 12px)
- Progress bar: Width 100%, height 6px
- Action buttons: [Read] [Edit] [Delete]

**Backend Connection:**
```typescript
// Convex Mutation - Upload PDF
async function uploadTextbook(file: File, metadata: {
  title: string,
  subject: string,
  course?: string
}) {
  // File stored in Convex file storage
  return await mutation('createTextbook', {
    title: metadata.title,
    subject: metadata.subject,
    pdf_url: await pdf.upload(file),
    pages: extractPageCount(file),
    userId: currentUser.id
  })
}

// Convex Query - List textbooks
const textbooks = useFetch('getUserTextbooks', {
  userId: currentUser.id,
  subject: selectedSubject || undefined
})
```

**Reading View (PDF Viewer Component)**

**Appearance:**
```
┌──────────────────────────────────────┐
│ [←] Page 128 of 542 [→] [Zoom] [Full]│ ← Controls
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐   │
│  │                              │   │
│  │  PDF Page Content            │   │
│  │  (Rendered page)             │   │
│  │                              │   │
│  └──────────────────────────────┘   │
│                                      │
│  ┌──────────────────────────────┐   │
│  │ Notes & Annotations for Page:│   │  ← Side panel
│  │ • Important formula here...  │   │
│  │ • Remember this concept...   │   │
│  │ [+ Add Note] [+ Highlight]   │   │
│  └──────────────────────────────┘   │
└──────────────────────────────────────┘
```

**Controls:**
- Pagination buttons: Prev [←], Next [→] (icon buttons)
- Page display: "Page 128 of 542" (click to jump to page)
- Zoom: Dropdown [50% / 75% / 100% / 125% / 150%]
- Fullscreen: Toggle button
- Download: Download button

**PDF Container:**
- Width: 100%
- Height: Calculated (responsive)
- Border: 1px border-color
- Background: #fff (white for PDF)
- Zoom: CSS transform scale()

**Annotations Panel:**
- Width: 320px (sidebar)
- Position: Right side
- Scrollable
- Border-left: 1px border-color
- Padding: 16px

**Backend Connection:**
```typescript
// Convex Query - Get PDF metadata
const pdfData = useFetch('getTextbookPages', {
  textbookId: selectedTextbookId
})
// Returns pages with prerendered images

// Convex Mutation - Add annotation
async function addAnnotation(pageNumber: number, text: string) {
  return await mutation('addTextbookAnnotation', {
    textbookId: selectedTextbookId,
    pageNumber,
    text,
    timestamp: new Date()
  })
}

// Convex Mutation - Update reading progress
async function updateReadingProgress(pageNumber: number) {
  return await mutation('updateTextbookProgress', {
    textbookId: selectedTextbookId,
    currentPage: pageNumber
  })
}
```

### Goals Tab

**Component: Goal Card List & Create Dialog**

**Appearance:**
```
┌────────────────────────────────────┐
│ Study Goals                        │
│ [Active: 3] [Completed: 8]         │ ← Filter
├────────────────────────────────────┤
│                                    │
│ ┌──────────────────────────────┐  │
│ │ Master Calculus I        [⋯] │  │
│ │ Target: 90% | Current: 72%   │  │
│ │ [██████░░░░░░] 72%           │  │
│ │ Deadline: Dec 31 (13 days)   │  │
│ │ Status: Active               │  │
│ │ [+10%] [+25%] [Custom] [Edit]│  │
│ └──────────────────────────────┘  │
│                                    │
│ ┌──────────────────────────────┐  │
│ │ Finish Physics Textbook  [⋯] │  │
│ │ Target: 100% | Current: 85%  │  │
│ │ [████████░░░] 85%            │  │
│ │ Deadline: Dec 28 (10 days)   │  │
│ │ Status: Active               │  │
│ │ [+10%] [+25%] [Custom] [Edit]│  │
│ └──────────────────────────────┘  │
│                                    │
│ [+ New Goal]                       │
└────────────────────────────────────┘
```

**Goal Card:**
- Container: Card with border-left (4px color-coded by subject)
- Title: Font-size 16px, bold
- Target display: "Target: 90% | Current: 72%"
- Progress bar: Height 8px, animation smooth
  - Fill color: Green if on track, yellow if warning, red if behind
- Deadline: "Deadline: Dec 31 (13 days)" - red text if < 3 days
- Status badge: "Active" (green), "Completed" (blue), "Abandoned" (gray)
- Buttons: [+10%] [+25%] [Custom] [Edit]

**Backend Connection:**
```typescript
// Convex Query - Get user goals
const goals = useFetch('getUserStudyGoals', {
  userId: currentUser.id,
  status: selectedStatus || 'all' // 'active' | 'completed' | 'abandoned'
})

// Convex Mutation - Update goal progress
async function updateGoalProgress(goalId: string, increment: number) {
  return await mutation('updateGoalProgress', {
    goalId,
    progressIncrement: increment,
    timestamp: new Date()
  })
  // Also triggers notifications if goal completed
}

// Convex Mutation - Delete/abandon goal
async function deleteGoal(goalId: string) {
  return await mutation('deleteGoal', { goalId })
}
```

**Create Goal Dialog:**
- Modal form with fields:
  - Title input (text field)
  - Subject dropdown
  - Target percentage (number 0-100)
  - Target date picker
  - Description (textarea)
  - [Create Goal] button

### Resources Tab

**Component: Resource Cards Grid**

**Appearance:**
```
┌────────────────────────────────────┐
│ Recommended Resources              │
│ [All] [Tutorials] [Courses] [Books]│ ← Filter
├────────────────────────────────────┤
│                                    │
│ ┌──────────────┐ ┌──────────────┐ │
│ │ Calculus     │ │ Linear       │ │
│ │ Tutorial     │ │ Algebra      │ │
│ │ Excellent    │ │ Course       │ │
│ │ explanation  │ │ Comprehensive│ │
│ │ of concepts  │ │ 12-hour      │ │
│ │ [Visit]      │ │ course       │ │
│ └──────────────┘ │ [Visit]      │ │
│                  └──────────────┘ │
│                                    │
└────────────────────────────────────┘
```

**Resource Card:**
- Width: 300px
- Height: 280px
- Container: Card with hover effect (scale 1.05, shadow increase)
- Title: Font-size 18px, bold
- Category badge: Badge (top right)
- Description: Font-size 14px, 3 lines max (ellipsis)
- Call-to-action: [Visit] button (secondary)
- Border-bottom: 2px accent color (by category)

**Backend Connection:**
```typescript
// Convex Query - Get recommended resources
const resources = useFetch('getRecommendedResources', {
  userId: currentUser.id,
  subject: selectedSubject || undefined,
  category: selectedCategory || 'all'
})
// Resource data pulled from external API or curated database
```

### AI Coach Tab

**Component: AIChatbot (Sentinel AI)**

**Appearance:**
```
┌──────────────────────────────────────┐
│ Study Coach (Sentinel AI)    [Normal]│ ← Mode
├──────────────────────────────────────┤
│                                      │
│ [Chat history scrollable area]       │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ User:                            │ │
│ │ How do I study calculus better?  │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Coach:                           │ │
│ │ Try the Feynman technique...     │ │
│ │ ...                              │ │
│ │ [Copy] [Save]                    │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ [Type question.....................│ │
│ │ [Send]                           │ │
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

**See COMPONENTS.md - Chat Interface for detailed styling**

**AIChatbot Configuration:**
```typescript
<AIChatbot
  moduleType="study"
  context={{
    subjects: userSubjects,
    currentGoals: userGoals,
    flashcardStats: stats,
    recentQuizzes: quizzes
  }}
  systemPrompt="You are a helpful study coach. Provide study strategies, explain concepts, and motivate learners."
/>
```

**Backend Connection:**
```typescript
// Convex Action - Stream AI response
const aiResponse = await action('sentinelAI', {
  message: userMessage,
  mode: 'normal' | 'research',
  context: studyContext,
  userId: currentUser.id
})
// Uses Groq Llama 3.3 (normal) or Perplexity (research)
// Streams response token by token
```

### Calendar Tab

**Component: Unified Calendar**

**Appearance:**
```
┌──────────────────────────────────────┐
│ Study Calendar                [Month]│
├──────────────────────────────────────┤
│ Mo Tu We Th Fr Sa Su                │
│  1  2  3  4  5  6  7                │
│  8  9 10 11 12 13 14                │
│ 15 16 17 18 19 20 21  ← Dec 18      │
│ 22 23 24 25 26 27 28                │
│ 29 30 31                            │
│                                      │
│ Events:                              │
│ [Study: Math] [Quiz: Physics]        │ ← Color-coded
│ [Goal: Finish Ch5]                  │
│                                      │
└──────────────────────────────────────┘
```

**See COMPONENTS.md - Unified Calendar for detailed styling**

**Study Events:**
- Study sessions: Blue color (#blue-500)
- Quiz dates: Orange color (#orange-500)
- Goal deadlines: Red color (#red-500)
- Exams (if input): Purple color (#purple-500)
- Click event to see details

**Backend Connection:**
```typescript
// Convex Query - Get study events
const studyEvents = useFetch('getStudyCalendarEvents', {
  userId: currentUser.id,
  month: currentMonth
})
// Returns: Array of { date, type, title, details }
```

## Data Structure

**Database Tables:**

| Table | Fields | Purpose |
|-------|--------|---------|
| `notes` | userId, title, content, subject, tags[], createdAt, updatedAt | Note storage |
| `flashcards` | userId, question, answer, difficulty, nextReviewDate, reviewCount, easeFactor, interval | SM-2 cards |
| `textbooks` | userId, title, subject, pdf_url, pages, annotationCount, readProgress | Textbook management |
| `quizzes` | userId, title, questions[], subject, difficulty, score, timeSpent, date | Quiz history |
| `studyGoals` | userId, title, subject, targetDate, currentProgress, targetGrade, status | Goal tracking |
| `studySessions` | userId, date, subject, duration, xpEarned, itemsCreated | Session tracking |

## SM-2 Algorithm Details

**How It Works:**

1. **Initial State:**
   - EaseFactor = 2.5 (default)
   - Interval = 1 day
   - NextReview = today + 1

2. **User Reviews Card:**
   - Quality rating: 0-5 (1=easy, 3=good, 5=hard)

3. **Update EaseFactor:**
   ```
   EaseFactor = EaseFactor + (0.1 - (5 - quality) * (0.08 + (5 - quality) * 0.02))
   Min EaseFactor = 1.3
   ```

4. **Calculate Next Interval:**
   ```
   if first review:
     Interval = 1
   else if second review:
     Interval = 3
   else:
     Interval = Interval * EaseFactor
   ```

5. **Set Next Review Date:**
   ```
   NextReviewDate = today + Interval
   ```

**Result:** Cards requiring review appear at optimal times for memory retention.

## XP & Gamification

**XP Awards:**
- Create note: 5 XP
- Create flashcard: 2 XP
- Review flashcard (correct): 3 XP + difficulty bonus (1-3 XP)
- Complete quiz: 10 XP + accuracy bonus
- Complete study goal: 50 XP
- Achieve 100% quiz score: 25 XP bonus
- Study 1 hour: 25 XP

**Streaks:**
- Daily study streak (study every day)
- Subject mastery streak (complete X flashcards in subject)
- Quiz perfection streak (100% on 5 consecutive quizzes)

## Keyboard Shortcuts

**Study Module Specific:**
- `Space`: Flip flashcard (in review mode)
- `1`: Rate "Easy" on flashcard
- `2`: Rate "Good" on flashcard
- `3`: Rate "Hard" on flashcard
- `Ctrl+N`: Create new note
- `Ctrl+F`: Create new flashcard
- `Ctrl+Q`: Generate new quiz
- `Alt+← →`: Navigate tabs

## Mobile Features

- **Pull-to-Refresh:** Swipe down to refresh data
- **Swipe Navigation:** Left/right between tabs
- **Haptic Feedback:** On card flips, button clicks
- **Full-width Input:** Input fields adapt to mobile screens

## Performance Optimizations

- **Query Optimization:**
  - `getFlashcardsDueForReview` filters by date (server-side)
  - `getNotes` with subject filtering
  - Real-time Convex queries auto-cache

- **Rendering:**
  - Flashcard review uses React.memo to prevent re-renders
  - Note grid uses virtualization for large note lists
  - Quiz questions lazy-load

- **Storage:**
  - PDFs stored in Convex file storage
  - Image compression for note attachments
  - Flashcard review history pruned after 6 months

## Related Components

- **RichTextEditor:** Note creation/editing
- **AIChatbot:** Study coach integration
- **UnifiedCalendar:** Study session scheduling
- **SpotifyPlayer:** Background study music
- **XPFeedback:** Real-time XP display

## Integration Points

1. **Discipline Module:** Study goals link to discipline goals
2. **Mind Module:** Create notes from strategy articles
3. **Fitness Module:** Log study time as discipline habit
4. **Creator Module:** Create content from study notes
5. **Sentinel AI:** Ask questions about study material

## Accessibility

- Keyboard navigation for all features
- Color contrast WCAG AA for quiz options
- Alt text for textbook thumbnails
- Focus management in modal dialogs
- Screen reader support for progress bars

## Future Features

1. **Collaborative Learning:**
   - Share notes with classmates
   - Study group coordination
   - Peer review of flashcards

2. **Advanced AI:**
   - Adaptive difficulty based on performance
   - Personalized study schedules
   - Predict exam performance
   - Identify knowledge gaps automatically

3. **Video Integration:**
   - Embed video tutorials in notes
   - AI extract key frames as flashcard images
   - Lecture video transcription and annotation

4. **Visualization:**
   - Knowledge graph of note relationships
   - Heatmap of study consistency
   - Learning curve visualization

5. **Export Options:**
   - Export notes as PDF/Word
   - Anki format for flashcards
   - Study guide generation

## Technical Stack

- **Frontend:** React 18, TypeScript, TipTap editor
- **Real-time:** Convex queries with auto-sync
- **AI:** Groq LLM (flashcard/quiz generation)
- **Storage:** Convex file storage (PDFs)
- **Animations:** Framer Motion
- **UI:** shadcn/ui components
- **State Management:** React hooks + Convex mutations
