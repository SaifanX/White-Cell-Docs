# Mind & Strategy Module

**File Path:** `src/pages/Mind.tsx` (373 lines)

## Overview

The Mind & Strategy module is a mental training and strategic thinking platform that combines chess puzzle training, speedcubing timer, Stoic philosophy wisdom, debate practice, memory techniques, speed reading, logic puzzles, code sandbox projects, and strategy library management. It's designed for users who want to sharpen their cognitive abilities and develop strategic thinking.

## Page Purpose

- **Primary Function:** Comprehensive mental training and strategic thinking platform
- **Target Users:** Chess enthusiasts, puzzle lovers, strategic thinkers, developers, lifelong learners
- **Core Feature:** Integrated chess puzzles from Lichess with puzzle difficulty progression
- **Integration:** Unified calendar, Spotify player, strategy coaching via AI chatbot

## Key Features

### 1. Chess Puzzles & Training

**Puzzle Source:** Lichess.org integration (free, 1M+ puzzles)

**Daily Puzzle:**
- One new puzzle every day (difficulty adjusted to player rating)
- Shows correct solution after completion
- Tracks solve time
- Records accuracy (correct/incorrect)

**Puzzle Attempt Structure:**
```typescript
{
  puzzleId: string,
  userId: string,
  attempts: number,
  solved: boolean,
  timeSpent: number (seconds),
  difficulty: number (1-2500),
  date: Date
}
```

**Puzzle Features:**
- Progressive difficulty (match player skill level)
- Timed or untimed modes
- Hint system (show one move)
- Analysis board (review solution)
- Rating improvement tracking

**Chess Integration:**
- Connect Lichess account for synced stats
- Import game history
- Study opening repertoire
- Analyze blunders in games
- Train from your own games

**Puzzle Statistics:**
- Puzzles solved (total, this week, this month)
- Accuracy percentage
- Average solve time
- Puzzle rating (what difficulty you can solve)
- Streak (consecutive days solving)

### 2. Cube Timer (Speedcubing)

**Speedcubing Support:**

