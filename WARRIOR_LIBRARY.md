# Warrior Library Page

**File Path:** `src/pages/WarriorLibrary.tsx` (321 lines)

## Overview

The Warrior Library is a curated resource hub containing admin-approved books, courses, tools, and videos for personal development. It serves as a knowledge repository where users can access quality learning materials filtered by difficulty, category, and type, with features for bookmarking and resource viewing.

## Page Purpose

- **Primary Function:** Curated learning resource discovery
- **Target Users:** All authenticated users
- **Key Actions:** Search resources, filter by category/type, bookmark, view content
- **Resource Types:** Books, courses, tools, videos
- **Admin Control:** Resources curated and reviewed by admins

## Resource Types

**4 Resource Categories:**

### Type 1: Books (📖)
- Digital or physical books
- Include: Self-help, personal development, discipline, fitness
- Features: PDF viewer, bookmarking, progress tracking
- Example: "Atomic Habits", "The 4-Hour Body", "Meditations"

### Type 2: Courses (🎓)
- Online learning platforms
- Topics: Skill development, knowledge domains
- Features: Course links, progress tracking
- Example: YouTube courses, Udemy, educational content

### Type 3: Tools (🔧)
- Software tools and productivity apps
- Productivity, fitness tracking, note-taking
- Features: External links, guides, integration info
- Example: "Obsidian", "Fitbod", "MyFitnessPal"

### Type 4: Videos (🎬)
- Video content and tutorials
- Motivational, educational, documentary
- Features: Embedded players, playlists
- Example: Documentaries, tutorials, speeches

## Resource Data Model

**Resource Schema:**

```typescript
{
  _id: Id<"libraryResources">,
  title: string,
  description: string,
  type: "book" | "course" | "tool" | "video",
  category: "discipline" | "fitness" | "mind" | "study" | "creator" | "general",
  difficulty: "beginner" | "intermediate" | "advanced",
  estimatedTime: string (e.g., "2 hours", "3 weeks", "Ongoing"),
  tags: string[] (5-10 tags),
  author: string,
  featured: boolean,
  rating: number (1-5),
  ratingCount: number,
  fileId?: string (Convex file storage ID),
  url?: string (external link),
  audioOverviewUrl?: string (MP3 audio),
  createdAt: Date,
  updatedAt: Date,
  userBookmarks: Id<"users">[] (who bookmarked)
}
```

## Page Sections

### Section 1: Header

**Page Header:**
- BookOpen icon (large, primary color)
- Title: "Warrior Library"
- Subtitle: "Curated resources for total mastery"
- Visual emphasis with icon color

### Section 2: Search & Filters

**Search Functionality:**

```typescript
{
  searchQuery: string,
  selectedCategory: string | undefined,
  selectedType: string | undefined
}
```

**Search Bar:**
- Search icon on left
- Placeholder: "Search resources..."
- Input debounced (500ms)
- Searches: title, description, tags
- Real-time results

**Type Filter Buttons:**

| Type | Icon | Label | Default |
|------|------|-------|---------|
| Books | BookOpen | Books | Available |
| Courses | GraduationCap | Courses | Available |
| Tools | Wrench | Tools | Available |
| Videos | Video | Videos | Available |

**Features:**
- Toggle buttons (click to filter)
- Show only selected type
- All types shown when none selected
- Button highlight on selection

**Category Badges:**

```typescript
[
  { value: "discipline", label: "Discipline", icon: TrendingUp },
  { value: "fitness", label: "Fitness", icon: TrendingUp },
  { value: "mind", label: "Mind", icon: TrendingUp },
  { value: "study", label: "Study", icon: GraduationCap },
  { value: "creator", label: "Creator", icon: Video },
  { value: "general", label: "General", icon: BookOpen }
]
```

**Features:**
- Click badge to filter by category
- Click again to remove filter
- Only show resources matching category
- Visual highlight on selected category

### Section 3: Resource Grid

**Grid Layout:**
- Desktop: 3-column grid (lg:grid-cols-3)
- Tablet: 2-column grid (md:grid-cols-2)
- Mobile: 1-column stack
- Gap: 24px between cards
- Responsive sizing

**Resource Card:**

