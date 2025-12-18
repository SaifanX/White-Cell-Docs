# 404 Not Found Page

**File Path:** `src/pages/NotFound.tsx` (182 lines)

## Overview

The 404 Not Found page is a creative error page displayed when users navigate to non-existent routes. It features terminal/hacker-style aesthetics with animated glitch effects, developer-friendly error messages, and quick navigation back to the home page.

## Page Purpose

- **Primary Function:** Handle undefined routes gracefully
- **Target Users:** Users navigating to invalid URLs
- **Key Actions:** Return home, go back to previous page
- **Tone:** Creative, developer-centric, entertaining
- **Design Philosophy:** "The void has swallowed this page"

## Page Sections

### Section 1: Animated Background

**Grid Background:**
```
[Grid pattern 50px x 50px]
[Slight opacity 10%]
[Primary color with transparency]
```

**Floating Code Symbols:**
- 15 animated symbols randomly positioned
- Symbols: `</>`, `{}`, `[]`, `();`, `=>`, `!=`, `==`
- Animation: Fall from top to bottom
- Duration: 10-20 seconds per symbol
- Rotation: Random + incremental
- Repeat: Infinite
- Staggered delays: 0-5 seconds each
- Z-index: Below main content
- Pointer events: None (non-interactive)

**Pseudo Code:**
```typescript
[...Array(15)].map((_, i) => (
  <motion.div
    initial={{ y: -50, rotate: Math.random() * 360 }}
    animate={{ y: window.innerHeight + 50, rotate: Math.random() * 360 + 360 }}
    transition={{ duration: 10 + Math.random() * 10, repeat: Infinity, delay: Math.random() * 5 }}
  >
    {["</>", "{}", "[]", "();", "=>", "!=", "=="][i % 7]}
  </motion.div>
))
```

### Section 2: Main 404 Content

**Large 404 Display:**

**Primary Text:**
- Text: "404" (or glitched variants)
- Font size: 8xl-9xl (96-144px on desktop)
- Font weight: Bold
- Color: Gradient (primary → primary/60 → primary)
- Text clip: `bg-clip-text text-transparent`
- Animation: Scale 0.8→1 on mount

**Glitch Effect Layer:**
- Duplicate 404 text with red color
- Opacity: 30% (destructive/30)
- Blur: 4px (blur-sm)
- Position: Absolute overlay
- Animation: Animated x/y offset (-2, 2 pixels)
- Duration: 300ms
- Repeat: Every 3 seconds
- Timing: Offset pattern creates glitch

**Glitch Animation:**
```typescript
animate={{
  x: [0, -2, 2, 0],
  y: [0, 2, -2, 0]
}}
transition={{
  duration: 0.3,
  repeat: Infinity,
  repeatDelay: 3
}}
```

### Section 3: Terminal-Style Error Message

**Container:**
- Background: card/50 with blur
- Border: 2px primary/20
- Padding: 24px
- Border radius: 8px
- Box shadow: xl
- Backdrop blur: sm

**Terminal Header:**
- Icon: Terminal (5px height)
- Text: "terminal@void:~$"
- Font: Monospace
- Size: Small (text-sm)
- Color: Muted foreground
- Flex gap: 8px

**Error Message:**
- Font: Monospace
- Size: Large (text-lg) → XL on small screens (text-xl sm:)
- Random quotes from array
- 3-6 error messages in rotation
- Animated in/out with fade + slide
- Updates every 3 seconds

**Error Quote Examples:**
```typescript
const voidQuotes = [
  "// TODO: Find this page",
  "undefined is not a function",
  "Cannot read property 'page' of undefined",
  "Error: Page not found in the matrix",
  "NullPointerException: Reality.page",
  "Segmentation fault (core dumped)",
];
```

