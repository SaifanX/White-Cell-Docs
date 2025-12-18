# Color Palette Quick Reference

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Overview

Complete color palette reference for White Cell Protocol. All colors are defined in OKLCH format (CSS native support), with RGB and Hex equivalents for legacy systems.

---

## Base Color System

### Primary Colors

Used for main interactions, CTAs, and key UI elements.

#### Brand Blue (Primary)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(55% 0.12 258)` |
| **RGB** | `rgb(0, 102, 204)` |
| **Hex** | `#0066cc` |
| **Usage** | Primary buttons, links, active states |

```css
:root {
  --color-primary: oklch(55% 0.12 258);
  --color-primary-rgb: 0, 102, 204;
}
```

#### Brand Purple (Secondary)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(50% 0.15 270)` |
| **RGB** | `rgb(102, 51, 204)` |
| **Hex** | `#6633cc` |
| **Usage** | Secondary actions, accents, badges |

#### Brand Green (Success)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(60% 0.14 150)` |
| **RGB** | `rgb(34, 197, 94)` |
| **Hex** | `#22c55e` |
| **Usage** | Success messages, achievements, completed states |

---

### Semantic Colors

#### Error/Danger (Red)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(60% 0.20 30)` |
| **RGB** | `rgb(239, 68, 68)` |
| **Hex** | `#ef4444` |
| **Usage** | Error messages, delete actions, warnings |

```css
.error { color: oklch(60% 0.20 30); }
.error-bg { background-color: oklch(95% 0.05 30); } /* Light variant */
```

#### Warning (Orange)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(65% 0.16 55)` |
| **RGB** | `rgb(251, 146, 60)` |
| **Hex** | `#fb9238` |
| **Usage** | Warning messages, caution states |

#### Info (Cyan)

| Format | Value |
|--------|-------|
| **OKLCH** | `oklch(60% 0.13 205)` |
| **RGB** | `rgb(6, 182, 212)` |
| **Hex** | `#06b4d4` |
| **Usage** | Informational messages, hints, tips |

---

## Neutral Colors (Grayscale)

### Light Mode Neutrals

| Name | OKLCH | RGB | Hex | Usage |
|------|-------|-----|-----|-------|
| **White** | `oklch(100% 0 0)` | `rgb(255, 255, 255)` | `#ffffff` | Backgrounds, cards, text |
| **Gray 50** | `oklch(97% 0.002 264)` | `rgb(250, 250, 250)` | `#fafafa` | Subtle backgrounds |
| **Gray 100** | `oklch(94% 0.003 264)` | `rgb(242, 242, 242)` | `#f2f2f2` | Secondary backgrounds |
| **Gray 200** | `oklch(89% 0.005 264)` | `rgb(229, 229, 229)` | `#e5e5e5` | Borders, dividers |
| **Gray 300** | `oklch(83% 0.008 264)` | `rgb(212, 212, 212)` | `#d4d4d4` | Input borders |
| **Gray 400** | `oklch(73% 0.01 264)` | `rgb(163, 163, 163)` | `#a3a3a3` | Placeholder text |
| **Gray 500** | `oklch(63% 0.012 264)` | `rgb(115, 115, 115)` | `#737373` | Secondary text |
| **Gray 600** | `oklch(53% 0.013 264)` | `rgb(82, 82, 82)` | `#525252` | Secondary headings |
| **Gray 700** | `oklch(43% 0.012 264)` | `rgb(64, 64, 64)` | `#404040` | Body text |
| **Gray 800** | `oklch(33% 0.01 264)` | `rgb(38, 38, 38)` | `#262626` | Headings |
| **Gray 900** | `oklch(23% 0.005 264)` | `rgb(23, 23, 23)` | `#171717` | High contrast text |

### Dark Mode Neutrals

| Name | OKLCH | RGB | Hex | Usage |
|------|-------|-----|-----|-------|
| **Dark 50** | `oklch(20% 0.005 264)` | `rgb(20, 20, 20)` | `#141414` | Primary dark BG |
| **Dark 100** | `oklch(25% 0.006 264)` | `rgb(30, 30, 30)` | `#1e1e1e` | Secondary dark BG |
| **Dark 200** | `oklch(30% 0.008 264)` | `rgb(40, 40, 40)` | `#282828` | Card/panel dark BG |
| **Dark 300** | `oklch(40% 0.01 264)` | `rgb(60, 60, 60)` | `#3c3c3c` | Dark borders |
| **Dark 600** | `oklch(70% 0.01 264)` | `rgb(180, 180, 180)` | `#b4b4b4` | Dark secondary text |
| **Dark 700** | `oklch(80% 0.008 264)` | `rgb(200, 200, 200)` | `#c8c8c8` | Dark body text |