**Cube Types:**
- 2×2 cube (Pocket cube)
- 3×3 cube (Standard Rubik's)
- 4×4 cube (Revenge)
- Pyraminx (Pyramid puzzle)

**Timer Features:**
- Precise millisecond timing (to 0.01s)
- Scramble generation (WCA-standard for each puzzle)
- Best time tracking (PB = Personal Best)
- Average time (Ao5 = average of best 5)
- Mean time (all times average)
- Inspection mode (15 seconds before timer starts)

**Save Cube Time:**
```typescript
{
  cubeType: "2x2" | "3x3" | "4x4" | "pyraminx",
  time: number (milliseconds),
  scramble: string,
  dnf: boolean (Did Not Finish),
  date: Date
}
```

**Session Tracking:**
- Daily sessions (multiple solves)
- Session statistics (best, worst, average)
- Session history (revisit past sessions)
- Improvement tracking over weeks/months

**Cube Statistics:**
- Personal bests (PB) for each cube
- Ao5 (average of 5 consecutive solves)
- Ao12 (average of 12 consecutive solves)
- Total solves (lifetime, this year, this month)
- Recent trends (improving/plateauing)

### 3. Stoic Wisdom & Daily Philosophy

**Daily Stoic Wisdom:**
- One new Stoic quote every day
- Quotes from Marcus Aurelius, Seneca, Epictetus, etc.
- Philosophical commentary on quote
- Practical application to daily life
- Emoji mood rating (useful/powerful/neutral/confusing)

**Wisdom Features:**
- Save favorite quotes
- Search quotes by author or keyword
- Filter by theme (courage, discipline, virtue, etc.)
- AI generates daily wisdom (Groq LLM)
- Customize quote delivery time (morning/evening)

**Quote Examples:**
- "Memento Mori—Remember, you must die" (Unknown)
- "You have power over your mind—not outside events. Realize this, and you will find strength" (Marcus Aurelius)
- "The obstacle is the way" (Marcus Aurelius)
- "Amor Fati—Love your fate" (Seneca)

### 4. Debate Practice & Discussion

**Debate Mode:**
- AI poses debate topic or opposition argument
- User types response (2+ sentences minimum)
- AI provides counterargument
- User can rebut or concede
- Session tracks quality of arguments

**Debate Topics:**
- Philosophical (Is free will real?)
- Practical (Should school start later?)
- Technical (Is AI dangerous?)
- Personal development (How important is sleep?)
- Social (What makes a good society?)

**Daily Limit:**
- 3 debates per day (prevents overuse)
- Counter increments on API call
- Toast warning when approaching limit

**Debate Statistics:**
- Arguments made (total lifetime)
- Debate quality rating
- Topic variety
- Engagement streak (consecutive days debating)

**Activity Tracking:**
- Prevents abuse (spam debating)
- Logs debate participation
- Awards XP for quality arguments
- Encourages thoughtful engagement

### 5. Memory Palace (Mnemonic Device)

**Memory Palace Structure:**
```typescript
{
  name: string (e.g., "My House"),
  description: string,
  locations: [
    {
      name: string (e.g., "Front Door"),
      description: string,
      memoryItems: string[] (what to remember)
    }
  ]
}
```

**How It Works:**
1. Create palace with familiar locations
2. Add memory locations (rooms, landmarks)
3. Associate information to each location
4. Visualize walking through palace
5. Retrieve information by "visiting" location

**Example Palace:** "My childhood house"
- Front door → remember first 3 digits of π (3.14...)
- Living room → remember presidents list
- Kitchen → remember periodic table elements
- Bedroom → remember vocabulary words

**Features:**
- Create multiple palaces (organize by topic)
- Add/edit/delete locations
- Visualize palace layout (ASCII art, planned: 3D)
- Review mode (quiz yourself on locations)
- Export palace as study guide

### 6. Speed Reading Training

**Speed Reading Modes:**

**Guided Reading:**
- Text displays with adjustable reading speed (WPM: Words Per Minute)
- Ranges: 200 WPM (slow), 500 WPM (normal), 1000 WPM (fast), 2000+ WPM (challenge)
- Comprehension quiz after text
- Score based on accuracy

**Save Progress:**
```typescript
{
  wordCount: number,
  timeSpent: number (seconds),
  wpm: number (calculated),
  comprehensionScore: number (0-100),
  date: Date
}
```

**Improvement Tracking:**
- Personal WPM record
- Comprehension trend (maintain understanding while reading faster)
- Session history
- Difficulty progression

### 7. Logic Puzzles

**Puzzle Generation:**
- AI generates logic puzzles (Groq LLM)
- Types: Sudoku, Logic grid, Pattern recognition, Lateral thinking
- Difficulty levels: Easy, Medium, Hard, Expert

**Puzzle Caching:**
- Cache fresh puzzles (prevent repeats)
- Increment puzzle usage counter
- Get fresh puzzle when all cached used

**Save Puzzle Attempt:**
```typescript
{
  puzzleId: string,
  solved: boolean,
  timeSpent: number (seconds),
  difficulty: string,
  date: Date
}
```

**Puzzle Statistics:**
- Puzzles solved
- Solve rate (percentage correct)
- Average solve time
- Difficulty progress
- Streak (consecutive days solving)

### 8. Code Sandbox Projects

**Code Project Tracking:**
```typescript
{
  title: string,
  description: string,
  language: string (javascript, python, ruby, etc.),
  code: string,
  createdAt: Date,
  updatedAt: Date,
  tags: string[]
}
```

**Features:**
- Create new code projects
- Edit code with syntax highlighting
- Tag projects (project type, language, difficulty)
- Delete completed projects
- Export code as file

**Use Cases:**
- Save algorithm implementations
- Store coding interview prep
- Keep competitive programming solutions
- Document learning projects
- Quick reference snippets

**Code Integration:**
- Syntax highlighting for multiple languages
- Code formatter (planned)
- Run code in sandbox (planned: CodePen/Replit integration)
- Collaborate with others (planned)

### 9. Strategy Library

**PDF Strategy Management:**
```typescript
{
  title: string (e.g., "Bobby Fischer's Opening Principles"),
  description: string,
  pdf_url: string,
  uploadedAt: Date,
  category: string (chess, business, military, game theory)
}
```

**Upload & Store:**
- Upload PDF files (max 50MB per file)
- Organize by category/topic
- Searchable by title/description
- PDF viewer with page navigation
- Annotate pages (highlights, notes)

**Strategy Categories:**
- **Chess Strategies:** Opening principles, endgame techniques, middlegame tactics
- **Business Strategies:** Negotiation, leadership, decision-making
- **Military Strategies:** Historical tactics, strategic principles
- **Game Theory:** Zero-sum games, Nash equilibrium
- **Personal Development:** Time management, productivity

**Strategy Library Features:**
- Browse all uploaded strategies
- Search by keyword/category
- Filter by upload date
- Rate strategy (1-5 stars, planned)
- Save favorite sections
- Export highlighted sections

### 10. AI Strategy Coach

**AIChatbot Component:**
- `moduleType="mind"`
- Context: User's puzzle stats, chess rating, logic puzzle score
- Capabilities:
  - Explain chess concepts
  - Suggest logic puzzle strategies
  - Discuss Stoic philosophy
  - Recommend strategy reading
  - Motivate cognitive improvement

## UI Layout & Components

**Tab Structure (Swipe Navigation):**

```
Mind & Strategy Tabs:
├── Dashboard (Stats)
├── Chess Puzzles
├── Cube Timer
├── Stoic Wisdom
├── Debate
├── Memory Palace
├── Speed Reading
├── Logic Puzzles
├── Code Sandbox
├── Strategy Library
└── AI Coach
```

### Dashboard Tab - Mind Stats Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Mind Training Dashboard              │
├──────────────────────────────────────┤
│                                      │
│ Puzzle Streak: 🔥 12 days           │
│ Cube PB (3x3): 24.35s               │
│ Debates: 127 total                   │
│ Logic Puzzles Solved: 43             │
│                                      │
│ This Week:                           │
│ Puzzles: 8/10 [████░░░░░]           │
│ Cube Solves: 25/100 [██░░░░░░░]     │
│ Debates: 3/15 [████░░░░░░░░░]       │
│                                      │
│ Top Category Performance:            │
│ Tactical: 1450 rating               │
│ Endgame: 1380 rating                │
│ Opening: 1320 rating                │
│                                      │
└──────────────────────────────────────┘
```

**Backend:**
```typescript
const mindStats = useFetch('getMindStats', {
  userId: currentUser.id,
  timeRange: 'week'
})
```

### Chess Puzzles Tab - Chess Board Component

**Chessboard:**
- 8×8 grid alternating #f0d9b5 and #b58863
- Click piece to select, highlights valid moves in green
- Previous move highlight in light blue
- Unicode chess symbols (♔♕♖♗♘♙)

**Controls:** [Hint] [Give Up] [Analyze]

**Result Display:**
```
┌──────────────────────────────────────┐
│ Correct! ✓                           │
│ Solved in 2 moves | Time: 2:15      │
│ Accuracy: Excellent | +25 XP         │
│ [Next Puzzle] [Review] [Share]       │
└──────────────────────────────────────┘
```

**Backend:**
```typescript
async function submitMove(move: string) {
  return await mutation('submitChessPuzzleMove', {
    userId: currentUser.id,
    puzzleId,
    move,
    timeSpent,
    correct: validateMove(move)
  })
}
```

### Cube Timer Tab - Timer Component

**Timer Display:**
- Font: 72px monospace (tabular-nums)
- Format: MM:SS:MS
- Color: Green (normal), Yellow (< 60s), Red (very slow)
- Progress ring: SVG circle, animated as time counts

**Scramble:**
- Text: WCA notation (R U' R2 D2...)
- Buttons: [Copy] [Show] [Hide]

**Statistics:**
- Best (PB), Ao5 (average 5), Worst
- Recent times: Last 5-10 solves listed

**Backend:**
```typescript
async function logCubeTime(time: number, dnf: boolean) {
  return await mutation('logCubeTime', {
    userId: currentUser.id,
    cubeType: selectedCube,
    time,
    dnf,
    scramble: currentScramble,
    timestamp: new Date()
  })
}
```

### Stoic Wisdom Tab - Quote Component

**Quote Display:**
```
"The impediment to action advances action."
— Marcus Aurelius, Meditations
Reflection: Obstacles are opportunities...
```

**Components:**
- Quote: Italic, font-size 20px, centered
- Author: Semi-bold, 14px
- Reflection: Font-size 14px below divider
- Buttons: [♥ Save] [Share] [← Previous] [Next] [Random]

**Backend:**
```typescript
const dailyWisdom = useFetch('getDailyStoicWisdom', {
  userId: currentUser.id
})

async function saveQuote(quoteId: string) {
  return await mutation('toggleSavedQuote', {
    userId: currentUser.id,
    quoteId
  })
}
```

### Debate Tab - Debate Interface Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Debate Practice                      │
│ Debates Today: 2/3 remaining         │
├──────────────────────────────────────┤
│                                      │
│ Topic: "Is artificial intelligence   │
│ beneficial to humanity?"             │
│                                      │
│ [AI Statement]:                      │
│ "AI will create more jobs than      │
│  it destroys..."                     │
│                                      │
│ [Your Response]:                     │
│ [Type your argument here...]         │
│ (Minimum 2 sentences)                │
│                                      │
│ [Submit] [Skip] [Get Different Topic]│
│                                      │
│ Character count: 245/2000            │
│                                      │
└──────────────────────────────────────┘
```

**Debate Display:**
- Topic: Large, bold heading
- AI Statement: Card background, italicized
- User input: Textarea with char counter
- Min/Max: 2 sentences (enforced) / 2000 chars
- Quality feedback: Shown after submission

**Quality Scoring:**
- Logical coherence: Does argument follow logic?
- Evidence: Are claims backed up?
- Clarity: Is it easy to understand?
- Civility: Respectful tone?

**Backend:**
```typescript
// Convex Mutation - Submit debate response
async function submitDebateResponse(response: string) {
  return await mutation('submitDebateResponse', {
    userId: currentUser.id,
    topicId: currentTopic.id,
    userResponse: response,
    timestamp: new Date()
  })
  // Awards 10 XP for participation
  // AI generates counterargument
}

// Convex Query - Get daily debate limit
const debateCount = useFetch('getTodayDebateCount', {
  userId: currentUser.id
})
// Returns: { completed: 2, limit: 3 }
```

### Memory Palace Tab - Palace Builder Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Memory Palace: My House              │
├──────────────────────────────────────┤
│                                      │
│ Locations:                           │
│ [Front Door] [Living Room] [Kitchen] │
│ [Bedroom] [Bathroom]                 │
│                                      │
│ Selected: Front Door                 │
│ ┌──────────────────────────────────┐ │
│ │ Items to Remember:               │ │
│ │ 1. Golden apple (vivid imagery)  │ │
│ │ 2. Blue bird (singing)           │ │
│ │ 3. Purple elephant (dancing)     │ │
│ │ [+ Add Item] [Edit] [Delete]     │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Add Location] [Review Mode]       │
│ [Export] [Delete Palace]             │
│                                      │
└──────────────────────────────────────┘
```

**Palace Navigation:**
- Location buttons: Flex row, clickable
- Active location: Highlighted, underline
- Delete button (×) on each location

**Items List:**
- Numbered list
- Editable descriptions
- Add/edit/delete buttons

**Review Mode:**
- Hide item details, only show locations
- Test memory by "walking through" palace

**Backend:**
```typescript
async function addMemoryItem(locationId: string, item: string) {
  return await mutation('addMemoryPalaceItem', {
    userId: currentUser.id,
    locationId,
    itemDescription: item,
    timestamp: new Date()
  })
}

