# Icons Reference - Complete Icon Catalog

**Version:** 1.0
**Framework:** Lucide React Icons
**Last Updated:** December 18, 2025

---

## Overview

Complete reference of all Lucide icons used throughout White Cell Protocol. Includes icon names, usage contexts, sizing guidelines, and implementation examples.

---

## Navigation & UI Icons

### Primary Navigation

| Icon | Name | Lucide ID | Usage | Size |
|------|------|-----------|-------|------|
| ![home](data:image/svg+xml;base64,) | Home | `Home` | Main dashboard link | 24px |
| ![book](data:image/svg+xml;base64,) | Book | `BookOpen` | Study module | 24px |
| ![dumbbell](data:image/svg+xml;base64,) | Dumbbell | `Dumbbell` | Fitness module | 24px |
| ![brain](data:image/svg+xml;base64,) | Brain | `Brain` | Mind module | 24px |
| ![target](data:image/svg+xml;base64,) | Target | `Target` | Discipline module | 24px |
| ![pencil-square](data:image/svg+xml;base64,) | Pencil Square | `PencilSquare` | Creator module | 24px |
| ![sparkles](data:image/svg+xml;base64,) | Sparkles | `Sparkles` | AI/Sentinel module | 24px |

**Implementation:**
```typescript
import { Home, BookOpen, Dumbbell, Brain, Target, PencilSquare, Sparkles } from 'lucide-react';

<nav>
  <Home size={24} className="text-gray-700" />
  <BookOpen size={24} className="text-blue-600" />
  <Dumbbell size={24} className="text-green-600" />
</nav>
```

---

### Header & Common

| Icon | Name | Usage | Context |
|------|------|-------|---------|
| `Menu` | Menu icon | Mobile hamburger menu | Top navigation |
| `X` | Close | Close dialogs, modals | Pop-ups |
| `ChevronDown` | Dropdown | Expandable menus | Dropdowns |
| `ChevronRight` | Next | Forward navigation | Breadcrumbs |
| `Search` | Search | Search functionality | Header |
| `Bell` | Notifications | Notification icon | Header |
| `Settings` | Settings | Settings page | User menu |
| `User` | User profile | User menu, profile | Header |
| `LogOut` | Logout | Logout action | User menu |

```typescript
// Header Icons
import { Menu, X, ChevronDown, Search, Bell, Settings, User, LogOut } from 'lucide-react';

// Header component
<header className="flex items-center gap-4">
  <Menu size={24} />
  <Search size={20} className="text-gray-600" />
  <Bell size={20} />
  <User size={20} />
</header>
```

---

## Study Module Icons

### Core Study Icons

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `BookMarked` | Bookmarked | Saved notes | Blue-500 |
| `PenTool` | Note creation | Create note button | Blue-600 |
| `Lightbulb` | Idea/Concept | Key concepts | Yellow-500 |
| `List` | Notes list | View all notes | Gray-700 |
| `Grid3x3` | Card grid | Flashcard view | Gray-700 |
| `RotateCw` | Review | Spaced repetition | Blue-600 |
| `CheckCircle2` | Mastered | Completed flashcards | Green-600 |
| `Clock` | Time tracking | Study duration | Gray-600 |
| `Bookmark` | Pin note | Pin functionality | Orange-500 |
| `Archive` | Archive | Archive notes | Gray-600 |

**Study-specific implementation:**
```typescript
import { BookMarked, PenTool, Lightbulb, RotateCw, CheckCircle2 } from 'lucide-react';

// Flashcard component
<div className="card">
  <Button icon={<RotateCw size={16} />}>Review Again</Button>
  <Button icon={<CheckCircle2 size={16} className="text-green-600" />}>Master</Button>
</div>
```

### Quiz & Assessment Icons

| Icon | Name | Usage |
|------|------|-------|
| `MessageSquare` | Quiz/Test | Quiz icon |
| `Zap` | Quick quiz | Fast quiz mode |
| `CheckCircle` | Correct answer | Right response feedback |
| `XCircle` | Wrong answer | Incorrect response |
| `AlertCircle` | Warning | Knowledge gap alert |
| `TrendingUp` | Progress | Score improvement |

