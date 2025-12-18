# Landing Page

**File Path:** `src/pages/Landing.tsx`

## Overview

The Landing page is the public-facing homepage for unauthenticated users. It showcases the White Cell Protocol platform with hero section, feature highlights, testimonials, and call-to-action buttons designed to drive user registration.

## Page Purpose

- **Primary Function:** Marketing and user acquisition landing page
- **Target Users:** Unauthenticated visitors, potential new users
- **Key Action:** Drive signup (CTA: "Get Started")
- **SEO Focus:** High optimization with structured data and meta tags

## Components Included

### 1. SEO & Structured Data

**SEO Head Meta Tags:**
```typescript
<SEOHead
  title="White Cell Protocol - Personal Growth & Life Optimization..."
  description="Transform your life with White Cell Protocol - the ultimate gamified..."
  keywords="white cell protocol, personal growth app, discipline tracker, fitness..."
/>
```

**Structured Data Schemas:**
- `OrganizationSchema` - Organization details for Google
- `WebApplicationSchema` - App capabilities and features
- `FAQSchema` - Common questions and answers

**Benefits:**
- Rich snippets in search results
- Enhanced Google Knowledge Panel
- Social media card previews
- Schema validation for SEO compliance

### 2. Navigation & Theme

**Header Elements:**
- Top-right: Theme Toggle (dark/light mode)
- Fixed position (z-50) so always visible when scrolling
- No hamburger menu needed (landing page is one-page scroll)

### 3. Hero Section

**Hero Content:**
```
Logo (100x100px, animated)
Title: "White Cell Protocol"
Subtitle: "Your personal operating system for total life domination"
CTAs: 
  - "Get Started" (or "Go to Empire" if authenticated)
  - "View All Modules"
```

**Design:**
- Parallax scroll effect: `useTransform(scrollY, [0, 300], [0, 100])`
- Y-axis movement on scroll for depth
- Center-aligned text with max-width constraint
- Large gradient text on main heading
- Animated entrance on page load

**Buttons:**
```typescript
// CTA Button Logic
onClick={() => navigate(isAuthenticated ? "/empire" : "/auth")}

// Secondary Button
onClick={() => navigate("/dashboard")}
```

### 4. Feature Showcase Grid

**8 Major Features Displayed:**

| Feature | Icon | Color | Link |
|---------|------|-------|------|
| Discipline Engine | Target | Red | `/discipline` |
| Study & Build | BookOpen | Blue | `/study` |
| Fitness & Combat | Dumbbell | Green | `/fitness` |
| Mind & Strategy | Brain | Purple | `/mind` |
| Creator Hub | Video | Pink | `/creator` |
| Sentinel AI | Bot | Cyan | `/sentinel` |
| Warrior Library | Library | Orange | `/library` |
| Empire Dashboard | Trophy | Yellow | `/empire` |

**Card Features:**
- Icon with custom color
- Title and description
- Optional "New" badge for latest features
- Hover scale effect (1.05x)
- Link to feature page

### 5. Testimonials Section

**Example Testimonials:**

1. **Alex Chen - Software Engineer**
   - Quote: "White Cell Protocol transformed my daily routine..."
   - Rating: ⭐⭐⭐⭐⭐

2. **Sarah Johnson - Content Creator**
   - Quote: "The AI features are incredible. It's like having a personal coach..."
   - Rating: ⭐⭐⭐⭐⭐

3. **Marcus Williams - Entrepreneur**
   - Quote: "Finally, an all-in-one platform that actually delivers..."
   - Rating: ⭐⭐⭐⭐⭐

**Display Format:**
- Star rating (1-5 stars)
- Author name and role
- Quote text with markdown support

### 6. "Why White Cell" Value Props

**4 Core Value Propositions:**

| Value | Icon | Description |
|-------|------|-------------|
| **Discipline** | Target | Build unbreakable habits and routines |
| **Intelligence** | Brain | Sharpen your mind with strategic tools |
| **Strength** | Dumbbell | Achieve peak physical performance |
| **Productivity** | Zap | Maximize output with AI assistance |

### 7. Active Warriors Counter

**Real-time User Count:**
```typescript
const totalUsers = useQuery((api as any).users.getTotalUsers);
```

- Displays total registered users on platform
- Updates in real-time from Convex
- Builds social proof
- Example: "10,000+ warriors building their empire"

## Layout & Sections

**Full Page Structure:**

```
Landing Page (One-page scroll)
├── Theme Toggle (fixed top-right)
├── Hero Section
│   ├── Logo (animated)
│   ├── Main Heading (gradient text)
│   ├── Subheading
│   └── CTA Buttons (2)
├── Warriors Counter Section
│   └── "Join X warriors..."
├── Features Grid Section
│   └── 8 feature cards (responsive grid)
├── Why White Cell Section
│   └── 4 value prop cards
├── Testimonials Section
│   └── 3 testimonial cards with ratings
└── Footer Section (planned)
```