async function createMemoryPalace(name: string, locations: string[]) {
  return await mutation('createMemoryPalace', {
    userId: currentUser.id,
    name,
    locations: locations.map(loc => ({
      name: loc,
      items: []
    }))
  })
}
```

### Logic Puzzles Tab - Puzzle Component

**Puzzle Display:**
```
┌──────────────────────────────────────┐
│ Logic Puzzle #847          [Hint]    │
│ Difficulty: Medium | Time: 04:32     │
├──────────────────────────────────────┤
│                                      │
│ Puzzle Text:                         │
│ "Alice, Bob, and Carol each own a   │
│  pet: a cat, dog, or bird. Given:  │
│  - Alice doesn't own a cat...       │
│  - Bob owns the dog..."             │
│                                      │
│ Solution Area:                       │
│ Alice: [Select ▼]  | Dropdown       │
│ Bob: [Select ▼]    |                │
│ Carol: [Select ▼]  |                │
│                                      │
│ [Submit] [Clear] [Give Up]           │
│                                      │
└──────────────────────────────────────┘
```

**Components:**
- Puzzle text: Font-size 16px, line-height 1.6
- Solution dropdowns: Select from options
- Hint button: Shows relevant clue
- Progress ring: Time visualization
- Submit button: Validate solution

**Result:**
- Correct: Celebration message + XP
- Incorrect: Show mistakes + hint to try again

**Backend:**
```typescript
async function submitPuzzleSolution(solution: object) {
  return await mutation('submitLogicPuzzleSolution', {
    userId: currentUser.id,
    puzzleId,
    solution,
    timeSpent,
    timestamp: new Date()
  })
  // Validates solution, awards XP if correct
}

