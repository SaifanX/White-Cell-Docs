# Components, UI/UX & Visual Design Guide

**Document:** Complete Component Library & Module UI Specifications
**Version:** 1.0
**Last Updated:** December 2025

## Table of Contents

1. [Layout Components](#layout-components)
2. [Common UI Components](#common-ui-components)
3. [Study Module Components](#study-module-components)
4. [Fitness Module Components](#fitness-module-components)
5. [Mind Module Components](#mind-module-components)
6. [Discipline Module Components](#discipline-module-components)
7. [Creator Module Components](#creator-module-components)
8. [Sentinel AI Module Components](#sentinel-ai-module-components)

---

## Layout Components

### AppLayout Component

**Purpose:** Main application container managing navigation and routing

**Visual Structure:**
```
┌─────────────────────────────────────────────────────────┐
│  Header (Desktop) / Top Nav (Mobile)                   │
├─────────────────┬─────────────────────────────────────┤
│                 │                                     │
│  Sidebar        │  Main Content Area                  │
│  (Desktop)      │  (Responsive grid)                  │
│                 │                                     │
└─────────────────┴─────────────────────────────────────┘
└──────────────────────────────────────────────────────────┘
│ Bottom Navigation (Mobile Only)                         │
└──────────────────────────────────────────────────────────┘
```

**Components:**
- Header/Top Navigation
- Sidebar (desktop only)
- Main content area
- Bottom navigation (mobile only)
- Theme toggle button (top right)
- User menu (top right)

**Responsive Behavior:**
- Desktop (>1024px): Sidebar + header + main content
- Tablet (768-1024px): Sidebar visible, optimized spacing
- Mobile (<768px): No sidebar, bottom navigation visible

**States:**
- Normal: Full layout visible
- Mobile menu open: Sidebar slides in
- Mobile sheet visible: Content shifts
- Loading: Content skeleton

### Sidebar Component

**Purpose:** Navigation hub for authenticated users

**Visual Design:**
- Width: 280px (desktop)
- Background: Secondary color (slightly lighter)
- Position: Fixed, left side
- Z-index: 40 (below modals)
- Border-right: 1px border

**Structure:**
```
┌────────────────────────────┐
│ Logo (clickable to home)   │  ← 56px height
├────────────────────────────┤
│ Navigation Items:          │  ← Scrollable area
│ 🏆 Empire        [Level 42]│
│ 🎯 Discipline    [Active]  │
│ 💪 Fitness                 │
│ 📚 Study                   │
│ 🧠 Mind                    │
│ 🎬 Creator                 │
│ 🤖 Sentinel AI             │
│                            │
├────────────────────────────┤
│ User Profile Card          │  ← Bottom section
└────────────────────────────┘
```

**Navigation Items:**
- Icon (24px)
- Label text
- Module-specific badge (if active)
- Hover: Highlight background (opacity 10% primary)
- Active: Bold text + accent color + left border (4px)
- Click: Navigate to module page

**Logo:**
- Animated (if animateLogoOnHover)
- Click: Navigate to `/`
- Padding: 16px

**User Profile Card (Bottom):**
- Avatar (40px circle)
- User name
- Level: "Level X"
- Click: Open user menu/settings
- Padding: 16px

**Mobile Behavior:**
- Hidden by default on mobile
- Slide-in drawer on mobile (triggered by menu button)
- Overlay backdrop on mobile
- Swipe to close

### BottomNav Component

**Purpose:** Mobile-first bottom navigation for quick module access

**Visual Design:**
- Height: 64px + safe area (env(safe-area-inset-bottom))
- Position: Fixed, bottom
- Background: Background color (slightly elevated)
- Border-top: 1px border
- Display: Flex, justify-between
- Z-index: 50 (above main content)

**Structure:**
```
┌──────────────┬──────────────┬──────────────┐
│ 🏆 Empire    │ 🎯 Discipline │ 💪 Fitness   │
├──────────────┼──────────────┼──────────────┤
│ 📚 Study     │ 🧠 Mind      │ 🎬 Creator   │
└──────────────┴──────────────┴──────────────┘
```

**Navigation Items:**
- Icon (24px)
- Label (text-xs)
- Active: Icon colored + text primary, background highlight
- Inactive: Icon muted, text muted-foreground
- Touch target: 44px minimum height
- Flex: 1 (equal width distribution)

**Behavior:**
- Single tap: Navigate to module
- Long press: Show tooltip (optional)
- Active indicator: Underline (4px) with primary color

---

## Common UI Components

### Card Component (shadcn/ui)

**Purpose:** Container for grouped information

**Visual Design:**
- Border: 1px border color
- Border-radius: 8px
- Background: Card background color
- Padding: 24px (default)
- Box-shadow: 0 1px 3px rgba(0,0,0,0.12)

**Hover State:**
- Box-shadow: 0 4px 12px rgba(0,0,0,0.15)
- Transform: translateY(-2px) (optional)
- Transition: 300ms

**Variants:**
- Default: Standard card
- Elevated: Larger shadow, more prominent
- Ghost: No border, minimal styling

**Sub-components:**
```
<Card>
  <CardHeader>        ← Title section
    <CardTitle>
    <CardDescription>
  </CardHeader>
  <CardContent>       ← Main content
  <CardFooter>        ← Actions
</Card>
```

### Button Component (shadcn/ui)

**Purpose:** Trigger actions or navigate

**Visual Design:**

**Primary Button:**
- Background: Primary color
- Text: Primary foreground
- Height: 40px (medium)
- Padding: 16px 24px
- Border-radius: 6px
- Font-weight: 500
- Cursor: pointer

**States:**
- Default: Normal appearance
- Hover: Darken 10%, scale 1.02
- Active/Pressed: Scale 0.98, darken 15%
- Disabled: Opacity 50%, cursor: not-allowed
- Loading: Show spinner, disable interaction

**Variants:**
```
Primary    | Solid color, main CTA
Secondary  | Lighter background, secondary action
Outline    | Border only, transparent background
Ghost      | No border, subtle appearance
Destructive| Red background, delete/danger action
Link       | Text-only, underline on hover
```

**Sizes:**
```
Small   | h-8 (32px) | px-3 | text-sm
Medium  | h-10 (40px)| px-4 | text-base (default)
Large   | h-12 (48px)| px-6 | text-base
Icon    | h-10 (40px)| w-10 | Just icon
```

**With Icon:**
```
<Button>
  <IconComponent className="mr-2" />
  Button Text
</Button>
```

### Input Component (shadcn/ui)

**Purpose:** Text input for user data entry

**Visual Design:**
- Height: 40px
- Padding: 12px (left/right)
- Border: 1px border color
- Border-radius: 6px
- Font-size: 16px (prevents zoom on iOS)
- Background: Input background color

**States:**
- Default: Border color visible
- Focus: Ring (2px, primary color), border primary
- Disabled: Opacity 50%, cursor: not-allowed
- Error: Border destructive (red)
- Success: Border success (green)

**Attributes:**
```html
<input
  type="text"
  placeholder="Enter value..."
  disabled={false}
  readOnly={false}
/>
```

### Badge Component (shadcn/ui)

**Purpose:** Display categorical or status information

**Visual Design:**
- Padding: 8px 12px
- Border-radius: 4px
- Font-size: 12px (text-xs)
- Font-weight: 500
- Display: inline-flex
- Align-items: center
- Gap: 4px (if icon included)

**Variants:**
```
Default     | Primary background, primary text
Secondary   | Secondary background
Destructive | Red background, white text
Outline     | Border only, transparent background
```

**With Icon:**
```
<Badge>
  <IconComponent className="h-3 w-3" />
  Badge Text
</Badge>
```

### Dialog Component (shadcn/ui)

**Purpose:** Modal overlay for important actions/information

**Visual Design:**
- Backdrop: rgba(0,0,0,0.5), blur
- Content: Card, max-width 512px
- Position: Center screen
- Z-index: 50

**Structure:**
```
┌─────────────────────────────┐
│ Title          [Close Button]│  ← DialogHeader
├─────────────────────────────┤
│ Dialog Content              │  ← DialogContent
│ (Can be scrollable)         │
├─────────────────────────────┤
│ [Cancel]  [Confirm/Action]  │  ← DialogFooter
└─────────────────────────────┘
```

**Animations:**
- Enter: Scale 0.9 → 1, opacity 0 → 1 (300ms)
- Exit: Scale 1 → 0.9, opacity 1 → 0 (300ms)
- Backdrop: Fade in/out

### Tabs Component (shadcn/ui)

**Purpose:** Switch between related content sections

**Visual Design:**
- Tab buttons: Flex row, evenly spaced
- Active indicator: Underline (2px, primary color)
- Tab padding: 12px 24px
- Border-bottom: 1px border

**States:**
- Default: Gray text, no background
- Hover: Background opacity 5%
- Active: Primary text, underline, background opacity 10%
- Disabled: Muted text, opacity 50%

**Interaction:**
- Click: Change active tab, content transitions
- Keyboard: Arrow keys navigate, Enter/Space selects
- Mouse wheel: Scroll horizontally (if overflow)

### Tooltip Component (radix-ui)

**Purpose:** Contextual help text on hover

**Visual Design:**
- Background: Foreground color
- Text: Background color
- Padding: 8px 12px
- Border-radius: 4px
- Font-size: 12px
- Arrow: Triangle pointing to trigger

**Position:**
- Default: Top of trigger
- Smart: Avoids screen edges

**Behavior:**
- Show: 200ms hover delay
- Hide: Instant on mouse leave
- Mobile: On tap, dismiss on outside tap

### Progress Component

**Purpose:** Show completion percentage

**Visual Design:**
- Height: 4px (thin bar)
- Background: Muted color
- Foreground: Primary color
- Border-radius: 2px (rounded ends)
- Transition: 300ms smooth width change

**States:**
```
<Progress value={65} />  ← 65% complete

Visual:
[███████░░░░░░░░░░░░] 65%
```

**With Label:**
```
┌────────────────────┐
│ Progress Title     │
│ [██████░░░░] 60%   │
└────────────────────┘
```

---

## Study Module Components

### Note Editor (TipTap-based)

**Purpose:** Create and edit rich text notes

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Toolbar:                             │
│ [B] [I] [U] [#] [•] [→] [Code] [...] │ ← Rich text controls
├──────────────────────────────────────┤
│                                      │
│ [Click to edit note...]              │
│ Rich text editor with formatting     │
│                                      │
│                                      │
└──────────────────────────────────────┘
```

**Toolbar Buttons:**
- **Bold** (B): Bold text
- **Italic** (I): Italic text
- **Underline** (U): Underlined text
- **Heading** (#): H1, H2, H3 dropdown
- **Bullet List** (•): Unordered list
- **Numbered List** (1): Ordered list
- **Blockquote** (>): Indented quote
- **Code Block**: Monospace code
- **Link**: Insert hyperlink
- **Image**: Upload image
- **More**: Additional formatting options

**Editor Features:**
- Auto-save (debounced 1s)
- Character count
- Word count
- Placeholder text
- Keyboard shortcuts (Ctrl+B for bold, etc.)
- Undo/Redo (Ctrl+Z / Ctrl+Y)

**States:**
- Empty: Placeholder visible
- Editing: Cursor visible, focus ring
- Saved: No visual change (auto-save)
- Unsaved: Indicator dot (small)

### Flashcard Component

**Purpose:** Display spaced repetition flashcards

**Visual Design:**
```
┌──────────────────────────────┐
│ Question Side (Front):       │
│                              │
│  What is the capital of      │
│  France?                     │
│                              │
│   [Click to reveal answer]   │
│                              │
│   Set 1 of 25 | Review Mode  │
└──────────────────────────────┘

After flip:

┌──────────────────────────────┐
│ Answer Side (Back):          │
│                              │
│  Paris                       │
│                              │
│  [← Remember]  [Forget →]   │
│                              │
│   Set 1 of 25 | Review Mode  │
└──────────────────────────────┘
```

**Front (Question):**
- Large heading (question text)
- Centered layout
- "Click to reveal" hint
- Set counter (bottom)
- Mode indicator (Study / Review / Test)

**Back (Answer):**
- Large heading (answer text)
- Two action buttons:
  - **← Remember** (green): Mark correct
  - **Forgot →** (red): Mark incorrect
- Flip animation: 500ms rotate

**Card Size:**
- Desktop: 600px width × 400px height
- Tablet: 90% width
- Mobile: Full width, min height 300px

**Buttons:**
- Green "Remember" button on correct
- Red "Forgot" button on incorrect
- Hover: Slight scale + shadow
- Disabled: Opacity 50%

### Study Session Timer (Pomodoro)

**Purpose:** Time management with work/break intervals

**Visual Design:**
```
┌────────────────────────────┐
│   Study Session Timer      │
├────────────────────────────┤
│          25:00             │  ← Large countdown
│                            │
│     [████░░░░░░░░░░]       │  ← Progress ring
│                            │
│ Work Session 1 of 4        │
│ Next: 5-minute break       │
│                            │
│ [Start] [Pause] [Reset]    │
└────────────────────────────┘
```

**Timer Display:**
- Font size: 72px
- Font weight: Bold
- Monospace font (tabular-nums)
- Format: MM:SS
- Color: Primary (normal), amber (1 min left), red (time up)

**Progress Ring:**
- Circular SVG
- Circumference: 314px
- Stroke: 8px
- Color: Primary
- Animates as time counts down
- Full rotation = session end

**Status Text:**
- Large: "Work Session 1 of 4"
- Small: "Next: 5-minute break"
- Updates dynamically

**Controls:**
- Start: Begin/resume timer
- Pause: Pause timer
- Reset: Reset to initial time
- Skip: Skip to next phase

**Phases:**
1. Work: 25 minutes (adjustable)
2. Break: 5 minutes (adjustable)
3. Long Break: 15 minutes (after 4 cycles)

**Notifications:**
- Desktop notification when timer ends
- Sound alert
- Visual pulse animation
- Toast message

### Textbook PDF Viewer

**Purpose:** View and annotate PDF textbooks

**Visual Design:**
```
┌──────────────────────────────────────┐
│ [←] Page 42 of 256 [→] [Search] [Zoom] │ ← Controls
├──────────────────────────────────────┤
│                                      │
│  ┌──────────────────────────────┐   │
│  │                              │   │
│  │  PDF Page Content            │   │
│  │  (Rendered PDFs)             │   │
│  │                              │   │
│  └──────────────────────────────┘   │
│                                      │
└──────────────────────────────────────┘
```

**Controls:**
- **Previous Page** (←): Navigate back one page
- **Next Page** (→): Navigate forward one page
- **Page Indicator**: "42 of 256"
- **Zoom Controls**: (-) 100% (+)
- **Search**: Find text in PDF
- **Download**: Save PDF locally
- **Fullscreen**: Expand to full screen

**Annotations:**
- Highlight tool (yellow)
- Notes tool (sticky notes)
- Margin comments
- Bookmark pages
- Export annotations

**Mobile:**
- Swipe left/right to navigate
- Pinch to zoom
- Tap to show/hide controls

### Quiz Question Component

**Purpose:** Present quiz questions with multiple choice/short answer

**Visual Design:**

**Multiple Choice:**
```
┌────────────────────────────────────┐
│ Question 12 of 20                  │ ← Progress
├────────────────────────────────────┤
│                                    │
│ What year did World War II end?   │
│                                    │
│ ○ 1943                             │  ← Radio buttons
│ ○ 1944                             │
│ ● 1945                             │  ← Selected
│ ○ 1946                             │
│                                    │
│         [Next Question →]          │
└────────────────────────────────────┘
```

**Short Answer:**
```
┌────────────────────────────────────┐
│ Question 5 of 20                   │
├────────────────────────────────────┤
│                                    │
│ What is the capital of France?    │
│                                    │
│ [___________________]              │  ← Text input
│                                    │
│  [Submit Answer]                   │
└────────────────────────────────────┘
```

**Matching:**
```
┌────────────────────────────────────┐
│ Match items to definitions:        │
│                                    │
│ Photosynthesis ←→ [2]  Definition A│
│ Respiration    ←→ [1]  Definition B│
│ Osmosis        ←→ [3]  Definition C│
│                                    │
│ [Submit Answers]                   │
└────────────────────────────────────┘
```

**Question Progress:**
- "Question 12 of 20" at top
- Progress bar: [█████░░░░░░░░░░]
- Percentage: 60%

**Response Timing:**
- Instant feedback option (see correct answer immediately)
- Deferred feedback (see all answers at end)
- Time limit per question (with countdown)

---

## Fitness Module Components

### Workout Logger

**Purpose:** Log completed exercises with sets/reps/weight

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Log Workout - [Chest Day]            │
├──────────────────────────────────────┤
│                                      │
│ Exercise Selection:                  │
│ [Bench Press ▼]  [Browse exercises] │
│                                      │
│ Add Sets:                            │
│ Set 1 │ Reps: [5]  │ Weight: [225] lbs
│ Set 2 │ Reps: [5]  │ Weight: [225] lbs
│ Set 3 │ Reps: [3]  │ Weight: [225] lbs  ← Drop set
│       │ [+ Add Set] [- Remove Set]    │
│                                      │
│ Notes: [Felt strong today...]        │
│                                      │
│ [Cancel] [Save Workout]              │
└──────────────────────────────────────┘
```

**Sections:**

**Exercise Dropdown:**
- Searchable dropdown
- Favorite exercises pinned
- Recent exercises shown first
- Browse button: See all exercises
- Custom exercises allowed

**Sets Table:**
- Columns: Set #, Reps, Weight, RPE (Rate of Perceived Exertion)
- Add/remove row buttons
- Quick fill: Copy previous set
- Auto-calculate: 1RM (One Rep Max)

**RPE Scale:**
```
10 → Max effort
9  → Hard, could do 1 more
8  → Moderate, could do 2 more
7  → Fairly easy
6  → Easy
```

**Notes Field:**
- Placeholder: "How did this exercise feel?"
- Character limit: 500
- Rich text support (optional)

### Nutrition Tracker

**Purpose:** Log meals and track macros (Protein, Carbs, Fat)

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Today's Nutrition                    │
├──────────────────────────────────────┤
│                                      │
│ Daily Goal: 2500 cal, 150P 250C 70F │
│                                      │
│ Current: 1850 cal, 120P 180C 52F    │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Calories: [████████░░] 74%       │ │
│ │ Protein:  [██████░░░░] 80%       │ │
│ │ Carbs:    [███████░░░] 72%       │ │
│ │ Fat:      [██████░░░░] 74%       │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Meals Logged:                        │
│ ┌──────────────────────────────────┐ │
│ │ Breakfast (08:00)                │ │
│ │ 2 eggs, 1 toast, 1 coffee        │ │
│ │ 450 cal | 15P 45C 12F            │ │
│ │ [Edit] [Delete]                  │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Add Meal]                         │
└──────────────────────────────────────┘
```

**Daily Summary:**
- Goal macros displayed
- Current intake displayed
- Progress bars for each macro
- Color coding:
  - Green: On track (80-120% of goal)
  - Yellow: Close (60-80% or 120-150%)
  - Red: Off track (< 60% or > 150%)

**Meal Entry:**
- Time: Auto-filled (current time)
- Foods: Add items with quantity
- Autocomplete: Search food database
- Barcode scan: Quick food entry
- Nutrition facts: Display for each food
- Edit/delete buttons

**Barcode Scanner:**
- Use device camera
- Scan barcode
- Auto-populate nutrition
- Manual override available

**Macros Breakdown:**
- Protein: Blue (muscle building)
- Carbs: Orange (energy)
- Fat: Pink (hormone production)
- Fiber: Green (digestive health)

### Measurement Tracker

**Purpose:** Track body measurements and progress photos

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Body Measurements                    │
├──────────────────────────────────────┤
│                                      │
│ Last Updated: 5 days ago             │
│                                      │
│ Chest:   38.5"  (↓ 0.3" from peak)  │
│ Waist:   32.1"  (↓ 0.5" from peak)  │
│ Thighs:  21.2"  (↑ 0.2" from peak)  │
│ Biceps:  14.8"  (↑ 0.1" from peak)  │
│                                      │
│ [+ Log New Measurement]              │
│                                      │
│                                      │
│ Progress Photos:                     │
│ ┌──────────┐ ┌──────────┐ ┌────────┐ │
│ │ Front    │ │ Side     │ │ Back   │ │
│ │ Today    │ │ Today    │ │ Today  │ │
│ └──────────┘ └──────────┘ └────────┘ │
│                                      │
│ [+ Upload Photo] [View Timeline]     │
└──────────────────────────────────────┘
```

**Measurements Table:**
- Editable fields
- Date picker for historical entry
- Unit toggle (inches/cm)
- Trend arrows (up/down/same)
- Peak/trough indicators
- Chart view: Line graph over time

**Progress Photos:**
- Grid layout: 3 columns
- Angle: Front, side, back
- Date overlay on image
- Swipe to compare (before/after)
- Timeline view: See all photos chronologically

### Rest Timer

**Purpose:** Countdown between exercise sets

**Visual Design:**
```
┌────────────────────────────┐
│   Rest Between Sets        │
├────────────────────────────┤
│                            │
│          45 : 02           │  ← Countdown
│                            │
│     [Skip]  [Add 30s]      │
│                            │
│     Ready for next set     │
│                            │
└────────────────────────────┘
```

**Display:**
- Large countdown: MM:SS format
- Next exercise hint below
- Color changes: Green → Yellow → Red as time runs out

**Controls:**
- Skip: Start next set immediately
- Add time: Extend rest by 30s
- Haptic feedback: Vibration at 10s, 5s, 0s

---

## Mind Module Components

### Chess Puzzle Component (Lichess Integration)

**Purpose:** Present chess puzzles for mental training

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Chess Puzzle #2847                   │
│ Rating: 1650 | Streak: 3             │
├──────────────────────────────────────┤
│                                      │
│  ┌────────────────────────────────┐  │
│  │  ♖  ·  ·  ·  ♗  ·  ♔  ·       │  │ ← 8 (rank)
│  │  ·  ♙  ·  ·  ♕  ·  ·  ·       │  │ ← 7
│  │  ·  ·  ♙  ·  ·  ·  ·  ·       │  │ ← 6
│  │  ·  ·  ·  ♖  ·  ·  ·  ·       │  │ ← 5
│  │  ·  ·  ·  ·  ·  ·  ·  ·       │  │ ← 4
│  │  ·  ·  ·  ·  ♟  ·  ·  ·       │  │ ← 3
│  │  ·  ·  ·  ·  ♞  ·  ·  ·       │  │ ← 2
│  │  ·  ·  ·  ·  ·  ·  ♚  ·       │  │ ← 1
│  │  a  b  c  d  e  f  g  h       │  │ ← Files
│  └────────────────────────────────┘  │
│                                      │
│ Your turn to play.                   │
│ Click piece, then destination.       │
│                                      │
│ [Hint]  [Give up]                    │
└──────────────────────────────────────┘
```

**Chessboard:**
- 8×8 grid with alternating colors
- Light squares: #f0d9b5
- Dark squares: #b58863
- Piece symbols (Unicode chess symbols)
- Highlighted squares: Selected piece (yellow), valid moves (green)
- Previous move highlight: Light blue

**Interaction:**
- Click piece to select (highlights valid moves)
- Click destination to move
- Drag piece to destination (mobile-friendly)
- Undo move (Ctrl+Z)

**Hint System:**
- First hint: Show best move (one option)
- Second hint: Show top 3 moves with win percentages
- Third hint: Show full analysis

**Results:**
- Correct: Show congratulation message, award XP
- Incorrect: Show best move, option to try again
- Time limit: Optional, with countdown

### Cube Timer (Speedcubing)

**Purpose:** Time Rubik's cube solves

**Visual Design:**
```
┌────────────────────────────────────┐
│ Cube Timer                         │
├────────────────────────────────────┤
│                                    │
│      [Ready?]                      │ ← Initial state
│      Hold space...                 │
│                                    │
│      24.35s                        │ ← Timer running
│                                    │
│  Best: 18.42s  Avg: 22.31s  Worst: 26.89s
│                                    │
│  Last 5 Solves:                    │
│  24.35s   22.91s   ✓21.87s        │ ← Times list
│  ✗19.12s (DNF)   23.45s           │
│                                    │
│  [Delete Session] [Export Stats]   │
└────────────────────────────────────┘
```

**Timer Display:**
- Large numbers: MM:SS:MS
- Green text: Running
- Red text: Time up (limit exceeded)
- Monospace font (tabular-nums)

**Interaction:**
- Long press spacebar: Warm up timer (10s)
- Release: Start timer
- Hit spacebar again: Stop timer
- Auto-record: Save time to history

**Statistics:**
- Best (PB - Personal Best)
- Average (last 5, 12, 100)
- Worst time
- Standard deviation
- Session count

**Solve History:**
- List of times
- DNF (Did Not Finish): Marked with ✗
- Plus 2: +2 second penalty marked
- Delete individual solves
- Color coding: Green (good), yellow (average), red (slow)

### Stoic Wisdom Display

**Purpose:** Show daily stoic quotes for philosophy

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Daily Stoic Wisdom                   │
│                                      │
│  "The impediment to action           │
│   advances action. What stands       │
│   in the way becomes the way."       │
│                                      │
│  — Marcus Aurelius                   │
│    Meditations                       │
│                                      │
│ [Share] [Save to Collection]         │
│                                      │
│ [← Previous] [Next →]                │
└──────────────────────────────────────┘
```

**Design:**
- Card layout with padding: 32px
- Quote text: Italic, large (18-24px)
- Author: Smaller, semi-bold
- Source: Smaller, muted gray
- Centered alignment

**Features:**
- Daily quote highlighted
- Navigation arrows (previous/next)
- Share button: Copy to clipboard, share to social
- Save button: Save to personal collection
- Like/favorite button

**Collections:**
- Organize by theme
- Favorite/most liked quotes
- Search functionality
- Export as text/PDF

### Memory Palace Component

**Purpose:** Help users build visual memory techniques

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Memory Palace: My House              │
├──────────────────────────────────────┤
│                                      │
│ Rooms:                               │
│                                      │
│ [Front Door]  [Kitchen]  [Bedroom]   │
│ [Bathroom]    [Living Room]          │
│                                      │
│ Selected Room: Front Door            │
│ ┌──────────────────────────────────┐ │
│ │ Items to Remember:               │ │
│ │ 1. Golden apple (bright red)      │ │
│ │ 2. Blue bird (singing loudly)     │ │
│ │ 3. Purple elephant (dancing)      │ │
│ │                                  │ │
│ │ [+ Add Item] [Edit] [Delete]     │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [Review Mode] [Export]               │
└──────────────────────────────────────┘
```

**Palace Navigation:**
- Rooms as clickable buttons
- Room selection highlights active room
- Room editor: Add/remove rooms
- Room name: Editable

**Memory Items:**
- List of items in selected room
- Numbered (memory journey order)
- Editable descriptions (vivid imagery)
- Add item button
- Edit/delete buttons per item

**Visualization:**
- Optional images/icons for items
- Color coding per item
- Size relative to importance

---

## Discipline Module Components

### Daily Task Manager

**Purpose:** Track daily tasks with completion status

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Today's Tasks                        │
│ 12 of 15 completed (80%)             │ ← Progress
├──────────────────────────────────────┤
│                                      │
│ Morning Routine:                     │
│ ☑ Wake up at 6am                     │
│ ☑ Exercise (45 min)                  │
│ ☐ Cold shower                        │
│ ☐ Meditation (10 min)                │
│                                      │
│ Work:                                │
│ ☑ Check emails                       │
│ ☑ Attend team meeting                │
│ ☑ Complete project tasks             │
│ ☐ Send report to manager             │
│                                      │
│ Evening:                             │
│ ☑ Dinner preparation                 │
│ ☑ Spend time with family             │
│ ☐ Read (30 min)                      │
│ ☐ Gratitude journal                  │
│                                      │
│ [+ Add Task]                         │
└──────────────────────────────────────┘
```

**Task Item:**
```
┌────────────────────────────────────┐
│ ☐ Exercise (45 min)                │ ← Checkbox
│                                    │ ← Title
│ ⏰ 18:00  [Category badge]          │ ← Time + category
│ [Edit] [Delete]  [More options]    │
└────────────────────────────────────┘
```

**States:**
- Uncompleted: ☐ (empty box)
- Completed: ☑ (checkmark) + strikethrough text
- Overdue: ⚠ (warning) + red text
- Recurring: 🔄 (icon) + note "Daily"

**Progress Bar:**
- Shows: "12 of 15 completed (80%)"
- Visual bar: [████████░░░]
- Color: Green if > 80%, yellow if 50-80%, red if < 50%

**Task Editor:**
- Modal/slide-in form
- Title field
- Time picker (optional)
- Category selector
- Recurring options
- Priority level (1-5 stars)
- Notes field

### Habit Stack Builder

**Purpose:** Create compound habits by chaining actions

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Habit Stack: Morning Routine         │
│ Streak: 18 days                      │ ← Streak display
├──────────────────────────────────────┤
│                                      │
│ After I [wake up], I will:           │ ← Anchor habit
│                                      │
│ 1. Drink water (drink 500ml)         │ ← Stacked habits
│ 2. Exercise (push-ups 20)            │
│ 3. Cold shower (take 2min)           │
│ 4. Meditate (deep breaths 10)        │
│                                      │
│ [Edit Stack] [Track Today]           │
│ [View History]                       │
└──────────────────────────────────────┘
```

**Habit Chain:**
- Anchor habit: "After I [trigger]"
- Stacked habits: Numbered list
- Connection: "Then I will..."
- All habits trigger together

**Tracking:**
- Single checkbox completes entire stack
- Or check individual habits
- XP reward: Based on stack difficulty
- Streak count: Days completed in a row

**Template Gallery:**
- Pre-made habit stacks
- Categories: Morning, evening, fitness, productivity
- Difficulty levels
- Clone template to customize

### Pomodoro Timer (with Advanced Features)

**Purpose:** Time management technique for focused work

**Visual Design:**
```
┌────────────────────────────────────┐
│ Pomodoro Timer                     │
├────────────────────────────────────┤
│                                    │
│        24:35                       │ ← Countdown
│                                    │
│  [████████░░░░░░░░░░]             │ ← Progress ring
│                                    │
│ Work Session 2 of 4                │
│ Next: 5-minute break               │
│                                    │
│ [Start] [Pause] [Reset]            │
│                                    │
│ Total Focused Time: 48 min         │
│ Sessions Completed: 2              │
│                                    │
│ [Settings] [History]               │
└────────────────────────────────────┘
```

**Timer Settings:**
- Work interval: 25 min (customizable)
- Short break: 5 min
- Long break: 15 min (after 4 cycles)
- Sessions per long break: 4

**Features:**
- Sound notification at end
- Desktop notification
- Visual pulse animation
- Auto-start next phase (optional)
- Focus mode: Hide notifications from other apps

**Statistics:**
- Total focused time (session)
- Completed sessions (count)
- Current streak (consecutive days)
- Weekly/monthly graph

### PMO Tracker (NoFap Support)

**Purpose:** Track sexual health and addiction recovery

**Visual Design:**
```
┌──────────────────────────────────────┐
│ PMO Tracker                          │
│                                      │
│ ⏱ Current Streak: 42 Days            │ ← Streak
│   Longest Streak: 89 Days            │ ← Best
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Timeline (last 90 days)          │ │
│ │ ████░░░░░░░░░░░░░░░░░░░░░░░░░░░ │ │ ← Calendar
│ │ Green = Reset, Black = Activity  │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Health Benefits:                     │
│ ✓ Energy: Recovering (3.5/5)        │
│ ✓ Focus: Improving (4/5)            │
│ ✓ Mood: Excellent (5/5)             │
│                                      │
│ [Log Relapse] [View Journal]         │
│ [Support Resources]                  │
└──────────────────────────────────────┘
```

**Streak Display:**
- Large number: Days
- Emoji: 🔥 (fire at milestones)
- Personal best underneath
- Milestone badges at: 7, 30, 90, 365 days

**Calendar Heatmap:**
- Green: Clean days (no activity)
- Gray: No data
- Red (optional): Relapse/activity days
- Hover: Show day summary

**Health Benefits:**
- Scales track: Energy, focus, mood, confidence, libido
- 1-5 point scale
- Animated on update
- Self-reported (user input)

**Support:**
- Resource links: Articles, communities
- Recovery tips: Daily motivation
- Crisis hotline: 24/7 support

---

## Creator Module Components

### Content Calendar (Kanban Board)

**Purpose:** Plan and organize content across stages

**Visual Design:**
```
┌──────────────────────────────────────────────────────────────┐
│ Content Calendar                                             │
│ [Month View] [Kanban View] [List View] [Analytics View]     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ ┌────────────┬────────────┬────────────┬────────────────┐   │
│ │  IDEATION  │   SCRIPTING│ PRODUCTION │    PUBLISHING  │   │
│ ├────────────┼────────────┼────────────┼────────────────┤   │
│ │ ┌──────┐   │ ┌──────┐   │ ┌──────┐   │ ┌─────────┐    │   │
│ │ │Title │   │ │Title │   │ │Title │   │ │Published│    │   │
│ │ │Desc  │   │ │Script│   │ │Video │   │ │Tue 10am │    │   │
│ │ │Ideas │   │ │Tips  │   │ │In Post  │   │ 2.4K views   │   │
│ │ │[...]  │   │ │[...]  │   │ │[...]  │   │ [...]     │   │
│ │ └──────┘   │ └──────┘   │ └──────┘   │ └─────────┘    │   │
│ │            │            │            │                 │   │
│ │ ┌──────┐   │ ┌──────┐   │ ┌──────┐   │ ┌─────────┐    │   │
│ │ │[+ New Content]        │            │ [Scheduled]    │   │
│ │ └──────┘   │ └──────┘   │ └──────┘   │ └─────────┘    │   │
│ │            │            │            │                 │   │
│ └────────────┴────────────┴────────────┴────────────────┘   │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**Columns:**
1. **Ideation**: Content concepts, brainstorming
2. **Scripting**: Script writing, outline
3. **Production**: Filming, editing, post-processing
4. **Publishing**: Scheduled or published

**Card Layout:**
```
┌───────────────────────┐
│ Content Title         │ ← Title
│                       │
│ Platform: Instagram   │ ← Platform badge
│ Due: Fri 3pm          │ ← Due date
│                       │
│ [👁 Views] [❤ Likes] │ ← Metrics (if published)
│                       │
│ [Edit] [Delete]       │ ← Actions
└───────────────────────┘
```

**Drag & Drop:**
- Drag card between columns
- Reorder within column
- Auto-save on drop
- Undo available

**Filtering:**
- By platform (Instagram, YouTube, TikTok, etc.)
- By category
- By due date
- By status

### Content Idea Vault

**Purpose:** Brainstorm and organize content ideas

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Idea Vault                           │
│ [List View] [Grid View] [Search]     │
├──────────────────────────────────────┤
│                                      │
│ Ideas by Category:                   │
│ [Trending] [Tutorial] [Personal]     │ ← Category filter
│ [Collab] [Series] [Evergreen]        │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Video: "5 Tips for Productivity" │ │
│ │ Category: Tutorial               │ │
│ │ Ideas: • Fast cuts               │ │
│ │        • B-roll examples         │ │
│ │        • Viewer examples         │ │
│ │ Tags: #productivity #tips #time │ │
│ │ Potential: 🔥 High               │ │
│ │ [Move to Kanban] [Edit] [Delete] │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ New Idea]                         │
└──────────────────────────────────────┘
```

**Idea Card:**
- Title
- Category badge
- Description/brainstorm points
- Tags (auto-suggested from trending)
- Potential score: Low/Medium/High/Trending
- Move to production button
- Edit/delete buttons

**Categories:**
- Trending (leverage current trends)
- Tutorial (educational content)
- Personal (personal stories)
- Collaboration (with other creators)
- Series (multi-part content)
- Evergreen (timeless content)

**Organization:**
- Drag to reorder
- Bulk move to category
- Favorite ideas
- Archive old ideas

### Analytics Dashboard

**Purpose:** Track content performance across platforms

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Analytics Dashboard                  │
│ [This Week] [This Month] [All Time] │
├──────────────────────────────────────┤
│                                      │
│ Overview:                            │
│ Total Views: 45,234  (↑ 12%)        │ ← Metrics with trend
│ Total Likes: 3,201   (↑ 8%)         │
│ Total Comments: 487  (↓ 2%)         │
│ Total Shares: 156    (↑ 5%)         │
│                                      │
│ Platform Breakdown:                  │
│ ┌──────────────────────────────────┐ │
│ │ Instagram: 28,342 (62%)          │ │ ← Pie chart
│ │ YouTube: 12,456 (28%)            │ │
│ │ TikTok: 3,891 (9%)               │ │
│ │ Twitter: 545 (1%)                │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Top Performing Content:              │
│ 1. "Productivity Tips" (8,234 views) │
│ 2. "My Morning Routine" (6,123 v)   │
│ 3. "Gym Motivation" (5,456 v)       │
│                                      │
│ [Export Report] [Settings]           │
└──────────────────────────────────────┘
```

**Metrics Display:**
- Large number (views, likes, comments, shares)
- Trend indicator: ↑ (up), ↓ (down), → (same)
- Percentage change from previous period
- Color: Green (positive), Red (negative)

**Charts:**
- Pie chart: Platform breakdown
- Line graph: Views over time
- Bar chart: Content performance
- Heatmap: Best times to post

**Time Range Selector:**
- This week
- This month
- Last 3 months
- This year
- All time
- Custom date range

### Template Editor

**Purpose:** Create reusable templates for content planning

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Create Template                      │
├──────────────────────────────────────┤
│                                      │
│ Template Name: [YouTube Video]       │ ← Name
│ Category: [Video ▼]                  │ ← Category
│                                      │
│ Outline:                             │
│ ┌──────────────────────────────────┐ │
│ │ Hook (first 3 seconds)           │ │
│ │ Problem statement                │ │
│ │ Solution/Tips (3-5 points)       │ │
│ │ Examples/Demo                    │ │
│ │ Call to action                   │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Script Template:                     │
│ [Rich text editor for template]      │
│                                      │
│ Length: [Video ▼] | Platforms:      │
│ ☑ YouTube  ☑ Instagram  ☐ TikTok   │
│                                      │
│ [Save Template] [Preview]            │
└──────────────────────────────────────┘
```

**Features:**
- Customizable outline sections
- Script template with placeholders
- Platform-specific versions
- Length recommendations
- Reuse count tracking

---

## Sentinel AI Module Components

### Chat Interface

**Purpose:** Conversational AI assistant

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Sentinel AI                          │
│ 🤖 Status: Ready  [Normal Mode]      │ ← Mode indicator
├──────────────────────────────────────┤
│                                      │
│ [Chat History] (scrollable)          │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ User:                            │ │
│ │ How can I improve my focus?      │ │ ← User message
│ │ (right-aligned, blue background) │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Sentinel:                        │ │
│ │ To improve your focus, consider: │ │ ← AI message
│ │ 1. Remove distractions...        │ │ (left-aligned)
│ │ 2. Use the Pomodoro technique... │ │
│ │ 3. Take breaks regularly...      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ [Type message.....................] │ ← Input
│ │ [Send Button] [Voice] [More]     │
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

**Message Bubbles:**
- User: Right-aligned, primary color background
- AI: Left-aligned, card background
- Timestamp: Small, muted gray
- Avatar: Small icon (user vs AI)
- Copy button: Appears on hover

**Input Area:**
- Text field: "Type message..."
- Send button: Enabled when text present
- Voice input: Microphone button (future)
- Attach: File/image attachment (future)

**Scrolling:**
- Auto-scroll to latest message
- Scroll to load history (older messages)
- Smooth scrolling behavior

### Conversation Tabs

**Purpose:** Organize multiple conversations

**Visual Design:**
```
┌──────────────────────────────────────┐
│ ┌──────────┐ ┌──────────┐ ┌────────┐ │
│ │ Fitness  │ │ Study    │ │ General│ │ ← Tab buttons
│ │ ✕        │ │ ✕        │ │ ✕      │ │
│ └──────────┘ └──────────┘ └────────┘ │
├──────────────────────────────────────┤
│ (Content of active tab shown)         │
└──────────────────────────────────────┘
```

**Tab Features:**
- Icon: Module-specific icon
- Label: Conversation title (editable)
- Close button: ✕ to close tab
- Active indicator: Underline or highlight
- Click: Switch conversation
- Drag: Reorder tabs (future)

**New Tab:**
- [+] button to create new conversation
- Auto-named with topic
- Can rename immediately

### Mode Selector

**Purpose:** Switch between Normal and Research modes

**Visual Design:**
```
┌──────────────────────────────────────┐
│ Mode: [Normal Mode ▼]                │ ← Dropdown
│                                      │
│ Normal Mode: Fast, cost-effective    │
│ (Uses Groq Llama 3.3)               │
│                                      │
│ Research Mode: Deep, web-connected  │
│ (Uses Perplexity Sonar)             │
│                                      │
│ [Switch to Research]                 │
└──────────────────────────────────────┘
```

**Mode Differences:**

| Aspect | Normal | Research |
|--------|--------|----------|
| Speed | Instant | 2-5 seconds |
| Web Access | No | Yes (citations) |
| Cost | Low | Higher |
| Best For | Quick answers | Deep research |
| Sources | Training data | Live web |

**Indicator:**
- Badge showing current mode
- Color: Green (normal), orange (research)
- Click to toggle
- Confirmation for premium users

### Status Bar

**Purpose:** Show AI system status and usage

**Visual Design:**
```
┌────────────────────────────────────┐
│ 🟢 Connected | Latency: 142ms      │ ← Status
│ Tokens used: 2,341 / 10,000 month  │ ← Usage
└────────────────────────────────────┘
```

**Indicators:**
- Connection: 🟢 (connected), 🟡 (slow), 🔴 (error)
- Latency: Response time in milliseconds
- Token usage: Current month usage
- Typing indicator: Shows when AI is responding

### Context Memory Display

**Purpose:** Show conversation context summary

**Visual Design:**
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
│                                      │
│ [+ Add Context] [Clear Memory]       │
└──────────────────────────────────────┘
```

**Memory Items:**
- Bullet points of known preferences
- Editable (click to modify)
- Deletable (✕ button)
- Add new button: [+ Add Context]
- Clear all button: Reset memory

**Auto-collected:**
- From module activities
- From previous conversations
- From profile settings
- From user manual input

---

## Component States & Animations

### Button States

**All Button Variants:**

1. **Default**
   - Full opacity
   - Normal shadow
   - Normal cursor (pointer)

2. **Hover**
   - Scale: 1.02x
   - Shadow increases
   - Color slightly darkens
   - Transition: 200ms ease

3. **Active/Pressed**
   - Scale: 0.98x
   - Shadow decreases
   - Color darkens more
   - Transition: 100ms ease

4. **Disabled**
   - Opacity: 50%
   - Cursor: not-allowed
   - No hover effect
   - Grayed out appearance

5. **Loading**
   - Show spinner
   - Text hidden/faded
   - Disabled interaction
   - Button height unchanged

### Input States

**All Input Variants:**

1. **Default**
   - Border: 1px border color
   - Background: Input background
   - Cursor: text
   - Placeholder visible

2. **Focus**
   - Ring: 2px primary color
   - Border: 1px primary color
   - Placeholder: Hidden
   - Cursor: text

3. **Filled**
   - Value visible
   - Cursor: text
   - Border: 1px border color
   - No placeholder

4. **Disabled**
   - Opacity: 50%
   - Background: Muted
   - Cursor: not-allowed
   - Border: Muted

5. **Error**
   - Ring: 2px destructive
   - Border: 1px destructive
   - Error message below
   - Color: Red text

6. **Success**
   - Ring: 2px success (green)
   - Border: 1px success
   - Check icon visible
   - Color: Green text

### Common Animations

**Fade In/Out**
- Opacity: 0 → 1 (in), 1 → 0 (out)
- Duration: 300ms
- Easing: ease-in-out

**Slide Up/Down**
- Transform: translateY(20px) → translateY(0)
- Duration: 400ms
- Easing: ease-out

**Scale**
- Transform: scale(0.9) → scale(1)
- Duration: 300ms
- Easing: cubic-bezier(0.4, 0, 0.2, 1)

**Rotate**
- Transform: rotate(0deg) → rotate(360deg)
- Duration: 2s (continuous)
- Easing: linear (for spinner)

---

## Responsive Component Behavior

### Desktop (>1024px)
- Full-width components
- Hover states active
- Sidebar visible
- Multiple columns
- Tooltip support

### Tablet (768-1024px)
- 2-column layouts
- Sidebar collapses on scroll
- Touch-friendly sizes
- Bottom sheets for forms

### Mobile (<768px)
- 1-column stack
- No sidebar (drawer on menu)
- Full-width cards
- 44px+ touch targets
- Bottom navigation visible

---

## Accessibility for Components

### Keyboard Support
- Tab navigation through all interactive elements
- Enter/Space to activate buttons
- Arrow keys in menus/lists
- Escape to close modals/dropdowns

### Screen Reader Support
- aria-label on icon buttons
- aria-describedby for help text
- role attributes where needed
- Semantic HTML (button, link, etc.)

### Focus Management
- Visible focus indicator (2px ring)
- Focus outline color matches theme
- Focus order logical (LTR, top-bottom)
- Trap focus in modals

### Color Contrast
- All text: 4.5:1 minimum
- Large text: 3:1 minimum
- UI components: 3:1 minimum
- Icons: 3:1 minimum

---

## Summary

This comprehensive guide covers:
- **Layout components**: AppLayout, Sidebar, BottomNav
- **Common components**: Card, Button, Input, Badge, Dialog, Tabs, etc.
- **Module-specific components**: Detailed for all 6 major modules
- **Component states**: Default, hover, active, disabled, loading, error, success
- **Animations**: Transitions, keyframe animations, effects
- **Responsive behavior**: Adapts to desktop, tablet, mobile
- **Accessibility**: Full WCAG AA compliance support

All components follow the design system specifications and maintain visual consistency across the application.

**Last Updated:** December 18, 2025
**Maintained By:** Product & Design Team
