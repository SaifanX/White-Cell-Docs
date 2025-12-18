# Creator Module

**File Path:** `src/pages/Creator.tsx` (1,193 lines)

## Overview

The Creator Hub is a complete content creation management system designed for creators, influencers, and social media managers. It provides tools for content planning, ideation, production workflow management (Kanban board), AI-assisted content generation, analytics tracking, trend research, and unified content scheduling across all major platforms.

## Page Purpose

- **Primary Function:** Content creation and creator business management platform
- **Target Users:** Content creators, influencers, social media managers, entrepreneurs
- **Core Feature:** Production pipeline (Kanban workflow) + AI content generation
- **Integration:** Unified calendar, analytics dashboard, Sentinel AI coaching

## Key Features

### 1. Content Plan Management

**Create Content Plan:**
```typescript
{
  platform: "instagram" | "youtube" | "tiktok" | "twitter",
  title: string,
  script: string (optional),
  caption: string (optional),
  status: "idea" | "scripted" | "filmed" | "edited" | "published",
  createdAt: Date,
  updatedAt: Date
}
```

**Content Operations:**
- Create with platform, title, script, caption
- Update status to move through workflow
- Delete when no longer needed
- Search and filter by platform/status
- Display on workflow Kanban board

**Workflow Pipeline:**
- **Idea:** Raw concept stage
- **Scripted:** Finalized script ready for production
- **Filmed:** Video recorded, waiting editing
- **Edited:** Post-production complete
- **Published:** Live on platform with analytics

### 2. Idea Vault - Brainstorming System

**Save Content Idea:**
```typescript
{
  title: string,
  description: string,
  format: "reels" | "shorts" | "threads" | "podcasts" | "posts",
  tags: string[],
  priority: 1-5,
  createdAt: Date
}
```

**Idea Features:**
- Unlimited idea capture
- Format-specific ideas
- Comma-separated tagging
- Priority slider (1-5)
- Edit and delete anytime
- Bulk archive (planned)
- Browse past ideas to avoid duplicates

**Idea Priority Levels:**
- 5 = Must-create-now (urgent, trending)
- 4 = High priority (important, aligned with strategy)
- 3 = Medium (nice-to-have, opportunistic)
- 2 = Low (backlog, someday)
- 1 = Maybe (experimental, uncertain)

### 3. Content Templates System

**Template Types:**
```typescript
{
  name: string,
  platform: "instagram" | "youtube" | "tiktok" | "twitter",
  templateType: "caption" | "script" | "hook",
  content: string,
  createdAt: Date
}
```

**Template Usage:**
- Reusable formats for consistency
- {variable} placeholders (e.g., {topic}, {cta})
- AI fills variables when generating content
- Platform-specific optimizations

**Example Templates:**
- **Instagram Caption:** "Want to know [TOPIC]? Tap the link in bio to [CTA]"
- **YouTube Hook:** "If you [ACTION], watch this before [DATE]"
- **TikTok Hook:** "[EMOTION] [STATEMENT]. Here's why..."
- **Twitter Thread:** "🧵 [TOPIC] explained in 5 tweets..."

### 4. Analytics Dashboard

**Add Content Analytics:**
```typescript
{
  contentId: string,
  platform: string,
  views: number,
  likes: number,
  comments: number,
  shares: number,
  saves: number (optional),
  watchTime: number (optional, for video),
  engagementRate: number (auto-calculated),
  date: Date
}
```

**Engagement Rate Calculation:**
```
Engagement Rate = (Likes + Comments + Shares) / Views * 100
```

**Dashboard Metrics:**

| Metric | Purpose |
|--------|---------|
| Total Views | Reach measurement |
| Total Likes | Engagement indicator |
| Total Comments | Community interaction |
| Avg Engagement % | Overall performance |
| Content Count | Volume tracking |

**Performance Ranking:**
- Top-performing content (by engagement rate)
- Worst-performing content (opportunities)
- Platform-specific rankings
- Time-based comparisons (week, month, all-time)

**Analytics Time Range:**
- Week (last 7 days)
- Month (last 30 days)
- All Time (lifetime data)

### 5. AI-Powered Content Generation

**Caption & Title Generator:**
```typescript
Input:
  topic: string,
  platform: string,
  keywords: string[] (optional)

Output:
  title: string,
  caption: string,
  hashtags: string[]
```

**How It Works:**
1. Enter topic (e.g., "productivity hacks")
2. Select platform (Instagram, YouTube, etc.)
3. Add keywords (optional, e.g., ["ai", "automation"])
4. AI generates platform-optimized content
5. Review and copy to content plan

**Platform Optimization:**
- Instagram: Max 2,200 characters, hashtag strategy
- TikTok: Trending sounds, viral hooks
- YouTube: SEO-optimized descriptions
- Twitter: Character limit (280), threading