// AI Puzzle Generation (Groq)
const freshPuzzle = await action('generateLogicPuzzle', {
  difficulty: 'medium'
})
```

### Speed Reading Tab - Reading Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Speed Reading Session                │
│ Speed: [200 WPM ▼]  Time: 00:45      │
├──────────────────────────────────────┤
│                                      │
│ "The human brain is capable of      │
│  processing vast amounts of         │
│  information at remarkable speeds.  │
│  By training with focused reading   │
│  exercises, we can improve both our │
│  speed and comprehension..."        │
│                                      │
│ Progress: [████████░░░░] 60% read   │
│                                      │
│ [Pause] [Next Section] [Restart]    │
│                                      │
└──────────────────────────────────────┘
```

**Speed Selector:**
- Options: 200, 500, 1000, 2000+ WPM
- Changes text scroll speed
- Metrics: Words per minute display

**Text Display:**
- Center-aligned
- Font-size: 18px for readability
- Line-height: 1.6
- Color: Foreground

**Comprehension Quiz:**
```
After Reading:
1. What was the main idea?
   ○ Answer A
   ○ Answer B
   ○ Answer C
2. What did the text mention about...?
```

**Backend:**
```typescript
async function completeReadingSession(session: {
  wordCount: number,
  timeSpent: number,
  wpm: number,
  comprehensionScore: number
}) {
  return await mutation('logSpeedReadingSession', {
    userId: currentUser.id,
    ...session,
    timestamp: new Date()
  })
}
```

