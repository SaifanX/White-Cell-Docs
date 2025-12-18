# Typography Reference

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Font System

### Primary Font Stack

**Segoe UI / System Fonts (Recommended)**

```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
             'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
             sans-serif;
```

**Why this stack:**
- System-optimized on all platforms
- No external font loading (faster)
- Native rendering on each OS
- Excellent readability
- Fallback coverage for all browsers

### Code Font Stack

**Monospace for code blocks**

```css
font-family: 'SFMono-Regular', 'Consolas', 'Liberation Mono', 'Menlo', monospace;
```

---

## Font Sizes & Scale

### Modular Scale (1.125 ratio)

All sizes follow a consistent 1.125:1 ratio for visual harmony.

| Level | px | rem | Usage |
|-------|----|----|-------|
| **xs** | 11 | 0.6875 | Captions, tags, timestamps |
| **sm** | 13 | 0.8125 | Small labels, helper text |
| **base** | 16 | 1 | Body text, paragraphs |
| **lg** | 18 | 1.125 | List items, card titles |
| **xl** | 20 | 1.25 | Section headings |
| **2xl** | 22.5 | 1.40625 | Subheadings |
| **3xl** | 25.3 | 1.58125 | Page headings |
| **4xl** | 28.4 | 1.775 | Hero titles |
| **5xl** | 32 | 2 | Large hero text |
| **6xl** | 36 | 2.25 | Display/banner text |

### Usage Guidelines

```html
<!-- Page Title / Hero -->
<h1 class="text-5xl font-bold">Learn Anything</h1> <!-- 32px -->

<!-- Section Heading -->
<h2 class="text-3xl font-bold">Study Section</h2> <!-- 25.3px -->

<!-- Subsection Heading -->
<h3 class="text-2xl font-semibold">Flashcards</h3> <!-- 22.5px -->

<!-- Card Title -->
<h4 class="text-xl font-semibold">Quick Stats</h4> <!-- 20px -->

<!-- List Item / Card Subtitle -->
<p class="text-lg font-medium">Completed 5 workouts</p> <!-- 18px -->

<!-- Body Text / Paragraph (Default) -->
<p class="text-base font-normal">Lorem ipsum dolor sit amet...</p> <!-- 16px -->

<!-- Helper Text / Labels -->
<label class="text-sm font-medium">Email Address</label> <!-- 13px -->

<!-- Captions / Meta -->
<time class="text-xs text-gray-500">2 hours ago</time> <!-- 11px -->
```

---

## Font Weights

### Available Weights

| Weight | Value | Usage |
|--------|-------|-------|
| **Light** | 300 | Subtle, de-emphasized text |
| **Regular** | 400 | Body text, default |
| **Medium** | 500 | Labels, emphasis |
| **Semibold** | 600 | Subheadings, important text |
| **Bold** | 700 | Headings, strong emphasis |

### Implementation

```css
/* CSS Font Weights */
.font-light {
  font-weight: 300;
}

.font-normal {
  font-weight: 400;
}

.font-medium {
  font-weight: 500;
}

.font-semibold {
  font-weight: 600;
}

.font-bold {
  font-weight: 700;
}
```

### Usage Patterns

```html
<!-- Heading with bold weight -->
<h1 class="text-5xl font-bold">White Cell Protocol</h1>

<!-- Subheading with semibold -->
<h2 class="text-2xl font-semibold">Your Dashboard</h2>

<!-- Body text with regular weight -->
<p class="text-base font-normal">Welcome back to your learning platform.</p>

<!-- Emphasis with medium weight -->
<span class="font-medium">250 XP</span> earned today

<!-- Subtle text with light weight -->
<p class="text-sm font-light">Optional supplementary information</p>
```

---

## Line Height

### Vertical Rhythm

Line heights maintain consistent vertical spacing.

| Context | Value | Ratio | Usage |
|---------|-------|-------|-------|
| **Heading** | 1.2 | 1.2x font size | H1-H3 titles |
| **Subheading** | 1.35 | 1.35x font size | H4-H5 subtitles |
| **Body** | 1.6 | 1.6x font size | Paragraphs, default |
| **Loose** | 1.8 | 1.8x font size | Long-form content |
| **Tight** | 1.4 | 1.4x font size | Labels, compact text |

### Examples