---

## Fitness Module Icons

### Workout Icons

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `Activity` | Cardio | Cardio workouts | Green-600 |
| `Dumbbell` | Strength | Strength training | Orange-600 |
| `Zap` | Intensity | High intensity | Red-600 |
| `Clock` | Duration | Workout time | Gray-600 |
| `Flame` | Calories | Calories burned | Red-500 |
| `TrendingUp` | PR | Personal record | Green-600 |
| `Award` | Achievement | Workout achievement | Gold-500 |
| `CheckCircle` | Complete | Workout done | Green-600 |
| `Calendar` | Schedule | Planned workout | Blue-600 |

```typescript
import { Activity, Dumbbell, Flame, TrendingUp } from 'lucide-react';

// Workout log
<div className="workout-entry">
  <Activity size={20} className="text-green-600" />
  <Flame size={20} className="text-red-500" />
  <span>450 kcal</span>
</div>
```

### Nutrition Icons

| Icon | Name | Usage |
|------|------|-------|
| `Apple` | Food/Nutrition | Food/meal icon |
| `Droplet` | Water intake | Hydration tracking |
| `PieChart` | Macros | Macro breakdown |
| `BarChart3` | Nutrition stats | Statistics |
| `Target` | Goal | Nutrition goal |

### Body Tracking

| Icon | Name | Usage |
|------|------|-------|
| `Weight` | Scale/Weight | Weight tracking |
| `Ruler` | Measurement | Body measurements |
| `Image` | Photo | Progress photo |
| `Camera` | Photo capture | Take photo |

---

## Mind Module Icons

### Chess & Puzzles

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `Crown` | Chess | Chess/puzzle icon | Purple-600 |
| `Lightbulb` | Hint | Puzzle hint | Yellow-500 |
| `RotateCw` | Retry | Attempt again | Gray-600 |
| `CheckCircle2` | Solved | Puzzle complete | Green-600 |
| `Zap` | Difficulty | Puzzle rating | Orange-500 |
| `AlertTriangle` | Mistake | Wrong move | Red-600 |

```typescript
import { Crown, Lightbulb, CheckCircle2 } from 'lucide-react';

// Chess puzzle
<div className="puzzle-card">
  <Crown size={24} className="text-purple-600" />
  <Button icon={<Lightbulb size={16} />}>Get Hint</Button>
  <Button icon={<CheckCircle2 size={16} className="text-green-600" />}>Solve</Button>
</div>
```

### Speedcubing

| Icon | Name | Usage |
|------|------|-------|
| `Timer` | Timer | Cube solve timer |
| `BarChart3` | Statistics | Solve statistics |
| `TrendingDown` | Best time | Personal best |
| `Award` | Record | New record |

### Strategy & Wisdom

| Icon | Name | Usage |
|------|------|-------|
| `BookOpen` | Strategy | Strategy document |
| `MessageCircle` | Quote | Wisdom quote |
| `Lightbulb` | Insight | Key insight |
| `Bookmark` | Save | Save strategy |

---

## Discipline Module Icons

### Habits & Tasks

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `CheckSquare2` | Completed task | Task done | Green-600 |
| `Square` | Pending task | To-do item | Gray-400 |
| `AlertCircle` | Overdue | Past deadline | Red-600 |
| `Flag` | Priority | High priority | Red-500 |
| `Clock` | Scheduled | Scheduled time | Gray-600 |
| `Repeat` | Recurring | Daily/recurring | Blue-600 |

```typescript
import { CheckSquare2, Square, Flag, AlertCircle } from 'lucide-react';

// Task item
<div className="task">
  <CheckSquare2 size={20} className="text-green-600" />
  <span>Complete homework</span>
  <Flag size={16} className="text-red-500" />
</div>
```

### Streaks & Motivation

| Icon | Name | Usage |
|------|------|-------|
| `Flame` | Streak | Daily streak count |
| `AlertTriangle` | Warning | Streak at risk |
| `Award` | Achievement | Streak milestone |
| `Zap` | Energy | Motivation/energy |
| `Heart` | Motivation | Motivational message |
| `TrendingUp` | Progress | Improvement trend |