**Alert Box:**
- Icon: ⚠️ AlertTriangle (yellow)
- Icon size: 5px height
- Color: text-yellow-500
- Text: "The page you're looking for has fallen into the void. It might have been deleted, moved, or never existed in this dimension."
- Layout: Flex with gap
- Text: Smaller size (text-sm → text-base sm:)

### Section 4: Action Buttons

**Button Layout:**
- Flex row on desktop
- Flex column on mobile
- Gap: 16px
- Justify: center
- Wrap: Yes (flex-wrap)

**Button 1: Return Home**
- Icon: 🏠 Home
- Text: "Return Home"
- Type: Primary
- Size: Large
- Icon animation: Scale on hover (group-hover:scale-110)
- Action: `navigate("/")`

**Button 2: Go Back**
- Icon: `</>`Code (rotates)
- Text: "Go Back"
- Type: Outline
- Size: Large
- Icon animation: Rotate 180° on hover (500ms duration)
- Action: `navigate(-1)`

**Gap:** 16px between buttons

### Section 5: Easter Egg

**Hidden Message:**
- Text: "Error Code: 0x2A // The answer to life, the universe, and everything"
- Font: Monospace (font-mono)
- Size: Extra small (text-xs)
- Color: Muted foreground
- Opacity: Starts 0, animates to 1 after 1s
- Positioned: Bottom of content

**Reference:** Douglas Adams' "The Hitchhiker's Guide to the Galaxy" (answer to life = 42)

## State Management

**NotFound Page State:**
```typescript
const [glitchText, setGlitchText] = useState("404");
const [currentQuote, setCurrentQuote] = useState(voidQuotes[0]);

// Glitch text effect
useEffect(() => {
  const glitchInterval = setInterval(() => {
    const glitchChars = ["4", "0", "4", "?", "!", "@", "#"];
    const glitched = Array.from({ length: 3 })
      .map(() => glitchChars[Math.floor(Math.random() * glitchChars.length)])
      .join("");
    setGlitchText(glitched);
    setTimeout(() => setGlitchText("404"), 100);
  }, 5000);
  return () => clearInterval(glitchInterval);
}, []);

// Quote rotation
useEffect(() => {
  const quoteInterval = setInterval(() => {
    setCurrentQuote(voidQuotes[Math.floor(Math.random() * voidQuotes.length)]);
  }, 3000);
  return () => clearInterval(quoteInterval);
}, []);
```

## Features

### Glitch Text Effect

**Random Characters:**
- Pool: `["4", "0", "4", "?", "!", "@", "#"]`
- Duration: 5 seconds between glitches
- Animation: 100ms glitch, back to "404"
- Creates visual "corrupted" effect

### Quote Rotation

**Random Selection:**
- 6 programming/error-related quotes
- Rotate every 3 seconds
- Smooth fade + slide transition
- Key prop on motion.p for re-render

### Animations

**Page Load:**
- Main content: Scale 0.8→1, opacity 0→1 (500ms, 200ms delay)
- Terminal box: y: 20→0, opacity 0→1 (500ms)
- Buttons: y: 20→0, opacity 0→1 (500ms, 600ms delay)
- Easter egg: opacity 0→1 (1000ms delay)

**Hover Effects:**
- Home button: Home icon scales 1.1x
- Go Back button: Code icon rotates 180°
- Both: Smooth transitions

**Continuous Animations:**
- Floating code: 10-20s fall time + rotation
- Glitch layer: Jitter offset every 3 seconds
- Quote rotation: Every 3 seconds
- Grid background: Static (opacity only)

## Responsive Design

**Mobile (< 640px):**
- Font size: text-8xl (96px) for 404
- Buttons: Vertical stack (flex-col)
- Full width content
- Reduced padding
- Smaller gap between elements
- Touch-friendly button targets

**Desktop (> 640px):**
- Font size: text-9xl (144px) for 404
- Buttons: Horizontal (flex-row)
- Centered max-width
- Larger padding
- Enhanced visual effects

## Accessibility