```css
/* Heading - tighter line height */
h1, h2, h3 {
  line-height: 1.2;
}

/* Body text - readable spacing */
p, .text-base {
  line-height: 1.6;
}

/* Form labels - tight */
label {
  line-height: 1.4;
}

/* Long articles - loose */
article p {
  line-height: 1.8;
}
```

### Visual Examples

```html
<!-- Heading with 1.2 line-height -->
<h2 style="line-height: 1.2;">
  This is a heading with<br/>
  multiple lines of text
</h2>

<!-- Body with 1.6 line-height -->
<p style="line-height: 1.6;">
  Lorem ipsum dolor sit amet, consectetur adipiscing elit.
  Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
</p>
```

---

## Letter Spacing

### Tracking Values

Used for specific text contexts to improve readability or aesthetics.

| Context | Value | Usage |
|---------|-------|-------|
| **Tight** | -0.02em | All caps headings, badges |
| **Normal** | 0em | Default, most text |
| **Wide** | 0.05em | Spaced out labels |
| **Wider** | 0.1em | Aesthetic headers, names |

### Examples

```css
/* Tight tracking for all-caps */
.badge {
  letter-spacing: -0.02em;
  text-transform: uppercase;
  font-weight: 600;
}

/* Normal spacing (default) */
p {
  letter-spacing: 0;
}

/* Wider spacing for premium feel */
h1 {
  letter-spacing: 0.05em;
  font-weight: bold;
}
```

---

## Heading Hierarchy

### Complete System (H1-H6)

```html
<!-- H1: Page Title (32px, Bold, 1.2 line-height) -->
<h1 class="text-5xl font-bold leading-tight">Learn Anything</h1>

<!-- H2: Section Title (25.3px, Bold, 1.2 line-height) -->
<h2 class="text-3xl font-bold leading-tight">Study Zone</h2>

<!-- H3: Subsection (22.5px, Semibold, 1.35 line-height) -->
<h3 class="text-2xl font-semibold leading-snug">Flashcards</h3>

<!-- H4: Component Title (20px, Semibold, 1.35 line-height) -->
<h4 class="text-xl font-semibold leading-snug">Recent Activity</h4>

<!-- H5: Card Title (18px, Semibold, 1.4 line-height) -->
<h5 class="text-lg font-semibold leading-relaxed">Quick Stats</h5>

<!-- H6: Label/Minor Heading (16px, Medium, 1.4 line-height) -->
<h6 class="text-base font-medium leading-relaxed">Category Label</h6>
```

### Spacing Between Headings & Content

```css
/* Margin below headings */
h1 {
  margin-bottom: 1.5rem;  /* 24px */
}

h2 {
  margin-bottom: 1.25rem; /* 20px */
  margin-top: 2rem;       /* Add space before */
}

h3, h4, h5, h6 {
  margin-bottom: 1rem;    /* 16px */
  margin-top: 1.5rem;     /* Add space before */
}

p {
  margin-bottom: 1rem;    /* 16px between paragraphs */
}
```

---

## Body Text

### Paragraph Styles

```html
<!-- Standard paragraph -->
<p class="text-base font-normal leading-relaxed text-gray-700">
  Standard paragraph text with default styling for general content.
</p>

<!-- Emphasized paragraph -->
<p class="text-base font-normal leading-relaxed text-gray-700">
  This paragraph contains <strong class="font-semibold">emphasized text</strong>
  and <em>italic text</em> for variety.
</p>

<!-- Large paragraph (callout) -->
<p class="text-lg font-normal leading-relaxed text-gray-800">
  This is a larger paragraph, often used for important callouts or introductions.
</p>

<!-- Small paragraph (fine print) -->
<p class="text-sm font-normal leading-relaxed text-gray-600">
  This is smaller text, suitable for disclaimers, supplementary info, or fine print.
</p>
```

### Text Emphasis

```html
<!-- Bold for strong emphasis -->
<strong class="font-bold">Important information</strong>

<!-- Semibold for medium emphasis -->
<span class="font-semibold">Moderate emphasis</span>

<!-- Italic for citations/variations -->
<em>Italicized text for emphasis</em>

<!-- Underline for links or marked text -->
<a href="#" class="underline text-blue-600">Link text</a>

<!-- Strikethrough for deprecated content -->
<s class="line-through text-gray-500">Old value</s>
```

---

## Form Typography

### Labels