---

## Contextual Colors

### Study Module

#### Study Blue (Primary Focus)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(50% 0.14 258)` | Notes, flashcards, study sessions |
| **RGB** | `rgb(29, 78, 216)` | |
| **Hex** | `#1d4ed8` | |

**Light variant:** `oklch(90% 0.04 258)` - Background tints
**Dark variant:** `oklch(70% 0.08 258)` - Hover states

### Fitness Module

#### Fitness Green (Energy)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(55% 0.16 150)` | Workouts, calories, achievements |
| **RGB** | `rgb(34, 197, 94)` | |
| **Hex** | `#22c55e` | |

**Light variant:** `oklch(90% 0.05 150)` - Background tints
**Accent:** `oklch(45% 0.18 120)` - Strength exercises

### Mind Module

#### Mind Purple (Wisdom)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(52% 0.15 270)` | Chess, wisdom, strategies |
| **RGB** | `rgb(102, 51, 204)` | |
| **Hex** | `#6633cc` | |

**Light variant:** `oklch(92% 0.04 270)` - Background tints
**Dark variant:** `oklch(65% 0.10 270)` - Interactive states

### Discipline Module

#### Discipline Orange (Willpower)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(65% 0.16 55)` | Streaks, habits, challenges |
| **RGB** | `rgb(251, 146, 60)` | |
| **Hex** | `#fb9238` | |

**Light variant:** `oklch(92% 0.05 55)` - Background tints
**Warm accent:** `oklch(70% 0.12 35)` - Intensity highlight

### Creator Module

#### Creator Pink (Creativity)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(60% 0.15 340)` | Content creation, ideas |
| **RGB** | `rgb(236, 72, 153)` | |
| **Hex** | `#ec4899` | |

**Light variant:** `oklch(92% 0.04 340)` - Background tints
**Dark variant:** `oklch(65% 0.10 340)` - Hover states

### Sentinel AI Module

#### AI Cyan (Intelligence)

| Format | Value | Usage |
|--------|-------|-------|
| **OKLCH** | `oklch(60% 0.15 200)` | AI features, intelligence |
| **RGB** | `rgb(34, 211, 238)` | |
| **Hex** | `#22d3ee` | |

**Light variant:** `oklch(92% 0.05 200)` - AI message bubbles
**Dark variant:** `oklch(65% 0.10 200)` - Focus states

---

## Theme Variants

### Theme 1: Light (Default)

```css
:root {
  /* Background */
  --bg-primary: oklch(100% 0 0);      /* White */
  --bg-secondary: oklch(97% 0.002 264); /* Gray 50 */
  --bg-tertiary: oklch(94% 0.003 264);  /* Gray 100 */
  
  /* Text */
  --text-primary: oklch(23% 0.005 264);   /* Gray 900 */
  --text-secondary: oklch(53% 0.013 264); /* Gray 600 */
  --text-tertiary: oklch(73% 0.01 264);   /* Gray 400 */
  
  /* Borders */
  --border-primary: oklch(89% 0.005 264);  /* Gray 200 */
  --border-secondary: oklch(83% 0.008 264); /* Gray 300 */
  
  /* Accents */
  --accent-primary: oklch(55% 0.12 258);    /* Brand Blue */
  --accent-secondary: oklch(50% 0.15 270);  /* Brand Purple */
  --accent-success: oklch(60% 0.14 150);    /* Brand Green */
  --accent-error: oklch(60% 0.20 30);       /* Brand Red */
  --accent-warning: oklch(65% 0.16 55);     /* Brand Orange */
}
```

### Theme 2: Dark

```css
[data-theme="dark"] {
  --bg-primary: oklch(20% 0.005 264);       /* Dark 50 */
  --bg-secondary: oklch(25% 0.006 264);     /* Dark 100 */
  --bg-tertiary: oklch(30% 0.008 264);      /* Dark 200 */
  
  --text-primary: oklch(90% 0.008 264);     /* Dark 700 */
  --text-secondary: oklch(75% 0.01 264);    /* Dark 600 */
  --text-tertiary: oklch(55% 0.008 264);    /* Gray 500 */
  
  --border-primary: oklch(35% 0.008 264);   /* Dark 300 */
  --border-secondary: oklch(40% 0.01 264);  /* Dark 400 */
  
  --accent-primary: oklch(65% 0.12 258);    /* Lighter Blue */
  --accent-secondary: oklch(65% 0.15 270);  /* Lighter Purple */
  --accent-success: oklch(70% 0.14 150);    /* Lighter Green */
  --accent-error: oklch(70% 0.20 30);       /* Lighter Red */
  --accent-warning: oklch(75% 0.16 55);     /* Lighter Orange */
}
```

