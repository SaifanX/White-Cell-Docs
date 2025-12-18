# Design System & UI/UX Guidelines

**Document:** White Cell Protocol Design System
**Version:** 1.0
**Last Updated:** December 2025

## Table of Contents

1. [Design Philosophy](#design-philosophy)
2. [Color System](#color-system)
3. [Typography](#typography)
4. [Spacing & Layout](#spacing--layout)
5. [Components](#components)
6. [Animations](#animations)
7. [Accessibility](#accessibility)
8. [Responsive Design](#responsive-design)
9. [Dark Mode & Themes](#dark-mode--themes)
10. [Visual Effects](#visual-effects)

---

## Design Philosophy

### Core Principles

**1. Clarity Through Simplicity**
- Clean, minimalist interfaces
- Distraction-free user experiences
- Information hierarchy guides user attention
- Visual elements serve purpose

**2. Performance & Speed**
- Lightweight animations (60fps)
- Instant visual feedback
- Optimized asset loading
- Smooth interactions

**3. Accessibility First**
- WCAG AA compliance (minimum)
- Color blind friendly palettes
- Keyboard navigation essential
- Screen reader support

**4. Gamification Integration**
- Visual rewards for actions
- Progress indicators prominent
- Achievement celebration
- XP gain feedback

**5. Mobile-First Approach**
- Designed for small screens first
- Touch targets 44px minimum
- Vertical navigation primary
- Progressive enhancement

**6. Warrior Aesthetic**
- Military precision in layout
- Power and confidence in visuals
- Motivational tone throughout
- Achievement celebration (aura effects)

---

## Color System

### Color Variables (OKLCH Format)

**Why OKLCH?**
- Perceptually uniform
- Better accessibility
- Smoother transitions
- Hardware accelerated

### Light Mode (Default)

```css
--background: oklch(100% 0 0)           /* Pure white */
--foreground: oklch(9% 0 0)             /* Near black */
--card: oklch(100% 0 0)                 /* White */
--card-foreground: oklch(9% 0 0)        /* Black text */
--primary: oklch(25% 0.02 0)            /* Dark gray/primary */
--primary-foreground: oklch(98.78% 0 0) /* Light text on primary */
--secondary: oklch(96.15% 0 0)          /* Light gray */
--secondary-foreground: oklch(13.84% 0 0) /* Dark text on secondary */
--muted: oklch(96.15% 0 0)              /* Light muted */
--muted-foreground: oklch(45.6% 0.004 285.82) /* Gray text */
--accent: oklch(96.15% 0 0)             /* Light accent */
--accent-foreground: oklch(13.84% 0 0)  /* Dark text */
--destructive: oklch(57.68% 0.243 29.23) /* Red (error) */
--destructive-foreground: oklch(98.78% 0 0) /* Light text */
--border: oklch(91.42% 0 0)             /* Light gray borders */
--input: oklch(91.42% 0 0)              /* Input backgrounds */
--ring: oklch(25% 0.02 0)               /* Focus ring color */
```

### Dark Mode

```css
--background: oklch(13.84% 0 0)         /* Dark gray */
--foreground: oklch(98.78% 0 0)         /* Near white */
--card: oklch(13.84% 0 0)               /* Dark gray */
--card-foreground: oklch(98.78% 0 0)    /* White text */
--primary: oklch(85% 0.02 0)            /* Light gray (accent) */
--primary-foreground: oklch(13.84% 0 0) /* Dark text */
--secondary: oklch(21.69% 0 0)          /* Slightly lighter dark */
--secondary-foreground: oklch(98.78% 0 0) /* White text */
--muted: oklch(21.69% 0 0)              /* Muted dark */
--muted-foreground: oklch(64.9% 0.004 285.82) /* Light gray text */
--accent: oklch(21.69% 0 0)             /* Accent dark */
--accent-foreground: oklch(98.78% 0 0)  /* White text */
--destructive: oklch(57.68% 0.243 29.23) /* Red (same as light) */
--destructive-foreground: oklch(98.78% 0 0) /* White text */
--border: oklch(21.69% 0 0)             /* Dark borders */
--input: oklch(21.69% 0 0)              /* Input backgrounds */
--ring: oklch(85% 0.02 0)               /* Focus ring (light) */
```

### Color Semantics

| Color | Semantic | Usage | Contrast |
|-------|----------|-------|----------|
| Primary | Main action | Buttons, links, focus states | High (WCAG AA+) |
| Secondary | Supporting | Secondary buttons, background highlights | High |
| Accent | Emphasis | Important info, highlights | High |
| Destructive | Danger | Delete, errors, warnings | High |
| Muted | De-emphasized | Disabled, secondary text | Medium |
| Border | Structure | Cards, inputs, dividers | Medium |

### Module-Specific Colors

**Primary Colors for Each Module:**

| Module | Color | Hex (Light) | Hex (Dark) | Icon |
|--------|-------|------------|-----------|------|
| Discipline | Red | #EF4444 | #FCA5A5 | 🎯 Target |
| Fitness | Green | #22C55E | #86EFAC | 💪 Dumbbell |
| Study | Blue | #3B82F6 | #93C5FD | 📖 BookOpen |
| Mind | Purple | #A855F7 | #D8B4FE | 🧠 Brain |
| Creator | Pink | #EC4899 | #F472B6 | 🎬 Video |
| Sentinel AI | Cyan | #06B6D4 | #67E8F9 | 🤖 Bot |
| Empire | Yellow | #EAB308 | #FACC15 | 🏆 Trophy |

**Usage:**
- Module navigation highlights
- Progress indicators
- Category badges
- Achievement icons

### Extended Color Palette

**Status Colors:**
```css
--success: #10B981 (green)      /* Completed, success */
--warning: #F59E0B (amber)      /* Warning, caution */
--info: #3B82F6 (blue)          /* Information, help */
--error: #EF4444 (red)          /* Error, failure */
```

**Transparency Levels:**
- Alpha 100%: Primary uses
- Alpha 90%: Hover states
- Alpha 75%: Disabled states
- Alpha 50%: Subtle backgrounds
- Alpha 30%: Very subtle overlays
- Alpha 10%: Extremely subtle

---

## Typography

### Font Stack

```css
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
```

**Fallback Order:**
1. System fonts (optimal performance)
2. Segoe UI / Roboto (Windows/Android)
3. Helvetica Neue (Mac fallback)
4. Generic sans-serif

### Font Sizes

**Heading Scale:**

| Level | Size | Desktop | Mobile | Use Case |
|-------|------|---------|--------|----------|
| h1 | 2.5rem (40px) | Yes | Mobile only | Page title |
| h2 | 2rem (32px) | Yes | Yes | Section title |
| h3 | 1.5rem (24px) | Yes | Yes | Subsection |
| h4 | 1.25rem (20px) | Yes | Yes | Card title |
| p | 1rem (16px) | Yes | Yes | Body text |
| small | 0.875rem (14px) | Yes | Yes | Secondary text |
| xs | 0.75rem (12px) | Yes | Yes | Labels, badges |

### Font Weights

| Weight | Value | Usage |
|--------|-------|-------|
| Regular | 400 | Body text, descriptions |
| Medium | 500 | Button text, labels |
| Semi-bold | 600 | Card titles, emphasis |
| Bold | 700 | Headings, important text |

### Line Heights

| Type | Line Height | Usage |
|------|------------|-------|
| Headings | 1.2 | Tight line height |
| Body | 1.5 | Standard readability |
| Dense | 1.4 | Compact text |
| Loose | 1.6 | Accessibility improvement |

### Letter Spacing

```css
h1, h2, h3, h4, h5, h6 {
  letter-spacing: -0.02em;  /* Tighter headings */
}

body {
  letter-spacing: 0;        /* Normal body */
}
```

### Number Formatting

**Tabular Numbers (for alignment):**
```html
<span class="tabular-nums">
  Score: 1,234,567
  Level: 42
</span>
```

**Use for:**
- Leaderboards
- Statistics displays
- XP counters
- Time displays

---

## Spacing & Layout

### Spacing Scale

**Base Unit: 4px**

| Token | Value | Usage |
|-------|-------|-------|
| px-0 | 0 | Remove spacing |
| px-1 | 4px | Minimal spacing |
| px-2 | 8px | Tight spacing |
| px-3 | 12px | Small spacing |
| px-4 | 16px | Standard spacing |
| px-6 | 24px | Card padding |
| px-8 | 32px | Section spacing |
| px-12 | 48px | Large sections |

**Margin/Padding Pairs:**
```css
/* Common combinations */
px-4 py-3   /* Standard button padding */
px-6 py-4   /* Card padding */
px-8 py-6   /* Section padding */
px-12 py-8  /* Large sections */
```

### Grid System

**Tailwind Grid:**
```css
/* Container */
container: max-width: 1280px, padding: 16px

/* Common grid layouts */
grid-cols-1         /* Mobile */
md:grid-cols-2      /* Tablet */
lg:grid-cols-3      /* Desktop */
lg:grid-cols-4      /* Wide screens */
```

**Gap Spacing:**
```css
gap-3  /* 12px between grid items */
gap-4  /* 16px between grid items */
gap-6  /* 24px between grid items */
gap-8  /* 32px between grid items */
```

### Padding & Margins

**Standard Spacing Rules:**

**Buttons:**
- Padding: px-4 py-2 (16px horizontal, 8px vertical)
- Minimum height: 40px (desktop), 44px (mobile)
- Minimum width: 40px (desktop), 44px (mobile)

**Cards:**
- Padding: px-6 py-4 (24px horizontal, 16px vertical)
- Border radius: 8px
- Box shadow: 0 1px 3px rgba(0,0,0,0.12)

**Sections:**
- Top/bottom: 32px-48px
- Left/right: 16px (mobile), 24px (desktop)
- Max width: 1280px (container)

**Inputs:**
- Height: 40px (desktop), 44px (mobile)
- Padding: px-3 py-2
- Border radius: 6px

---

## Components

### Button Component

**Variants:**

**Primary Button**
- Background: Primary color
- Text: Primary foreground
- Border: None
- Hover: Darken 10%, scale 1.02
- Active: Scale 0.98
- Disabled: Opacity 50%, cursor: not-allowed

**Secondary Button**
- Background: Secondary color
- Text: Secondary foreground
- Border: 1px secondary
- Hover: Darken background
- Active: Scale 0.98

**Outline Button**
- Background: Transparent
- Text: Primary
- Border: 1px primary
- Hover: Background opacity 10%
- Active: Background opacity 20%

**Ghost Button**
- Background: Transparent
- Text: Primary
- Border: None
- Hover: Background opacity 5%
- Active: Background opacity 10%

**Destructive Button**
- Background: Destructive (red)
- Text: White
- Hover: Darken 10%
- Confirmation required for critical actions

**Sizes:**

```
Small   | height: 32px | px-3 py-1 | text-sm
Medium  | height: 40px | px-4 py-2 | text-base (default)
Large   | height: 48px | px-6 py-3 | text-base
```

### Card Component

**Structure:**
```
┌────────────────────────┐
│ Card Header (optional) │  ← CardHeader
├────────────────────────┤
│ Card Content           │  ← CardContent
│ (main body)            │
├────────────────────────┤
│ Card Footer (optional) │  ← CardFooter
└────────────────────────┘
```

**Styling:**
- Background: Card background color
- Border: 1px border color
- Border radius: 8px
- Padding: 24px
- Box shadow: 0 1px 3px rgba(0,0,0,0.12)
- Hover: Box shadow 0 4px 12px rgba(0,0,0,0.15)

### Badge Component

**Variants:**

| Variant | Background | Text | Usage |
|---------|-----------|------|-------|
| default | Primary | Primary foreground | Status, tags |
| secondary | Secondary | Secondary foreground | Categories |
| outline | Transparent | Primary | Alternative selection |
| destructive | Destructive | Destructive foreground | Error, warning |

**Sizes:**
- Small: px-2 py-1, text-xs
- Medium: px-3 py-1, text-sm (default)
- Large: px-4 py-2, text-base

### Input Component

**States:**

| State | Border | Background | Text |
|-------|--------|-----------|------|
| Default | Border color | Input background | Foreground |
| Focus | Ring color | Input background | Foreground |
| Disabled | Border opacity 50% | Muted | Muted foreground |
| Error | Destructive | Input background | Destructive |
| Success | Success green | Input background | Foreground |

**Styling:**
- Height: 40px
- Padding: 12px 12px (px-3 py-2)
- Border radius: 6px
- Border: 1px solid
- Font size: 16px (prevent zoom on iOS)

### Select/Dropdown

**Styling:**
- Similar to input
- Chevron icon on right (pointer-events: none)
- Custom styling with appearance: none
- Keyboard accessible (arrow keys)

---

## Animations

### Custom Animations

**Shimmer (Loading)**
```css
@keyframes shimmer {
  100% { transform: translateX(100%); }
}
animation: shimmer 2s infinite;
```

**Float (Emphasis)**
```css
@keyframes float {
  0%, 100% { transform: translateY(0px); }
  50% { transform: translateY(-10px); }
}
animation: float 3s ease-in-out infinite;
```

**Pulse Glow (Attention)**
```css
@keyframes pulse-glow {
  0%, 100% { box-shadow: 0 0 20px rgba(var(--primary), 0.5); }
  50% { box-shadow: 0 0 40px rgba(var(--primary), 0.8); }
}
animation: pulse-glow 2s ease-in-out infinite;
```

**Slide Up (Page Load)**
```css
@keyframes slide-up {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}
animation: slide-up 0.5s ease-out;
```

**Scale In (Appearance)**
```css
@keyframes scale-in {
  from { opacity: 0; transform: scale(0.9); }
  to { opacity: 1; transform: scale(1); }
}
animation: scale-in 0.3s ease-out;
```

### Framer Motion Animations

**Page Transitions:**
```typescript
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.6 }}
>
```

**Staggered Lists:**
```typescript
<motion.div initial="hidden" animate="show" variants={containerVariants}>
  {items.map((item, i) => (
    <motion.div key={i} variants={itemVariants}>
      {item}
    </motion.div>
  ))}
</motion.div>
```

**Hover Effects:**
```typescript
<motion.button whileHover={{ scale: 1.02 }} whileTap={{ scale: 0.98 }} />
```

### Animation Timing

| Duration | Usage | Feel |
|----------|-------|------|
| 150ms | Micro-interactions | Snappy |
| 300ms | Component transitions | Responsive |
| 500ms | Page transitions | Smooth |
| 600ms | Modal/Dialog open | Deliberate |
| 3s | Looping animations | Continuous |

**Easing Functions:**
```css
ease-out: cubic-bezier(0.4, 0, 0.2, 1)     /* Enter animations */
ease-in: cubic-bezier(0.4, 0, 1, 1)        /* Exit animations */
ease-in-out: cubic-bezier(0.4, 0, 0.2, 1)  /* Transitions */
linear: linear                              /* Continuous motion */
```

### Performance Optimization

**GPU Acceleration:**
- Use `transform` and `opacity` only
- Avoid `top`, `left`, `width`, `height` animations
- Use `will-change: transform` sparingly
- Hardware acceleration enabled: yes

**Reduced Motion:**
```css
@media (prefers-reduced-motion: reduce) {
  * { animation: none !important; transition: none !important; }
}
```

---

## Accessibility

### WCAG 2.1 Compliance

**Target Level: AA (minimum)**
- Contrast ratio: 4.5:1 for normal text
- Contrast ratio: 3:1 for large text (18pt+)
- Contrast ratio: 3:1 for UI components

### Keyboard Navigation

**Focus Management:**
- Focus visible on all interactive elements
- Focus ring color: `--ring` variable
- Focus outline: 2px solid
- Focus order: Logical (left-to-right, top-to-bottom)

**Keyboard Shortcuts:**
```
Tab         | Navigate forward
Shift+Tab   | Navigate backward
Enter       | Activate button/link
Space       | Toggle checkbox/radio
Arrow Keys  | Navigate menus/lists
Escape      | Close dialog/popover
```

### Screen Reader Support

**Semantic HTML:**
- Use `<button>`, not `<div role="button">`
- Use `<label>` for inputs
- Use `<form>` for forms
- Use `<nav>` for navigation
- Use `<main>` for main content

**ARIA Attributes:**
```html
aria-label="Close"              <!-- Label for icon buttons -->
aria-description="..."          <!-- Longer descriptions -->
aria-hidden="true"              <!-- Hide decorative elements -->
aria-live="polite"              <!-- Announce dynamic content -->
aria-expanded="false"           <!-- Button state for toggles -->
aria-current="page"             <!-- Current navigation item -->
role="progressbar"              <!-- Progress indicators -->
```

### Color Blind Friendly

**Design Considerations:**
- Never use color alone to convey information
- Use patterns and icons in addition to color
- Test with color blind simulators
- Deuteranopia (red-green): Primary palette works
- Protanopia (red-green): Use blue/yellow when possible
- Tritanopia (blue-yellow): Rare, but test

### Minimum Touch Targets

**Mobile (< 1024px):**
- All clickable elements: 44px × 44px minimum
- Buttons: `h-11` (44px) minimum
- Links: Adequate padding
- Spacing: At least 8px between targets

**Desktop (> 1024px):**
- Minimum: 40px × 40px
- Comfortable: 44px × 44px or larger

---

## Responsive Design

### Breakpoints

```css
sm  /* 640px  */  Mobile landscape
md  /* 768px  */  iPad portrait
lg  /* 1024px */  iPad landscape / Desktop
xl  /* 1280px */  Large desktop
2xl /* 1536px */  Wide desktop
```

### Mobile-First Approach

**Pattern:**
```css
/* Mobile (default) */
width: 100%;
columns: 1;
text-size: 16px;

/* Tablet */
@media (min-width: 768px) {
  columns: 2;
  text-size: 18px;
}

/* Desktop */
@media (min-width: 1024px) {
  columns: 3;
  text-size: 20px;
}
```

### Responsive Typography

| Screen | h1 | h2 | p |
|--------|----|----|---|
| Mobile | 28px | 24px | 16px |
| Tablet | 32px | 28px | 16px |
| Desktop | 40px | 32px | 16px |

### Responsive Layout Patterns

**Single Column (Mobile)**
- Full width
- Vertical stacking
- 16px padding on sides
- Touch-friendly spacing

**Two Column (Tablet)**
- 50% width each
- Side-by-side layout
- 24px padding
- Larger touch targets

**Three+ Column (Desktop)**
- Grid layout
- Multiple columns
- 32px padding
- Optimized reading width

### Safe Areas & Notches

```css
.safe-area-bottom {
  padding-bottom: env(safe-area-inset-bottom);
}

.safe-area-horizontal {
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}
```

---

## Dark Mode & Themes

### Theme System

**Available Themes:**

**1. Default (Light + Dark)**
- Clean, professional
- High contrast
- Standard WCAG AA compliance

**2. OLED Black**
```css
.dark.oled {
  --background: oklch(0% 0 0);      /* True black */
  --card: oklch(0% 0 0);
  /* Reduced power consumption on OLED screens */
}
```

**3. Midnight Blue**
```css
.dark.midnight {
  --background: oklch(15% 0.05 250); /* Deep blue */
  --primary: oklch(65% 0.15 240);    /* Bright blue accent */
  /* Calming blue tones */
}
```

**4. Cyberpunk**
```css
.dark.cyberpunk {
  --primary: oklch(70% 0.25 330);    /* Magenta */
  --accent: oklch(70% 0.25 180);     /* Cyan */
  --background: oklch(12% 0.02 330); /* Dark magenta */
  /* Futuristic, high-contrast aesthetic */
}
```

**5. Military**
```css
.dark.military {
  --primary: oklch(60% 0.18 140);    /* Tactical green */
  --accent: oklch(55% 0.12 100);     /* Gold accent */
  --background: oklch(12% 0.02 140); /* Dark olive */
  /* Military precision aesthetic */
}
```

**6. Minimalist**
```css
.dark.minimalist {
  --primary: oklch(65% 0.18 264);    /* Purple */
  --background: oklch(16% 0 0);      /* Neutral gray */
  --border: oklch(18% 0 0);          /* Minimal visible borders */
  /* Distraction-free, focused interface */
}
```

### Theme Selection

**Settings:**
```html
<!-- Settings → Theme & Display Tab -->
<select>
  <option value="light">☀️ Light</option>
  <option value="dark">🌙 Dark</option>
  <option value="auto">🔄 System (Auto)</option>
</select>
```

**System Preference Detection:**
```typescript
const prefersDark = window.matchMedia("(prefers-color-scheme: dark)").matches;
```

### Custom Accent Colors

**In Settings:**
- 8 predefined colors (Blue, Purple, Green, Orange, Red, Pink, Cyan, Yellow)
- Custom hex input
- Live preview
- Applied to buttons, links, focus states

---

## Visual Effects

### Glassmorphism

**Subtle Glass Effect:**
```css
.glass {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.dark .glass {
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid rgba(255, 255, 255, 0.1);
}
```

**Strong Glass Effect:**
```css
.glass-strong {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}
```

**Uses:**
- Floating headers
- Modals/overlays
- Interactive cards
- Navigation bars

### Interactive Card Effects

```css
.interactive-card {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.interactive-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px -10px rgba(0, 0, 0, 0.2);
}
```

**Elevation System:**

| Level | Box Shadow | Use |
|-------|-----------|-----|
| Flat | None | Background |
| 1 | 0 1px 3px rgba(0,0,0,0.12) | Subtle cards |
| 2 | 0 4px 12px rgba(0,0,0,0.15) | Elevated cards |
| 3 | 0 12px 24px rgba(0,0,0,0.2) | Modals, floating |
| 4 | 0 20px 40px rgba(0,0,0,0.25) | Tooltips, menus |

### Scrollbar Styling

```css
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: var(--muted);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb {
  background: var(--primary);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: var(--ring);
}
```

### Warrior Aura (Achievement Effect)

**Purpose:** Highlight top leaderboard users

**Animation:**
```css
@keyframes warrior-aura {
  0%, 100% {
    box-shadow: 0 0 10px rgba(234, 179, 8, 0.3);
    filter: brightness(1);
  }
  50% {
    box-shadow: 0 0 20px rgba(234, 179, 8, 0.6);
    filter: brightness(1.15);
  }
}

.warrior-aura {
  animation: warrior-aura 3s ease-in-out infinite;
}
```

**Shimmer Effect:**
```css
@keyframes warrior-shimmer {
  0% { background-position: -200% center; }
  100% { background-position: 200% center; }
}

.warrior-shimmer {
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(234, 179, 8, 0.4) 50%,
    transparent 100%
  );
  background-size: 200% 100%;
  animation: warrior-shimmer 3s linear infinite;
}
```

---

## Implementation Guidelines

### CSS Architecture

```
index.css (Main stylesheet)
├── @import tailwindcss
├── @theme (OKLCH color definitions)
├── :root (Light mode)
├── .dark (Dark mode)
├── .dark.oled (OLED variant)
├── .dark.midnight (Blue variant)
├── @layer base (Reset & typography)
├── Utility classes (glass, interactive-card, etc.)
└── Custom animations (@keyframes)
```

### Component Naming

**Class Naming Convention:**
```css
.component-name          /* Root element */
.component-name-header   /* Sub-element */
.component-name--variant /* Variant modifier */
.component-name.is-active /* State modifier */
```

### Best Practices

**Colors:**
- ✅ Use CSS variables for all colors
- ❌ Don't hardcode hex values
- ✅ Test in both light and dark modes
- ✅ Check color contrast ratios

**Spacing:**
- ✅ Use Tailwind spacing scale
- ❌ Don't mix spacing systems
- ✅ Keep consistent padding/margin pairs
- ✅ Use semantic spacing tokens

**Animations:**
- ✅ Keep animations under 600ms
- ✅ Use `transform` and `opacity` only
- ✅ Respect `prefers-reduced-motion`
- ❌ Don't add animations without purpose

**Accessibility:**
- ✅ Ensure 4.5:1 contrast for normal text
- ✅ Use semantic HTML
- ✅ Keyboard test every page
- ✅ Test with screen readers

---

## Design Checklist

### Before Launch

- [ ] All colors tested in light and dark mode
- [ ] All components responsive (mobile → desktop)
- [ ] All interactive elements keyboard accessible
- [ ] Focus indicators visible on all elements
- [ ] Color contrast ratios meet WCAG AA
- [ ] Animations respect `prefers-reduced-motion`
- [ ] Touch targets minimum 44px on mobile
- [ ] Page load animations under 600ms
- [ ] Scrollbar styled consistently
- [ ] Custom themes tested
- [ ] Print styles considered
- [ ] Performance metrics acceptable

### Ongoing Maintenance

- Keep design tokens documented
- Test new components for accessibility
- Monitor animation performance
- Review user feedback on UI
- Update themes based on user preferences
- Maintain consistency across modules

---

## Resources

**Tools:**
- [Tailwind CSS](https://tailwindcss.com) - Utility-first CSS
- [OKLCH Color Picker](https://oklch.com) - Color tool
- [shadcn/ui](https://ui.shadcn.com) - Component library
- [Framer Motion](https://www.framer.com/motion) - Animation library
- [Lucide Icons](https://lucide.dev) - Icon library

**Color Accessibility:**
- [Contrast Checker](https://webaim.org/resources/contrastchecker) - WCAG compliance
- [Color Blind Simulator](https://www.color-blindness.com/coblis-color-blindness-simulator) - Simulations

**Typography:**
- [Font Pairing](https://www.fontpair.co) - System font combinations
- [Baseline Shift](https://www.baselineshiftmag.com) - Typography guide

**Animation:**
- [Easing Functions](https://easings.net) - Common easing curves
- [Keyframes](https://keyframes.app) - Animation tool

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Dec 2025 | Initial design system documentation |

---

**Last Updated:** December 18, 2025
**Maintained By:** Design & Product Team
**Next Review:** June 2026