### Code Sandbox Tab - Code Editor Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Code Projects                        │
│ [Create New] [JavaScript ▼]          │
├──────────────────────────────────────┤
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Fibonacci Algorithm              │ │
│ │ Language: JavaScript             │ │
│ │ Tags: algorithms, recursion      │ │
│ │ Created: 2 weeks ago             │ │
│ │ [Edit] [Delete] [Copy Code]      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Binary Search Implementation    │ │
│ │ Language: Python                │ │
│ │ Tags: search, algorithms        │ │
│ │ [Edit] [Delete] [Copy Code]      │ │
│ └──────────────────────────────────┘ │
│                                      │
└──────────────────────────────────────┘
```

**Code Editor Modal:**
```
┌──────────────────────────────────────┐
│ Edit Code Project                    │
├──────────────────────────────────────┤
│                                      │
│ Title: [Project Title...]            │
│ Language: [JavaScript ▼]             │
│ Tags: [Add tags...]                  │
│                                      │
│ Code Editor (Syntax Highlight):      │
│ ┌──────────────────────────────────┐ │
│ │ function fibonacci(n) {         │ │
│ │   if (n <= 1) return n;         │ │
│ │   return fibonacci(n-1) + fib...│ │
│ │ }                               │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [Save] [Cancel] [Export]             │
│                                      │
└──────────────────────────────────────┘
```

**Code Display:**
- Syntax highlighting (Prism.js library)
- Line numbers
- Copy button
- Language badge

**Project Cards:**
- Title, language, tags
- Created date
- Edit/delete/copy buttons

**Backend:**
```typescript
async function createCodeProject(project: {
  title: string,
  code: string,
  language: string,
  tags: string[]
}) {
  return await mutation('createCodeProject', {
    userId: currentUser.id,
    ...project,
    timestamp: new Date()
  })
}