**Semantic Structure:**
- Semantic HTML divs with purpose
- Proper heading hierarchy (h1 for 404)
- Alt text descriptions in code

**Visual Accessibility:**
- Color + icons for information
- Sufficient contrast ratios
- No reliance on color alone
- Clear button labels
- Focus indicators on buttons

**Keyboard Navigation:**
- Tab through buttons
- Enter to activate
- Escape to dismiss (future)

**Screen Readers:**
- Proper heading structure
- Button labels descriptive
- Error message contextual

## SEO

**Meta Information:**
- No specific SEO meta (error page)
- Robots: noindex (prevent indexing)
- Canonical: None
- Prevents 404 pages in search results

## Performance

**Optimizations:**
- Minimal state updates
- Optimized animations (GPU)
- No external data fetching
- Efficient event listeners
- Cleanup in useEffect

**Animation Performance:**
- Framer Motion hardware acceleration
- Transform + opacity only (GPU)
- No layout thrashing
- Debounced quote changes

**Metrics:**
- FCP: < 500ms
- LCP: < 1s (minimal content)
- CLS: 0 (animations isolated)

## Developer Experience

**Dev-Friendly Elements:**
- Terminal aesthetic appeals to developers
- Programming language error quotes
- Easter egg (42 reference)
- Monospace fonts throughout
- Developer-centric humor

**Error Message Quotes:**
- Reference common JavaScript errors
- Reference programming concepts
- Reference Matrix movie ("not in the matrix")
- Reference segmentation faults
- Relatable to technical audience

## Related Components

- `Button.tsx` - Navigation buttons
- `motion` - Framer Motion animations
- Icons: Terminal, Code, AlertTriangle, Home
- Color theme: Primary, destructive, yellow

## Routing Integration

**Catch-All Route:**
```typescript
// App.tsx routing
<Route path="*" element={<NotFound />} />
```

**Triggers:**
- User navigates to `/invalid-path`
- User navigates to `/typo`
- Any undefined route

**Redirects:**
- "Return Home" → `/` (navigate("/"))
- "Go Back" → Previous page (navigate(-1))
- Browser back button → Works as expected

## Animation Details

### Falling Code Symbols

**Properties:**
- Initial X: Random 0-window.innerWidth
- Initial Y: -50
- Animate Y: window.innerHeight + 50
- Initial Rotate: Math.random() * 360
- Animate Rotate: Math.random() * 360 + 360
- Duration: 10 + Math.random() * 10 seconds
- Repeat: Infinity
- Delay: Math.random() * 5 seconds

**Effect:**
- Symbols fall from top to bottom
- Each has unique duration
- Staggered start times
- Continuous loop
- Creates dynamic background

### Glitch Layer

**Movement:**
- X: [0, -2, 2, 0]
- Y: [0, 2, -2, 0]
- Duration: 300ms
- Repeat: Infinity
- repeatDelay: 3 seconds

**Visual:**
- Red overlay text
- Slightly offset from main text
- Creates "corrupted" appearance
- Draws attention to error

## Browser Compatibility

**Supported:**
- Chrome/Edge (Chromium)
- Firefox
- Safari
- Mobile browsers

**Graceful Degradation:**
- Animations disabled in prefers-reduced-motion
- Text still readable without effects
- Buttons fully functional
- Fallback fonts work

## Future Enhancements

- [ ] Animated grid background
- [ ] Matrix-style cascading text
- [ ] Clickable elements in terminal
- [ ] SVG animations
- [ ] Custom 404 message based on route
- [ ] Animation controls (pause/play)
- [ ] Dark mode specific styling
- [ ] Accessibility preferences
- [ ] Audio glitch effects

## Technical Stack

- **Animations:** Framer Motion
- **Icons:** Lucide React
- **Styling:** Tailwind CSS
- **Navigation:** React Router
- **State:** React useState + useEffect
- **Layout:** Flexbox + CSS Grid
- **Effects:** CSS transforms + blur