**Hashtag Research (Expensive - Perplexity API):**
- Returns 15-20 recommended hashtags
- Mix: high-volume, medium, niche
- Platform-specific (hashtag strategy differs)
- Costs ~$0.50-$1.00 per query

**Daily Limits:**
- Caption generation: 10/day (cost control)
- Hashtag research: 5/day (expensive API)
- User notified when limit reached
- Resets at midnight UTC

### 6. Trend Scanner & Research

**Research Trends:**
```typescript
Platform: instagram,
Niche: (optional) fitness,
Result: "5-10 trending topics with descriptions"
```

**Trend Research (Perplexity API):**
- Real-time web search for trends
- Niche-specific filtering
- AI summarizes top trends
- Provides brief explanations
- Actionable trend ideas

**Use Case:**
1. "What's trending on TikTok in fitness?"
2. AI returns: "Protein shake recipes (#1), 5-minute workouts (#2)..."
3. Create idea from trend
4. Build content plan
5. Create and publish

**Expensive Operation:**
- Uses Perplexity API ($0.50-$1.00 per query)
- Cache results (planned)
- Daily limit (planned)

### 7. Production Workflow (Kanban Board)

**5-Stage Pipeline:**

```
┌─────────┬──────────┬────────┬────────┬───────────┐
│  Idea   │ Scripted │ Filmed │ Edited │ Published │
├─────────┼──────────┼────────┼────────┼───────────┤
│ Content1│ Content2 │ Cont3  │ Cont4  │ Cont5, 6  │
│ Content2│ Content3 │ Cont4  │        │ Cont7     │
└─────────┴──────────┴────────┴────────┴───────────┘
```

**Board Features:**
- Drag-and-drop cards between stages (planned)
- Card shows title, platform, delete button
- Badge shows count per stage
- Filter by platform
- Color-coded by content type

**Workflow Insights:**
- Identify bottlenecks (e.g., 20 in "Filmed" stage)
- See underutilized stages
- Plan content pacing from bottlenecks

### 8. Calendar Integration & Scheduling

**Unified Content Calendar:**
- Visual month/week/day view
- Show all content plans
- Color-coded by platform
- Drag-to-schedule (planned)
- Set publish dates

**ICS Export (Planned):**
- Export calendar to Google Calendar, Outlook
- Sync deadlines with personal calendar
- Set reminders for filming, editing, publishing

### 9. User Interface & Navigation

**7 Major Tabs:**

| Tab | Content | Use Case |
|-----|---------|----------|
| Dashboard | Analytics overview | Review performance |
| Workflow | Kanban board | Manage pipeline |
| Ideas | Idea vault | Brainstorm content |
| Templates | Saved templates | Find format |
| AI Tools | Generation + research | Create content |
| Trends | Trend scanner | Discover topics |
| Calendar | Content calendar | Schedule publishing |

**Navigation:**
- Click tabs on desktop
- Swipe left/right on mobile
- Alt+← → keyboard shortcuts
- Mobile shows compact tab icons

**Responsive Design:**
- Mobile: Full-width, single column
- Tablet: 2 columns, optimized spacing
- Desktop: Multi-column, max-width container

### 10. Dialogs & Modals

**Create Content Dialog:**
- Large form (scrollable)
- Platform selector
- Title input
- Script textarea (5 rows)
- Caption textarea (3 rows)
- Status selector (idea → published)
- Create button

**Create Idea Dialog:**
- Idea title
- Description textarea
- Format selector (reels, shorts, threads, etc.)
- Tags input (comma-separated)
- Priority slider (1-5)
- Save button

**Create Template Dialog:**
- Template name
- Platform selector
- Template type (caption, script, hook)
- Content textarea with variable hints
- Create button

**Add Analytics Dialog:**
- Content selector (dropdown from published)
- Views input
- Likes input
- Comments input
- Shares input
- Saves input (optional)
- Add button

## State Management

**Major State Variables:**