async function updateCodeProject(projectId: string, updates: any) {
  return await mutation('updateCodeProject', {
    userId: currentUser.id,
    projectId,
    ...updates
  })
}
```

### Strategy Library Tab - PDF Management Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Strategy Library                     │
│ [Category: All ▼] [Search...]        │
├──────────────────────────────────────┤
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Bobby Fischer's Opening Prin... │ │
│ │ Category: Chess Strategies      │ │
│ │ Pages: 85 | Uploaded: 2 mo ago  │ │
│ │ Rating: ★★★★★ (5/5)             │ │
│ │ [Read] [Download] [Delete]      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Negotiation Tactics              │ │
│ │ Category: Business Strategies    │ │
│ │ Pages: 120 | Uploaded: 1 mo ago  │ │
│ │ Rating: ★★★★☆ (4/5)             │ │
│ │ [Read] [Download] [Delete]      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Upload New Strategy]              │
│                                      │
└──────────────────────────────────────┘
```

**Strategy Card:**
- Title: Bold, truncated
- Category badge
- Metadata: Pages, upload date
- Rating: Star display (1-5)
- Buttons: [Read] [Download] [Delete]

**PDF Viewer:**
- Pagination controls
- Zoom options
- Full-screen toggle
- Annotation tools (highlight, notes)

**Upload Modal:**
- File input (drag & drop)
- Title input
- Category selector
- Description textarea
- [Upload] button

**Backend:**
```typescript
async function uploadStrategy(file: File, metadata: {
  title: string,
  category: string,
  description?: string
}) {
  const pdfUrl = await file.upload(file)
  return await mutation('createStrategy', {
    userId: currentUser.id,
    title: metadata.title,
    category: metadata.category,
    pdf_url: pdfUrl,
    description: metadata.description,
    timestamp: new Date()
  })
}

const strategies = useFetch('getUserStrategies', {
  userId: currentUser.id,
  category: selectedCategory || undefined
})
```

### AI Coach Tab - Chat Component

**See COMPONENTS.md - Chat Interface for detailed styling**

**AIChatbot Configuration:**
```typescript
<AIChatbot
  moduleType="mind"
  context={{
    puzzleRating: userPuzzleRating,
    cubePB: userCubePB,
    debateCount: userDebateCount,
    logicPuzzlesSolved: userLogicSolved
  }}
  systemPrompt="You are a strategic thinking coach. Help with chess strategy, logic, and problem-solving."
/>
```

**Backend:**
```typescript
const aiResponse = await action('sentinelAI', {
  message: userMessage,
  mode: 'normal' | 'research',
  context: mindContext,
  userId: currentUser.id
})
// Uses Groq Llama 3.3 for strategy advice
// Streams response token by token
```

## Data Queries

**Convex Queries:**
```typescript
// Chess data
const puzzles = useQuery((api as any).mind.getChessPuzzles, {});
const dailyPuzzle = useQuery((api as any).mind.getDailyPuzzle, {});
const puzzleStats = useQuery((api as any).mind.getPuzzleStats, {});

// Cube data
const cubeTimes = useQuery((api as any).mind.getCubeTimes, { cubeType: "3x3" });

// Stoic wisdom
const dailyWisdom = useQuery((api as any).mind.getDailyStoicWisdom, {});

// Other features
const memoryPalaces = useQuery((api as any).mind.getMemoryPalaces, {});
const codeProjects = useQuery((api as any).mind.getCodeProjects, {});
const strategies = useQuery((api as any).mind.getStrategies, {});
```