### Timers

| Icon | Name | Usage |
|------|------|-------|
| `Timer` | Pomodoro | Pomodoro timer |
| `RotateCw` | Reset | Reset timer |
| `Pause` | Pause | Pause timer |
| `Play` | Start | Start timer |
| `Clock` | Time | Time display |

---

## Creator Module Icons

### Content Creation

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `FileText` | Document | Content idea | Blue-600 |
| `PenTool` | Edit | Edit content | Gray-600 |
| `Share2` | Publish | Publish/share | Green-600 |
| `Eye` | Views | View count | Gray-600 |
| `MessageCircle` | Comments | Comment count | Gray-600 |
| `Heart` | Likes | Like count | Red-500 |
| `Repeat2` | Retweets/Shares | Share count | Blue-600 |

```typescript
import { FileText, PenTool, Share2, Eye, Heart } from 'lucide-react';

// Content card
<div className="content-card">
  <FileText size={20} className="text-blue-600" />
  <Button icon={<PenTool size={16} />}>Edit</Button>
  <div className="analytics">
    <Eye size={16} /> 2.5K
    <Heart size={16} /> 450
    <Share2 size={16} /> 120
  </div>
</div>
```

### Platform Icons

| Icon | Name | Platform |
|------|------|----------|
| `Youtube` | YouTube | YouTube video |
| `Instagram` | Instagram | Instagram post |
| `Twitter` | Twitter | Twitter/X post |
| `Linkedin` | LinkedIn | LinkedIn article |
| `Figma` | Figma | Design file |

### Kanban Board

| Icon | Name | Usage |
|------|------|-------|
| `MoreHorizontal` | Menu | Card options |
| `Trash2` | Delete | Remove card |
| `Copy` | Duplicate | Copy content |
| `GripHorizontal` | Drag | Draggable handle |

---

## AI & Chat Icons

### AI/Sentinel Icons

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `MessageCircle` | Chat | Chat/conversation | Cyan-600 |
| `Zap` | AI | AI/intelligence | Yellow-500 |
| `Brain` | Intelligence | Smart/AI feature | Purple-600 |
| `Sparkles` | Magic | AI generated | Purple-500 |
| `Send` | Send | Send message | Cyan-600 |
| `Mic` | Voice | Voice input | Gray-600 |
| `Copy` | Copy | Copy message | Gray-600 |
| `ThumbsUp` | Helpful | Helpful response | Green-600 |
| `ThumbsDown` | Unhelpful | Not helpful | Red-600 |

```typescript
import { MessageCircle, Send, Copy, ThumbsUp, Sparkles } from 'lucide-react';

// Chat bubble
<div className="chat-message">
  <Sparkles size={16} className="text-purple-500" />
  <span>Message content</span>
  <div className="actions">
    <Button icon={<Copy size={16} />}>Copy</Button>
    <Button icon={<ThumbsUp size={16} className="text-green-600" />}>Helpful</Button>
  </div>
</div>
```

### Voice & Interaction

| Icon | Name | Usage |
|------|------|-------|
| `Mic` | Voice input | Start voice |
| `MicOff` | Muted | Mic disabled |
| `Volume2` | Audio | Sound on |
| `VolumeX` | Muted | Sound off |

---

## Status & Feedback Icons

### Success & Validation

| Icon | Name | Usage | Color |
|------|------|-------|-------|
| `CheckCircle2` | Success | Success state | Green-600 |
| `Check` | Verified | Checkmark | Green-600 |
| `CheckCircle` | Complete | Task complete | Green-600 |
| `AlertCircle` | Info | Information | Blue-500 |
| `AlertTriangle` | Warning | Warning state | Yellow-600 |
| `XCircle` | Error | Error state | Red-600 |
| `X` | Close/Error | Failed action | Red-600 |