## Animation & Interactions

### Scroll Animations

```typescript
const { scrollY } = useScroll();
const y = useTransform(scrollY, [0, 300], [0, 100]);
// Hero content moves up as user scrolls down
```

### Component-Level Animations

- **Hero fade-in:** `initial={{ opacity: 0, y: 30 }}` → `animate={{ opacity: 1, y: 0 }}`
- **Heading scale:** `initial={{ opacity: 0 }}` → scale animation
- **Staggered children:** Each feature card staggered by index
- **Button interactions:** `whileHover={{ scale: 1.05 }}` `whileTap={{ scale: 0.95 }}`

### Duration & Timing

- Hero animation: 0.8s
- Heading: 0.2s delay
- Subtitle: 0.4s delay
- Feature cards: staggered 0.1s between each
- Smooth easing (Framer Motion defaults)

## Responsive Design

**Mobile (< 640px):**
- Logo: 100x100px
- Heading: text-4xl
- Buttons: Full width, stacked vertically
- Feature grid: 1 column
- Padding: px-4, py-12

**Tablet (640px - 1024px):**
- Logo: larger
- Heading: text-5xl
- Buttons: Side-by-side
- Feature grid: 2 columns
- Padding: px-6, py-16

**Desktop (> 1024px):**
- Logo: 120x120px
- Heading: text-7xl
- Feature grid: 4 columns (or 8 if displayed as single row)
- Padding: py-20, max-width container

## Conditional Content

**Authenticated vs Unauthenticated:**

```typescript
const { isAuthenticated, isLoading } = useConvexAuth();

// In CTA button:
onClick={() => navigate(isAuthenticated ? "/empire" : "/auth")}

// Button text changes:
{isAuthenticated ? "Go to Empire" : "Get Started"}
```

## SEO Optimization

**Meta Keywords (Comprehensive List):**
- Brand: white cell protocol, discipline tracker, personal growth
- Feature-based: habit tracker, fitness tracker, productivity app
- Problem-solving: build unbreakable habits, improve discipline
- Long-tail: all-in-one personal development platform
- Niche: PMO tracker, workout logger, AI assistant
- Intent: goal tracking, self improvement, personal mastery

**Title Length:** ~160 characters (optimal for search results)

**Description:** ~155 characters (optimal display)

## Hooks & State Management

**React Hooks Used:**

```typescript
const navigate = useNavigate();
const { scrollY } = useScroll(); // Framer Motion
const y = useTransform(scrollY, [0, 300], [0, 100]);
const { isAuthenticated, isLoading } = useConvexAuth();
const totalUsers = useQuery((api as any).users.getTotalUsers);
```

## Performance Optimizations

- Logo animation only on page load (not continuous)
- Scroll effect optimized with Framer Motion transforms
- Query for totalUsers cached (Convex auto-caching)
- No unnecessary re-renders (dependency optimization)
- Icons from lucide-react (tree-shakable, only used icons bundled)

## Accessibility

- Color contrast: WCAG AA compliant
- Touch targets: 44px minimum on mobile
- Semantic HTML: section, heading hierarchy
- Icon + text pairs (no icon-only buttons)
- Focus states for keyboard navigation
- Alt text for logo image
- SVG optimization

## Future Enhancements

1. **Dynamic Content:**
   - Real-time stats (active users, daily tasks completed)
   - Rotating testimonials (auto-advance)
   - Featured success stories

2. **Interactivity:**
   - Feature demos (video embeds)
   - Interactive calculator: "How much can you improve?"
   - Quiz: "Which module fits you best?"

3. **Social Proof:**
   - Customer logos/brands
   - Press mentions
   - Case studies section

4. **Advanced SEO:**
   - Breadcrumb schema
   - Video schema
   - Job posting schema (if hiring)

5. **Performance:**
   - Image lazy loading
   - Code splitting by section
   - Critical CSS inlining

## Related Pages

- **Next Pages:** Auth (signup) or Dashboard/Empire (if authenticated)
- **External Links:** Privacy policy, terms of service (in footer)

## Technical Stack

- **Animations:** Framer Motion with parallax scroll
- **UI Components:** shadcn/ui (Button, Badge, Card)
- **Routing:** React Router (`useNavigate`)
- **Auth:** Convex Auth (`useConvexAuth`)
- **Real-time Data:** Convex queries
- **SEO:** Custom SEOHead + StructuredData components
- **Styling:** Tailwind CSS with responsive grid