```
┌─────────────────────────────────┐
│ [Icon]        [Bookmark Button] │
│ Resource Title                  │
│ Short description (2 lines)     │
├─────────────────────────────────┤
│ [Type Badge] [Difficulty] [Tag] │
│ ⏱️ Estimated Time                │
│ [Tag1] [Tag2] [Tag3]            │
│ [Access Resource Button]        │
└─────────────────────────────────┘
```

**Card Components:**

**Header:**
- Resource type icon (top left)
- Bookmark button (top right, toggles fill)
- Title (text-lg font-semibold)
- Description (line-clamp-2, text-muted-foreground)

**Badges:**
- Type badge: Capitalized type (secondary)
- Difficulty badge: Color-coded
  - Beginner: Green
  - Intermediate: Yellow
  - Advanced: Red
- Featured badge: Gold star (if featured)

**Estimated Time:**
- Clock icon + duration
- Examples: "2 hours", "4 weeks", "Ongoing"
- Shows user time commitment

**Tags:**
- First 3 tags displayed (slice(0, 3))
- Small outline badges
- Clickable for filtering (future feature)

**Access Button:**
- "Access Resource"
- Full width
- Click opens resource viewer dialog
- Disabled if no content available

### Section 4: Resource Viewer Dialog

**Modal Display:**

**Header:**
- BookOpen icon
- Resource title
- Description (one-liner)
- Close button

**Content Tabs:**

**Tab 1: Audio Overview (if available)**
- Headphones icon
- HTML5 audio player
- Controls: Play/pause, progress, volume
- MP3 format
- Full width player

**Tab 2: PDF Viewer (if fileId present)**
- FileText icon
- Open in New Tab button (external)
- Embedded iframe (height: 500px)
- PDF viewer controls built-in
- Full width with scroll

**Tab 3: External URL (if url present, no file)**
- ExternalLink icon
- "Open Resource" button
- Opens in new tab (_blank)
- Window.open() call

**Tab 4: Empty State (if no content)**
- BookOpen icon (large, faded)
- "No content available for this resource"
- Centered text

**Dialog Properties:**
- Max width: 4xl (56rem)
- Max height: 90vh
- Scrollable content
- Overflow hidden for frame

## State Management

**Library State:**

```typescript
const [searchQuery, setSearchQuery] = useState("");
const [selectedCategory, setSelectedCategory] = useState<string | undefined>();
const [selectedType, setSelectedType] = useState<string | undefined>();
const [selectedResource, setSelectedResource] = useState<any>(null);
const [pdfUrl, setPdfUrl] = useState<string | null>(null);

// Queries
const resources = useQuery(api.library.getResources, {
  searchQuery: searchQuery || undefined,
  category: selectedCategory,
  type: selectedType,
});

const bookmarks = useQuery(api.library.getUserBookmarks);
const getPDFUrl = useQuery(
  api.library.getPDFUrl,
  selectedResource?.fileId ? { fileId: selectedResource.fileId } : "skip"
);

// Mutations
const toggleBookmark = useMutation(api.library.toggleBookmark);
```

## Features

### Bookmarking System

**Bookmark Toggle:**
```typescript
const handleBookmark = async (resourceId: Id<"libraryResources">) => {
  try {
    await toggleBookmark({ resourceId });
    toast.success("Bookmark updated");
  } catch (error: any) {
    toast.error(error.message);
  }
};
```

**Visual Feedback:**
- Empty bookmark icon (outline)
- Filled bookmark icon when bookmarked
- Primary color fill
- Click toggles state
- Instant visual feedback

**Use Cases:**
- Save favorite resources
- Quick access in future
- Track reading progress
- Personal collection building

### Difficulty Color Coding

```typescript
const getDifficultyColor = (difficulty: string) => {
  switch (difficulty) {
    case "beginner": return "bg-green-500/10 text-green-500";
    case "intermediate": return "bg-yellow-500/10 text-yellow-500";
    case "advanced": return "bg-red-500/10 text-red-500";
    default: return "bg-gray-500/10 text-gray-500";
  }
};
```

**Color Meaning:**
- Green: Beginner friendly
- Yellow: Some prerequisites
- Red: Advanced knowledge required

### Search Implementation

**Real-Time Filtering:**
- Debounced input (500ms)
- Searches across fields:
  - Title
  - Description
  - Tags