```typescript
import { CheckCircle2, AlertCircle, AlertTriangle, XCircle } from 'lucide-react';

// Status message
<div className="feedback success">
  <CheckCircle2 size={20} className="text-green-600" />
  <span>Operation successful</span>
</div>

<div className="feedback error">
  <XCircle size={20} className="text-red-600" />
  <span>Error occurred</span>
</div>
```

### Loading & Progress

| Icon | Name | Usage |
|------|------|-------|
| `Loader` | Loading | Loading spinner |
| `Loader2` | Loading alt | Loading animation |
| `RefreshCw` | Refreshing | Refresh action |
| `RotateCw` | Syncing | Sync in progress |
| `BarChart3` | Progress | Progress indicator |
| `Circle` | Loading | Progress dot |

---

## Action Icons

### Common Actions

| Icon | Name | Usage | Size |
|------|------|-------|------|
| `Plus` | Add | Create new | 20px |
| `Minus` | Remove | Delete item | 20px |
| `Edit2` | Edit | Edit content | 20px |
| `Trash2` | Delete | Delete item | 20px |
| `Download` | Download | Export/download | 20px |
| `Upload` | Upload | File upload | 20px |
| `Copy` | Copy | Copy to clipboard | 20px |
| `Share2` | Share | Share content | 20px |
| `Bookmark` | Save | Bookmark/save | 20px |
| `Star` | Favorite | Mark favorite | 20px |

```typescript
import { Plus, Edit2, Trash2, Share2, Bookmark } from 'lucide-react';

// Toolbar
<toolbar>
  <Button icon={<Plus size={20} />}>Add</Button>
  <Button icon={<Edit2 size={20} />}>Edit</Button>
  <Button icon={<Trash2 size={20} />}>Delete</Button>
  <Button icon={<Share2 size={20} />}>Share</Button>
  <Button icon={<Bookmark size={20} />}>Save</Button>
</toolbar>
```

### Navigation Actions

| Icon | Name | Usage |
|------|------|-------|
| `ChevronLeft` | Back | Previous page |
| `ChevronRight` | Forward | Next page |
| `ChevronsLeft` | Start | Go to beginning |
| `ChevronsRight` | End | Go to end |
| `ExternalLink` | Open | Open in new window |
| `ArrowUpRight` | External | External link |

---

## Filter & Organization Icons

| Icon | Name | Usage |
|------|------|-------|
| `Filter` | Filter | Filter options |
| `SlidersHorizontal` | Settings | Adjust settings |
| `LayoutGrid` | Grid view | Card layout |
| `List` | List view | List layout |
| `Maximize2` | Expand | Expand view |
| `Minimize2` | Collapse | Collapse view |
| `GitBranch` | Tag | Category tag |
| `Tag` | Label | Item label |

---

## Sizing Guidelines

### Icon Sizes

| Size | Usage | Examples |
|------|-------|----------|
| **16px** | Inline icons | Labels, badges, small buttons |
| **20px** | Standard buttons | Action buttons, toolbar |
| **24px** | Navigation | Main nav, headers |
| **32px** | Large buttons | Hero buttons, CTAs |
| **48px** | Feature icons | Feature cards, hero |
| **64px** | Large graphics | Onboarding, empty states |

```typescript
// Icon sizing examples
<Home size={16} />      // Label icon
<Home size={20} />      // Button icon
<Home size={24} />      // Navigation
<Home size={32} />      // Large action
<Home size={48} />      // Feature card
```

---

## Color Guidelines

### Icon Colors

Icons inherit text color by default or use specific colors:

```typescript
// Default (inherit text color)
<Home size={24} /> {/* Uses parent text color */}

// Specific colors
<Home size={24} className="text-blue-600" />
<Heart size={20} className="text-red-500" />
<CheckCircle2 size={20} className="text-green-600" />
<Flame size={20} className="text-orange-500" />
<Brain size={20} className="text-purple-600" />
<Zap size={20} className="text-yellow-500" />
```

### Dark Mode

Icons automatically adapt in dark mode with CSS:

```css
[data-theme="dark"] svg {
  color: #e5e7eb; /* Light gray in dark mode */
}

[data-theme="dark"] .text-blue-600 {
  color: #60a5fa; /* Lighter blue in dark mode */
}
```