```html
<!-- Label (Medium weight, slightly smaller) -->
<label for="email" class="block text-sm font-medium text-gray-700 mb-2">
  Email Address
</label>

<!-- Label with required indicator -->
<label for="password" class="block text-sm font-medium text-gray-700 mb-2">
  Password
  <span class="text-red-500">*</span>
</label>

<!-- Label with helper text -->
<div>
  <label for="username" class="block text-sm font-medium text-gray-700 mb-1">
    Username
  </label>
  <p class="text-xs text-gray-500 mb-2">Use letters, numbers, and underscores only</p>
  <input id="username" class="..." />
</div>
```

### Input Text

```css
/* Input styling */
input[type="text"],
input[type="email"],
textarea {
  font-size: 1rem;          /* 16px - prevents zoom on mobile */
  font-family: inherit;
  line-height: 1.6;
  letter-spacing: 0;
}

/* Placeholder text */
input::placeholder {
  font-size: 1rem;
  color: #a0aec0;           /* Gray 400 */
  font-weight: 400;
}
```

### Error & Helper Text

```html
<!-- Helper text below input -->
<input class="..." />
<p class="text-xs text-gray-500 mt-1">
  Password must be at least 8 characters
</p>

<!-- Error message -->
<input class="border-red-500" />
<p class="text-xs text-red-600 mt-1">
  Email is already in use
</p>

<!-- Success message -->
<input class="border-green-500" />
<p class="text-xs text-green-600 mt-1">
  ✓ Email verified
</p>
```

---

## Number Formatting

### Large Numbers (Metrics)

```html
<!-- XP/Points Display (Larger, Bolder) -->
<div class="text-3xl font-bold text-blue-600">1,250</div>
<p class="text-sm text-gray-500">XP earned</p>

<!-- Stats Counter -->
<div class="text-2xl font-semibold">45</div>
<p class="text-xs text-gray-600">Flashcards mastered</p>

<!-- Progress Percentage -->
<div class="text-xl font-bold text-green-600">87%</div>
<p class="text-sm text-gray-600">Completion rate</p>
```

### Currency

```html
<!-- Dollar amount (prominent) -->
<span class="text-2xl font-bold">$99</span>
<span class="text-sm text-gray-600">/month</span>

<!-- Inline currency -->
Price: <span class="font-semibold">$19.99</span>

<!-- Calorie count -->
<span class="text-lg font-semibold">450</span>
<span class="text-sm text-gray-600">kcal</span>
```

### Time/Duration

```html
<!-- Duration display -->
<span class="font-semibold">45</span>
<span class="text-sm text-gray-600">minutes</span>

<!-- Time format -->
<time class="text-sm text-gray-500">2:30 PM</time>

<!-- Relative time -->
<span class="text-xs text-gray-400">2 hours ago</span>
```

---

## Code & Technical Text

### Inline Code

```css
code {
  font-family: 'SFMono-Regular', 'Consolas', 'Liberation Mono', monospace;
  font-size: 0.875em;         /* Slightly smaller than surrounding text */
  background-color: #f5f5f5;
  color: #d63384;             /* Purple/red tone */
  padding: 0.25em 0.5em;
  border-radius: 0.25rem;
}
```

### Code Blocks

```css
pre {
  font-family: 'SFMono-Regular', 'Consolas', 'Liberation Mono', monospace;
  font-size: 0.875rem;        /* 14px */
  line-height: 1.6;
  background-color: #1e1e1e;  /* Dark background */
  color: #e0e0e0;             /* Light text */
  padding: 1rem;
  border-radius: 0.5rem;
  overflow-x: auto;
  tab-size: 2;
}

pre code {
  background: none;
  color: inherit;
  padding: 0;
}
```

### Terminal/Output

```css
.terminal-output {
  font-family: 'SFMono-Regular', 'Consolas', 'Liberation Mono', monospace;
  font-size: 0.8125rem;       /* 13px */
  line-height: 1.5;
  background-color: #0d0d0d;
  color: #00d700;             /* Terminal green */
  padding: 1rem;
  border-radius: 0.25rem;
}
```

---

## Responsive Typography

### Scaling Rules

Typography scales proportionally on smaller screens while maintaining readability.