### Theme 3: Study (Blue-Focused)

```css
[data-theme="study"] {
  --bg-primary: oklch(99% 0.01 258);        /* Cool white */
  --accent-primary: oklch(50% 0.14 258);    /* Study Blue */
  --accent-secondary: oklch(65% 0.08 258);  /* Light blue */
  --accent-success: oklch(55% 0.14 180);    /* Teal (wisdom) */
  --text-secondary: oklch(50% 0.008 258);   /* Cool gray */
}
```

### Theme 4: Fitness (Green-Focused)

```css
[data-theme="fitness"] {
  --bg-primary: oklch(99% 0.01 150);        /* Cool white */
  --accent-primary: oklch(55% 0.16 150);    /* Fitness Green */
  --accent-secondary: oklch(45% 0.18 120);  /* Darker green */
  --accent-success: oklch(50% 0.14 258);    /* Blue (energy) */
  --text-secondary: oklch(50% 0.008 150);   /* Green-tinted gray */
}
```

### Theme 5: Mind (Purple-Focused)

```css
[data-theme="mind"] {
  --bg-primary: oklch(99% 0.01 270);        /* Cool white */
  --accent-primary: oklch(52% 0.15 270);    /* Mind Purple */
  --accent-secondary: oklch(65% 0.10 270);  /* Lighter purple */
  --accent-success: oklch(60% 0.14 150);    /* Green (growth) */
  --text-secondary: oklch(50% 0.008 270);   /* Purple-tinted gray */
}
```

### Theme 6: Dark Mode + All Accents

```css
[data-theme="dark-vibrant"] {
  --bg-primary: oklch(15% 0.004 264);       /* Darker base */
  --accent-primary: oklch(70% 0.14 258);    /* Vibrant Blue */
  --accent-secondary: oklch(70% 0.16 270);  /* Vibrant Purple */
  --accent-success: oklch(75% 0.16 150);    /* Vibrant Green */
  --accent-error: oklch(75% 0.22 30);       /* Vibrant Red */
  --accent-warning: oklch(75% 0.18 55);     /* Vibrant Orange */
}
```

---

## Accessibility & Contrast

### WCAG AA Compliance (4.5:1 ratio for text)

**Safe Combinations (Dark text on light backgrounds):**
- Gray 900 on White ✓ (15.4:1)
- Gray 800 on Gray 100 ✓ (12.1:1)
- Gray 700 on White ✓ (8.6:1)
- Gray 600 on White ✓ (7.2:1)

**Safe Combinations (Light text on dark backgrounds):**
- White on Dark 50 ✓ (16.3:1)
- White on Dark 100 ✓ (14.2:1)
- Gray 200 on Dark 50 ✓ (15.8:1)

**Color Blind Safe Combinations:**
- Avoid Red+Green alone (use Blue accent)
- Always combine color with pattern/icon
- Use text labels alongside color coding
- Test with Color Oracle or similar tools

### Contrast Verification

```css
/* High contrast mode */
@media (prefers-contrast: more) {
  --text-primary: oklch(15% 0 0);     /* Nearly black */
  --text-secondary: oklch(40% 0.008 264); /* Darker gray */
  --accent-primary: oklch(45% 0.14 258);  /* Darker blue */
}

/* Reduced motion mode */
@media (prefers-reduced-motion: reduce) {
  animation: none;
  transition: none;
}
```

---

## Common Color Combinations

### Button States

```css
/* Primary Button */
--btn-primary-bg: oklch(55% 0.12 258);      /* Blue */
--btn-primary-text: oklch(100% 0 0);        /* White */
--btn-primary-hover: oklch(50% 0.12 258);   /* Darker blue */
--btn-primary-active: oklch(45% 0.12 258);  /* Even darker */
--btn-primary-disabled: oklch(73% 0.01 264); /* Gray */

/* Secondary Button */
--btn-secondary-bg: oklch(94% 0.003 264);   /* Gray 100 */
--btn-secondary-text: oklch(33% 0.01 264);  /* Gray 800 */
--btn-secondary-hover: oklch(89% 0.005 264); /* Gray 200 */
--btn-secondary-border: oklch(83% 0.008 264); /* Gray 300 */

/* Danger Button */
--btn-danger-bg: oklch(60% 0.20 30);        /* Red */
--btn-danger-text: oklch(100% 0 0);         /* White */
--btn-danger-hover: oklch(55% 0.20 30);     /* Darker red */
--btn-danger-disabled: oklch(73% 0.01 264); /* Gray */
```

