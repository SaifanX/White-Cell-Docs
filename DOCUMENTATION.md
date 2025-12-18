# Documentation Page

**File Path:** `src/pages/Documentation.tsx` (346 lines)

## Overview

The Documentation page serves as an in-app help and learning hub. It provides users with comprehensive guides about the platform, module overviews, integration instructions, getting started steps, and key features—all accessible without leaving the app.

## Page Purpose

- **Primary Function:** In-app user guide and documentation hub
- **Target Users:** All users (new and experienced)
- **Key Actions:** Read guides, explore modules, learn features
- **Alternative to:** External documentation sites
- **Access:** From help menu or navigation

## Page Sections

### Section 1: Navigation & Header

**Back Button:**
- "← Back to Home"
- Click navigates to `/`
- Top left positioning

**Page Header:**
- Book icon (large, primary color)
- Title: "Documentation"
- SEO-optimized heading

**Subtitle:**
- "Everything you need to know about White Cell Protocol"
- Gray text (muted-foreground)
- Supporting description

### Section 2: Getting Started

**Card Layout:**

```
┌─────────────────────────────────┐
│ ⚡ Getting Started              │
├─────────────────────────────────┤
│ Follow these steps to begin     │
│ your journey...                 │
│                                 │
│ ① Create Your Account           │
│ ② Complete Onboarding          │
│ ③ Explore Modules              │
│ ④ Connect Integrations         │
│ ⑤ Start Building               │
└─────────────────────────────────┘
```

**5 Getting Started Steps:**

**Step 1: Create Your Account**
- Description: "Sign up with email OTP authentication or continue as a guest to explore."
- Action: Visual numbered badge (1)
- Badge color: Primary

**Step 2: Complete Onboarding**
- Description: "Set your goals, preferences, and customize your experience."
- Action: Onboarding wizard
- Personalization enabled

**Step 3: Explore Modules**
- Description: "Navigate through different modules and discover features that fit your needs."
- Action: Browse 6 core modules
- Self-discovery

**Step 4: Connect Integrations**
- Description: "Link third-party services like GitHub, Notion, and Spotify in Settings."
- Action: Settings → Integrations
- Expand capabilities

**Step 5: Start Building**
- Description: "Begin tracking tasks, workouts, study sessions, and watch your progress grow."
- Action: Log first activity
- Gamification starts

**Visual Design:**
- Numbered circles (1-5)
- Staggered animations on load
- Flex layout vertical
- Padding around each step
- Muted background for each step box

### Section 3: Core Modules

**7 Module Cards:**

**Grid Layout:**
- Desktop: 2-column grid
- Tablet: 2-column grid
- Mobile: 1-column stack
- Gap: 24px

**Module Layout:**

```
┌─────────────────────────────────┐
│ 🏆 Empire Dashboard             │
│ Your command center for         │
│ tracking progress...            │
├─────────────────────────────────┤
│ [Real-time stats badge]         │
│ [Progress tracking badge]       │
│ [Gamification badge]            │
│ [Leaderboards badge]            │
└─────────────────────────────────┘
```

**7 Modules Documented:**

1. **Empire Dashboard**
   - Icon: 🏆 Trophy (yellow)
   - Description: "Your command center for tracking progress, viewing stats, and managing your entire protocol."
   - Features: Real-time stats, Progress tracking, Gamification, Leaderboards

2. **Discipline Engine**
   - Icon: 🎯 Target (red)
   - Description: "Build unbreakable habits with daily tasks, streak tracking, and PMO monitoring."
   - Features: Daily tasks, Streak tracking, PMO tracker, Habit rewards

3. **Study & Build**
   - Icon: 📖 BookOpen (blue)
   - Description: "Master learning with notes, flashcards, Pomodoro timer, and code sandbox."
   - Features: Rich text notes, Flashcards, Pomodoro timer, Code sandbox, GitHub integration

4. **Fitness & Combat**
   - Icon: 💪 Dumbbell (green)
   - Description: "Track workouts, log nutrition, scan barcodes, and monitor body measurements."
   - Features: Workout logging, Macro tracking, Barcode scanner, Exercise library

5. **Mind & Strategy**
   - Icon: 🧠 Brain (purple)
   - Description: "Sharpen your mind with chess puzzles, cube timer, speed reading, and logic games."
   - Features: Chess puzzles, Cube timer, Speed reading, Memory palace, Debate practice

6. **Creator Hub**
   - Icon: 🎬 Video (pink)
   - Description: "Plan content, manage scripts, track analytics, and organize your creative workflow."
   - Features: Content calendar, Script templates, Analytics, Idea generation

7. **Sentinel AI**
   - Icon: 🤖 Bot (cyan)
   - Description: "Your tactical AI assistant with military precision for guidance and motivation."
   - Features: AI chat, Personalized advice, Context awareness, Multi-model support

**Card Components:**
- Icon with color
- Title
- Description (2-3 sentences)
- Feature badges (4 per module)
- Hover: Border highlight
- Click: Navigate to module

### Section 4: Integrations

**4 Integration Cards:**

**Integration Structure:**

```
┌─────────────────────────────────┐
│ 🐙 GitHub                       │
│ Connect your GitHub account     │
│ to import repositories...       │
└─────────────────────────────────┘
```

**Integrations:**

1. **GitHub**
   - Icon: 🐙 Github
   - Description: "Connect your GitHub account to import repositories and sync code projects."
   - Action: Settings → Integrations