- Case-insensitive matching
- Results update as user types

**Filter Combination:**
- Search + Category + Type
- All filters work together
- AND logic (must match all)
- Empty results show helpful message

### Featured Resources

**Identification:**
- Gold star badge with "Featured"
- Higher priority in search results
- Curated by admins
- Highlighted for visibility

**Use Case:**
- Recommend top resources
- Promote high-quality content
- Seasonal recommendations
- New releases

## Empty States

**No Search Results:**
```
[BookOpen icon - large, faded]
"No resources found"
"Try adjusting your filters or search query"
```

**No Bookmarks:**
- User has no bookmarked resources
- Encourages exploration
- Show suggestions

## Animations

**Page Load:**
- Resource cards: Staggered fade + slide up
- Delay: 50ms between cards
- Duration: 300ms each

**Dialog Animations:**
- Open: Scale + fade in
- Close: Scale + fade out
- Smooth transitions

**Interactions:**
- Hover: Card border highlight
- Click: Button scale effect
- Bookmark: Instant icon fill

## Mobile Responsiveness

**Mobile (< 640px):**
- 1-column grid
- Full-width search
- Stack type filters vertically
- Larger touch targets (44px+)
- Bottom sheet dialog option

**Tablet (640-1024px):**
- 2-column grid
- Side-by-side filters
- Standard dialog sizing

**Desktop (> 1024px):**
- 3-column grid
- Horizontal filter layout
- Full modal dialog
- Hover states enabled

## Accessibility

- Semantic HTML (article, section)
- ARIA labels on buttons
- Keyboard navigation (Tab)
- Focus indicators on inputs
- Screen reader friendly
- Color + text for difficulty
- Sufficient contrast ratios
- Form inputs with labels

## Performance

**Optimizations:**
- Lazy load dialog content
- Debounce search input
- Pagination (future)
- Image optimization
- Memoized resource cards
- Virtual scrolling (for large lists)

**Metrics:**
- FCP: < 2s
- LCP: < 3.5s
- CLS: < 0.1

## Database Queries

```typescript
// Get resources with filters
useQuery(api.library.getResources, {
  searchQuery?: string,
  category?: string,
  type?: string
})

// Get user bookmarks
useQuery(api.library.getUserBookmarks)

// Get PDF download URL
useQuery(api.library.getPDFUrl, { fileId: string })

// Toggle bookmark
useMutation(api.library.toggleBookmark, { resourceId })

// Rate resource (future)
useMutation(api.library.rateResource, { resourceId, rating })
```

## Related Components

- `Card.tsx` - Resource card wrapper
- `Badge.tsx` - Category/type/difficulty badges
- `Button.tsx` - Action buttons
- `Dialog.tsx` - Resource viewer modal
- `Input.tsx` - Search input
- `SEOHead.tsx` - Meta tags

## Admin Features (Future)

- [ ] Resource submission queue
- [ ] Bulk import from sources
- [ ] Resource editing
- [ ] Rating/approval system
- [ ] Analytics on resource usage
- [ ] Featured content management
- [ ] Duplicate detection
- [ ] Content moderation

## SEO

**Meta Tags:**
- Title: "Warrior Library - White Cell Protocol | Curated Books, Courses & Resources"
- Description: "Access the Warrior Library - a curated collection of books, courses, tools, and videos for personal growth, discipline, fitness, and mastery. Admin-approved resources for warriors."
- Keywords: "warrior library, personal growth books, online courses, productivity tools, fitness resources, discipline books, self improvement resources"

## Future Features

- [ ] User ratings and reviews
- [ ] Reading/course progress tracking
- [ ] Recommendation algorithm
- [ ] Import lists from external sources
- [ ] Resource sharing with friends
- [ ] Study groups for courses
- [ ] Discussion forums
- [ ] Certificate tracking
- [ ] Reading time estimates
- [ ] Audiobook support

## Technical Stack

- **State:** React useState
- **Data:** Convex queries & mutations
- **File Storage:** Convex file storage (PDFs)
- **Dialog:** shadcn/ui Dialog
- **Search:** Backend search implementation
- **Filtering:** Real-time client-side filters
- **Styling:** Tailwind CSS
- **Icons:** Lucide React
- **Animations:** Framer Motion