### Card Styling

```css
/* Light card */
--card-bg: oklch(100% 0 0);           /* White */
--card-border: oklch(89% 0.005 264);  /* Gray 200 */
--card-shadow: rgba(0, 0, 0, 0.1);

/* Dark card */
[data-theme="dark"] --card-bg: oklch(30% 0.008 264);     /* Dark 200 */
[data-theme="dark"] --card-border: oklch(35% 0.008 264); /* Dark 300 */
[data-theme="dark"] --card-shadow: rgba(0, 0, 0, 0.3);
```

### Status Indicators

```css
--status-success: oklch(60% 0.14 150);      /* Green */
--status-warning: oklch(65% 0.16 55);       /* Orange */
--status-error: oklch(60% 0.20 30);         /* Red */
--status-info: oklch(60% 0.13 205);         /* Cyan */
--status-pending: oklch(65% 0.15 55);       /* Orange */
--status-neutral: oklch(63% 0.012 264);     /* Gray */
```

---

## Implementation in CSS

### CSS Variables

```css
/* color-palette.css */
:root {
  /* Primary Colors */
  --color-primary: oklch(55% 0.12 258);
  --color-primary-rgb: 0, 102, 204;
  --color-secondary: oklch(50% 0.15 270);
  --color-success: oklch(60% 0.14 150);
  --color-error: oklch(60% 0.20 30);
  --color-warning: oklch(65% 0.16 55);
  --color-info: oklch(60% 0.13 205);
  
  /* Neutrals */
  --color-white: oklch(100% 0 0);
  --color-gray-50: oklch(97% 0.002 264);
  --color-gray-100: oklch(94% 0.003 264);
  /* ... more grays ... */
  --color-gray-900: oklch(23% 0.005 264);
  
  /* Semantic */
  --bg-primary: var(--color-white);
  --bg-secondary: var(--color-gray-50);
  --text-primary: var(--color-gray-900);
  --text-secondary: var(--color-gray-600);
  --border-color: var(--color-gray-200);
}

/* Usage in components */
.button-primary {
  background-color: var(--color-primary);
  color: var(--color-white);
  border-radius: 0.5rem;
}

.card {
  background-color: var(--bg-primary);
  border: 1px solid var(--border-color);
}

.text-secondary {
  color: var(--text-secondary);
}
```

### Tailwind Integration

```javascript
// tailwind.config.ts
export default {
  theme: {
    colors: {
      primary: 'oklch(55% 0.12 258)',
      secondary: 'oklch(50% 0.15 270)',
      success: 'oklch(60% 0.14 150)',
      error: 'oklch(60% 0.20 30)',
      warning: 'oklch(65% 0.16 55)',
      info: 'oklch(60% 0.13 205)',
      
      // Usage: bg-primary, text-error, border-warning
    }
  }
}
```

### Accessing with JS

```typescript
// Get color variable
const primaryColor = getComputedStyle(document.documentElement)
  .getPropertyValue('--color-primary')
  .trim(); // Returns: "oklch(55% 0.12 258)"

// Set color for theme switching
document.documentElement.style.setProperty(
  '--color-primary',
  'oklch(65% 0.12 258)' // Lighter variant
);
```

---

## Color Picker Reference

### Figma/Design Tools

**Use these OKLCH values directly in design tools:**

```
Primary: oklch(55% 0.12 258)
Secondary: oklch(50% 0.15 270)
Success: oklch(60% 0.14 150)
Error: oklch(60% 0.20 30)
Warning: oklch(65% 0.16 55)
Info: oklch(60% 0.13 205)
```

**Or convert to Hex for tools that don't support OKLCH:**

```
Primary: #0066cc
Secondary: #6633cc
Success: #22c55e
Error: #ef4444
Warning: #fb9238
Info: #06b4d4
```

---

## Color Change Checklist

When updating theme colors:

- [ ] Update OKLCH values
- [ ] Update RGB equivalents
- [ ] Update Hex values
- [ ] Test WCAG AA contrast (4.5:1 for text)
- [ ] Test in light mode
- [ ] Test in dark mode
- [ ] Update CSS variables
- [ ] Update Tailwind config
- [ ] Test color blind mode
- [ ] Update design tool (Figma)
- [ ] Test on actual components
- [ ] Verify all 6 theme variants
- [ ] Update this documentation

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Design & Brand Team