```css
/* Mobile (< 640px) */
@media (max-width: 639px) {
  h1 { font-size: 1.875rem; }     /* 5xl → 4xl */
  h2 { font-size: 1.5rem; }       /* 3xl → 2xl */
  h3 { font-size: 1.25rem; }      /* 2xl → xl */
  p { font-size: 1rem; }          /* base stays same */
}

/* Tablet (640px - 1024px) */
@media (min-width: 640px) and (max-width: 1023px) {
  h1 { font-size: 2rem; }         /* 5xl */
  h2 { font-size: 1.875rem; }     /* 4xl */
  h3 { font-size: 1.5rem; }       /* 3xl */
}

/* Desktop (> 1024px) */
@media (min-width: 1024px) {
  h1 { font-size: 2.25rem; }      /* 5xl */
  h2 { font-size: 2rem; }         /* 4xl */
  h3 { font-size: 1.875rem; }     /* 3xl */
}
```

### Responsive Scale Implementation

```typescript
// tailwind.config.ts
export default {
  theme: {
    fontSize: {
      xs: ['0.6875rem', { lineHeight: '1rem' }],     /* 11px */
      sm: ['0.8125rem', { lineHeight: '1.25rem' }],  /* 13px */
      base: ['1rem', { lineHeight: '1.5rem' }],      /* 16px */
      lg: ['1.125rem', { lineHeight: '1.75rem' }],   /* 18px */
      xl: ['1.25rem', { lineHeight: '1.75rem' }],    /* 20px */
      '2xl': ['1.40625rem', { lineHeight: '2rem' }], /* 22.5px */
      '3xl': ['1.58125rem', { lineHeight: '2rem' }], /* 25.3px */
      '4xl': ['1.775rem', { lineHeight: '2.25rem' }], /* 28.4px */
      '5xl': ['2rem', { lineHeight: '2.5rem' }],     /* 32px */
      '6xl': ['2.25rem', { lineHeight: '2.5rem' }]   /* 36px */
    }
  }
}
```

---

## Accessibility Guidelines

### Minimum Font Sizes

- Body text: minimum 16px (prevents automatic zoom on mobile)
- Captions: minimum 12px
- Form labels: minimum 13px
- All text must be readable without zooming

### Sufficient Contrast

- Normal text: 4.5:1 ratio (WCAG AA)
- Large text (18px+): 3:1 ratio (WCAG AA)

### Text Density

- Line length: 50-75 characters (comfortable reading)
- Paragraph length: 3-7 lines (optimal)
- Spacing between paragraphs: 1.5-2x font size

```css
/* Optimal reading width */
p, article {
  max-width: 65ch;  /* ~65 characters */
}
```

---

## Print Styles

```css
@media print {
  body {
    font-size: 12pt;
    line-height: 1.5;
    color: black;
    background: white;
  }

  h1, h2, h3, h4, h5, h6 {
    page-break-after: avoid;
  }

  p {
    orphans: 3;  /* Minimum lines at page break */
    widows: 3;
  }

  code {
    background: white;
    border: 1px solid #999;
  }
}
```

---

## Common Text Patterns

### Breadcrumb Navigation

```html
<nav class="text-sm text-gray-600">
  <a href="/" class="hover:text-gray-900">Home</a>
  <span class="mx-2">/</span>
  <a href="/study" class="hover:text-gray-900">Study</a>
  <span class="mx-2">/</span>
  <span class="text-gray-900">Flashcards</span>
</nav>
```

### Badge/Tag

```html
<span class="inline-block px-2 py-1 text-xs font-semibold bg-blue-100 text-blue-800 rounded">
  In Progress
</span>
```

### Tooltip Text

```html
<span class="text-xs text-gray-500 whitespace-nowrap">
  Hover for details
</span>
```

### Status Text

```html
<!-- Success -->
<span class="text-sm font-medium text-green-600">✓ Completed</span>

<!-- Error -->
<span class="text-sm font-medium text-red-600">✗ Failed</span>

<!-- Pending -->
<span class="text-sm font-medium text-yellow-600">⏳ Pending</span>
```

---

## Typography Checklist

When adding new text to the app:

- [ ] Use appropriate heading level (H1-H6)
- [ ] Font size from defined scale (11-36px)
- [ ] Font weight from available set (300, 400, 500, 600, 700)
- [ ] Line height appropriate to context (1.2-1.8)
- [ ] Contrast ratio at least 4.5:1
- [ ] Minimum 16px for body text
- [ ] Consistent spacing between elements
- [ ] Responsive font scaling on mobile
- [ ] No orphaned words at line breaks
- [ ] Adequate letter spacing for readability

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Design & UX Team