2. **Notion**
   - Icon: 📄 FileText
   - Description: "Sync tasks and notes with your Notion workspace for seamless productivity."
   - Action: Settings → Integrations

3. **Spotify**
   - Icon: 🎵 Music
   - Description: "Control music playback and create focus playlists directly from the app."
   - Action: Settings → Integrations

4. **Lichess**
   - Icon: 🛡️ Shield
   - Description: "Import chess games and practice puzzles from your Lichess account."
   - Action: Settings → Integrations

**Grid Layout:**
- Desktop: 2-column grid
- Mobile: 1-column
- Gap: 24px

### Section 5: Key Features

**Feature Grid:**

```
┌──────────────────────┐  ┌──────────────────────┐
│ 🏆 Gamification      │  │ 🤖 AI-Powered        │
│ Earn XP, level up... │  │ Get personalized...  │
└──────────────────────┘  └──────────────────────┘
```

**4 Key Feature Blocks:**

1. **Gamification** (🏆 Trophy yellow)
   - Description: "Earn XP, level up, unlock achievements, and compete on leaderboards."

2. **AI-Powered** (🤖 Bot cyan)
   - Description: "Get personalized insights, recommendations, and assistance from Sentinel AI."

3. **Real-time Sync** (⚡ Zap yellow)
   - Description: "All your data syncs instantly across devices with Convex backend."

4. **Privacy First** (🛡️ Shield green)
   - Description: "Your data is encrypted and secure. We never sell your information."

**Grid Layout:**
- 2-column on all sizes
- Gap: 16px
- Card with padding
- Icon on left, text on right
- Clean typography

### Section 6: Help CTA

**Support Card:**

```
┌─────────────────────────────────┐
│ Need More Help?                 │
│                                 │
│ Can't find what you're looking  │
│ for? Our support team is here.  │
│                                 │
│ [Contact Support] [Back Home]   │
└─────────────────────────────────┘
```

**Design:**
- Gradient background (primary/10)
- Centered text
- Two action buttons
- Call-to-action positioning

**Buttons:**
- "Contact Support" → `/support`
- "Back to Home" → `/`

## State Management

**Documentation State:**
```typescript
const navigate = useNavigate();

// Mostly static content with navigation
// Minimal state required
```

**Navigation Handlers:**
```typescript
const handleBackClick = () => navigate("/");
const handleModuleClick = (path: string) => navigate(path);
const handleSupportClick = () => navigate("/support");
```

## Animations

**Page Load:**
- Hero section: Fade + slide in
- Sections: Staggered animations
- Items within sections: Cascading animations
- Duration: 300-600ms
- Easing: ease-in-out

**Card Animations:**
- Initial: opacity: 0, y: 20
- Animate: opacity: 1, y: 0
- Transition: 300ms with delay

**Interaction Animations:**
- Hover: Border color change
- Click: Scale effect
- Smooth transitions

## SEO

**Meta Tags:**
- Title: "Documentation - White Cell Protocol | Complete User Guide & Tutorials"
- Description: "Complete guide to using White Cell Protocol. Learn about all modules, features, integrations, and how to maximize your productivity. Step-by-step tutorials for discipline tracking, fitness logging, study tools, and more."
- Keywords: "white cell protocol documentation, user guide, tutorial, how to use, app manual, discipline tracker guide, fitness app tutorial, study tools guide"
- Type: article

**Content Optimization:**
- Semantic HTML
- Descriptive headings
- Keyword-rich descriptions
- Internal linking
- Comprehensive coverage

## Responsive Design

**Mobile (< 640px):**
- 1-column layouts
- Full-width cards
- Vertical button stacks
- Larger touch targets
- Reduced padding

**Tablet (640-1024px):**
- 2-column grids
- Standard spacing
- Balanced layouts

**Desktop (> 1024px):**
- 2-column grids for modules
- Full hover states
- Larger text
- Optimized spacing

## Accessibility

- Semantic heading hierarchy
- ARIA labels on buttons
- Color + icons for features
- Sufficient contrast
- Keyboard navigation
- Focus indicators
- Screen reader support
- Alt text equivalents

## Performance

**Optimizations:**
- Static content (no queries)
- Minimal state management
- Lazy load with Framer Motion
- Optimized icon rendering
- CSS-based animations where possible

**Metrics:**
- FCP: < 1.5s
- LCP: < 2s
- CLS: < 0.05

## Related Components

- `Button.tsx` - Navigation buttons
- `Card.tsx` - Section containers
- `Badge.tsx` - Feature badges
- `SEOHead.tsx` - Meta tags
- `motion` div - Animations

## Related Pages

- `/support` - Get help
- `/features` - Feature showcase
- `/` - Home page
- Individual module pages (Study, Fitness, etc.)

## Future Enhancements

- [ ] Interactive tutorials
- [ ] Video walkthroughs
- [ ] Live chat support
- [ ] FAQ section expansion
- [ ] Search functionality
- [ ] Dark mode specific docs
- [ ] Video embeds
- [ ] Downloadable guides

## Technical Stack

- **Framework:** React + TypeScript
- **Routing:** React Router
- **Styling:** Tailwind CSS
- **Animations:** Framer Motion
- **Icons:** Lucide React
- **UI Components:** shadcn/ui
- **SEO:** Custom SEOHead component
- **State:** Minimal (navigation only)