---

## Common Icon Combinations

### Status Indicators

```typescript
// Success
<CheckCircle2 size={20} className="text-green-600" />

// Error
<XCircle size={20} className="text-red-600" />

// Warning
<AlertTriangle size={20} className="text-yellow-600" />

// Info
<AlertCircle size={20} className="text-blue-500" />

// Loading
<Loader2 size={20} className="animate-spin text-blue-600" />
```

### CTA Buttons

```typescript
// Primary action
<Button>
  <Plus size={20} className="mr-2" />
  Create New
</Button>

// Danger action
<Button variant="danger">
  <Trash2 size={20} className="mr-2" />
  Delete
</Button>

// External link
<a href="#">
  View More
  <ExternalLink size={16} className="ml-1" />
</a>
```

### Metrics Display

```typescript
// Workout stats
<div className="flex gap-4">
  <div>
    <Activity size={20} className="text-green-600" />
    <span>45 min</span>
  </div>
  <div>
    <Flame size={20} className="text-red-500" />
    <span>450 kcal</span>
  </div>
  <div>
    <Zap size={20} className="text-orange-500" />
    <span>High</span>
  </div>
</div>
```

---

## Animation & Effects

### Spinning Icons (Loading)

```typescript
import { Loader2 } from 'lucide-react';

<Loader2 size={24} className="animate-spin" />
```

### Pulsing Icons (Attention)

```typescript
import { Bell } from 'lucide-react';

<Bell size={24} className="animate-pulse text-red-500" />
```

### Bounce Icons (Notifications)

```css
@keyframes bounce-icon {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-4px); }
}

.icon-bounce {
  animation: bounce-icon 0.6s infinite;
}
```

---

## Icon Selection Guide

**Choosing the right icon:**

1. **Clarity**: Icon should be immediately recognizable
2. **Consistency**: Match existing icon style
3. **Appropriateness**: Use semantic icons (✓ for success, ✗ for error)
4. **Visibility**: Ensure icon is visible at its size
5. **Simplicity**: Avoid overly complex icons
6. **Context**: Icon should relate to its action

---

## Complete Icon List

```typescript
// All imported icons
import {
  // Navigation
  Home, BookOpen, Dumbbell, Brain, Target, PencilSquare, Sparkles,
  Menu, X, ChevronDown, ChevronRight, ChevronLeft, Search, Bell, 
  Settings, User, LogOut,
  
  // Study
  BookMarked, PenTool, Lightbulb, List, Grid3x3, RotateCw,
  CheckCircle2, Clock, Bookmark, Archive, MessageSquare, Zap,
  CheckCircle, XCircle, AlertCircle, TrendingUp,
  
  // Fitness
  Activity, Flame, Award, Calendar, Apple, Droplet, PieChart,
  BarChart3, Weight, Ruler, Image, Camera,
  
  // Mind
  Crown, AlertTriangle, Timer, MessageCircle, BookOpen,
  
  // Discipline
  CheckSquare2, Square, Flag, Repeat, Heart,
  Play, Pause,
  
  // Creator
  FileText, Edit2, Share2, Eye, Heart as HeartIcon, Repeat2,
  MoreHorizontal, Trash2, Copy, GripHorizontal,
  
  // AI
  Send, Mic, ThumbsUp, ThumbsDown, MicOff, Volume2, VolumeX,
  
  // Status
  Check, AlertTriangle, Loader, Loader2, RefreshCw,
  
  // Actions
  Plus, Minus, Edit2 as EditIcon, Download, Upload, Maximize2,
  Minimize2, GitBranch, Tag,
  
  // Layout
  LayoutGrid, Filter, SlidersHorizontal
} from 'lucide-react';
```

---

## Figma Integration

Icons are available in the Figma design file:
- File: White Cell Protocol Design System
- Page: Icons
- All icons at 24px, 32px, 48px sizes
- Light and dark mode versions included

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Icon Library:** Lucide React v0.latest
**Maintained By:** Design Team