```typescript
// Content planning
const [platform, setPlatform] = useState("instagram");
const [title, setTitle] = useState("");
const [script, setScript] = useState("");
const [caption, setCaption] = useState("");
const [status, setStatus] = useState("idea");

// Idea vault
const [ideaTitle, setIdeaTitle] = useState("");
const [ideaDescription, setIdeaDescription] = useState("");
const [ideaFormat, setIdeaFormat] = useState("reels");
const [ideaTags, setIdeaTags] = useState("");
const [ideaPriority, setIdeaPriority] = useState(3);

// Templates
const [templateName, setTemplateName] = useState("");
const [templatePlatform, setTemplatePlatform] = useState("instagram");
const [templateType, setTemplateType] = useState("caption");
const [templateContent, setTemplateContent] = useState("");

// Analytics
const [analyticsContentId, setAnalyticsContentId] = useState("");
const [analyticsViews, setAnalyticsViews] = useState("");
const [analyticsLikes, setAnalyticsLikes] = useState("");
const [analyticsComments, setAnalyticsComments] = useState("");
const [analyticsTimeRange, setAnalyticsTimeRange] = useState("month");

// AI Tools
const [aiTopic, setAiTopic] = useState("");
const [aiPlatform, setAiPlatform] = useState("instagram");
const [aiKeywords, setAiKeywords] = useState("");
const [aiLoading, setAiLoading] = useState(false);
const [trendNiche, setTrendNiche] = useState("");
const [hashtagTopic, setHashtagTopic] = useState("");
const [trendResults, setTrendResults] = useState("");
const [hashtagResults, setHashtagResults] = useState("");

// Filters
const [filterPlatform, setFilterPlatform] = useState("all");
const [filterStatus, setFilterStatus] = useState("all");
const [activeMainTab, setActiveMainTab] = useState("dashboard");
```

## Convex Queries & Mutations

**Queries:**
- `api.content.getContentPlans` - All content plans
- `api.creatorIdeas.getIdeas` - Idea vault
- `api.creatorTemplates.getTemplates` - Saved templates
- `api.creatorAnalytics.getAnalyticsDashboard` - Performance metrics
- `api.creatorAnalytics.getContentAnalytics` - Specific content stats

**Mutations:**
- `api.content.createContentPlan` - Create content
- `api.content.deleteContentPlan` - Delete content
- `api.content.updateContentPlan` - Update status
- `api.creatorIdeas.createIdea` - Save idea
- `api.creatorIdeas.deleteIdea` - Delete idea
- `api.creatorTemplates.createTemplate` - Save template
- `api.creatorTemplates.deleteTemplate` - Delete template
- `api.creatorAnalytics.addContentAnalytics` - Log analytics

**Actions (AI-Powered):**
- `api.creatorAI.generateCaptionAndTitle` - Groq LLM (caption/title)
- `api.creatorAI.researchTrends` - Perplexity API (trends)
- `api.creatorAI.researchHashtags` - Perplexity API (hashtags)

## XP & Gamification

**XP Awards:**
- Create content plan: 5 XP
- Publish content: 10 XP
- Save idea: 2 XP
- Create template: 10 XP
- Log analytics: 3 XP (per entry)
- Achieve 1K views: 25 XP
- Achieve 10% engagement rate: 50 XP

**Streaks:**
- Daily publishing streak
- Weekly content plan streak
- Template creation streak

**Badges:**
- "Content Creator" (first content)
- "Idea Vault" (50 ideas)
- "Template Master" (10 templates)
- "Viral" (1K views on one post)
- "Engagement King" (10% engagement rate)

## Keyboard Shortcuts

- `Ctrl+N`: Create new content
- `Ctrl+I`: Create new idea
- `Ctrl+T`: Create new template
- `Alt+← →`: Navigate tabs

## Mobile Features

- **Pull-to-Refresh:** Swipe to refresh data
- **Haptic Feedback:** On content creation, tab switching
- **Swipe Navigation:** Between tabs
- **Bottom Sheet:** Modals for full-screen input

## Accessibility

- Color contrast for status badges
- Icon + text on buttons (no icon-only)
- Keyboard navigation for all features
- Screen reader support for analytics numbers
- Large touch targets (56px minimum)

## Performance Optimizations

- Debounced hashtag/trend search
- Content grid virtualization for large lists
- Analytics queries filtered server-side
- Cached template suggestions

## Related Components

- **UnifiedCalendar:** Content scheduling
- **AIChatbot:** Creator coaching
- **Analytics Dashboard:** Performance visualization

## Integration Points

1. **Discipline Module:** Log content creation as task
2. **Study Module:** Create educational content from notes
3. **Mind Module:** Strategy content from library
4. **Sentinel AI:** Content coaching and strategy
5. **XP System:** All content operations award XP

## Future Features

1. **Advanced Analytics:**
   - A/B testing framework
   - Competitor analysis
   - Audience insights

2. **Publishing Integration:**
   - Direct platform publishing
   - Buffer/Hootsuite integration
   - Auto-scheduling

3. **AI Enhancement:**
   - Thumbnail generation
   - Video script voiceover
   - Automated editing suggestions

4. **Community:**
   - Hire freelance editors
   - Content marketplace
   - Collaboration tools

## Technical Stack

- **Frontend:** React 18, TypeScript
- **Backend:** Convex (mutations, queries)
- **AI:** Groq (captions), Perplexity (trends/hashtags)
- **Real-time:** Convex auto-sync queries
- **UI:** shadcn/ui components
- **Animations:** Framer Motion
- **Styling:** Tailwind CSS