## XP & Gamification

**XP Awards:**
- Solve chess puzzle: 5-25 XP (based on difficulty and time)
- Complete cube solve: 3-10 XP (faster = more XP)
- Engage in debate: 10 XP (quality argument)
- Complete logic puzzle: 10 XP
- Speed reading session: 5 XP
- Save memory palace location: 2 XP
- Create code project: 10 XP
- Upload strategy PDF: 5 XP

**Streaks:**
- Daily puzzle streak
- Daily cube solving streak
- Debate participation streak (min 3 per day)
- Logic puzzle solving streak

**Badges:**
- "Puzzle Master" (100 puzzles solved)
- "Stoic Sage" (7-day philosophy streak)
- "Speed Cuber" (Ao12 < 30 seconds)
- "Logician" (20 logic puzzles solved)
- "Code Warrior" (10 code projects)

## Keyboard Shortcuts

**Chess Module:**
- `Space`: Make move (if obvious)
- `H`: Show hint
- `R`: Restart puzzle

**Cube Timer:**
- `Space`: Start/stop timer
- `N`: New scramble

**Other Modules:**
- `Ctrl+S`: Save (code project, memory palace)
- `Ctrl+D`: Delete
- `Alt+← →`: Navigate tabs

## Mobile Features

- **Pull-to-Refresh:** Swipe to refresh puzzle/wisdom
- **Haptic Feedback:** On puzzle solve, timer completion
- **Swipe Navigation:** Between module tabs
- **Camera:** Scan chess boards (planned)

## Performance Optimizations

- Chess puzzles cached locally (fetch new ones in batches)
- Cube timer uses Web Worker for precise timing (no jank)
- Logic puzzle generator debounced (prevent spam generation)
- Memory palace visualization lazy-loads images

## Accessibility

- Chess board keyboard navigation (arrow keys)
- Cube timer large text (readable from distance)
- High contrast for logic puzzle numbers
- Semantic HTML for quote display
- Screen reader support for timer

## Related Components

- **ChessPuzzles:** Chess puzzle interface
- **CubeTimer:** Cube timer UI
- **DailyStoicWisdom:** Quote display and rotation
- **MemoryPalaceBuilder:** Create and manage palaces
- **LogicPuzzleGenerator:** Generate and track puzzles
- **CodeSandbox:** Edit and save code
- **StrategyLibrary:** PDF management and reading
- **LichessConnect:** Link Lichess account

## Integration Points

1. **Study Module:** Create notes from strategy lessons
2. **Discipline Module:** Log strategy study as habit
3. **Fitness Module:** Meditation paired with cubing
4. **Creator Module:** Create content about chess/strategy
5. **Unified Calendar:** Schedule practice sessions
6. **Spotify Player:** Background study music

## Future Features

1. **Advanced Chess:**
   - Opening repertoire builder
   - Endgame tablebase study
   - Blunder analysis from your games
   - Opening trap identification

2. **Speedcubing:**
   - Tutorial videos
   - Cubing community integration
   - Competition tracking
   - Beginner to expert progression

3. **AI Coaching:**
   - Personalized puzzle recommendations
   - Weakness identification
   - Strategy learning path
   - Daily challenge recommendations

4. **Visualizations:**
   - Chess rating progress graph
   - Cube time improvement curve
   - Logic puzzle difficulty heatmap
   - Memory palace 3D visualization

## Technical Stack

- **Frontend:** React 18, TypeScript
- **Chess:** Lichess.org API integration
- **Puzzles:** Puzzle.lichess.org (free)
- **AI:** Groq LLM (puzzle generation, coaching)
- **Real-time:** Convex auto-sync queries
- **Storage:** Convex (code projects, strategies)
- **File Storage:** Convex file storage (PDFs)
- **UI:** shadcn/ui components
- **Animations:** Framer Motion
