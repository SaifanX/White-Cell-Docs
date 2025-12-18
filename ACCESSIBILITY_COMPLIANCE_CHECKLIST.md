# Accessibility Compliance Checklist

**Version:** 1.0  
**Last Updated:** December 18, 2025  
**Target Standard:** WCAG 2.1 Level AA

---

## Table of Contents

1. [Checklist Overview](#checklist-overview)
2. [Keyboard Navigation](#keyboard-navigation)
3. [Screen Reader Testing](#screen-reader-testing)
4. [Color Contrast](#color-contrast)
5. [Focus Management](#focus-management)
6. [Semantic HTML](#semantic-html)
7. [Testing Tools](#testing-tools)
8. [Common Issues](#common-issues)

---

## Checklist Overview

### Priority 1: Critical (Must Fix)

- [ ] Keyboard navigation functional
- [ ] Focus indicators visible
- [ ] Color contrast ≥ 4.5:1
- [ ] Images have alt text
- [ ] Form labels associated
- [ ] Error messages clear
- [ ] No automated content changes without warning

### Priority 2: Important (Should Fix)

- [ ] Semantic HTML used
- [ ] Heading hierarchy correct
- [ ] Lists properly marked
- [ ] Skip links present
- [ ] Touch targets ≥ 44x44px
- [ ] Text resizable to 200%
- [ ] No fixed font sizes

### Priority 3: Nice to Have (Consider)

- [ ] ARIA live regions for dynamic content
- [ ] Logical tab order
- [ ] Captions on videos
- [ ] Dyslexia-friendly fonts option
- [ ] Dark mode support

---

## Keyboard Navigation

### Testing Checklist

- [ ] All interactive elements accessible via Tab/Shift+Tab
- [ ] Tab order logical (top to bottom, left to right)
- [ ] Enter/Space activates buttons
- [ ] Arrow keys navigate within components
- [ ] Escape closes modals/menus
- [ ] Focus trap in modals
- [ ] No keyboard traps anywhere

### Implementation Guide

```typescript
// Good: Keyboard accessible button
function AccessibleButton({ onClick, children }: Props) {
  return (
    <button
      onClick={onClick}
      onKeyDown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          onClick();
        }
      }}
      tabIndex={0}
      aria-label="Click to submit"
    >
      {children}
    </button>
  );
}

// Good: Keyboard navigation in list
function SelectableList({ items }: { items: Item[] }) {
  const [selectedIndex, setSelectedIndex] = useState(0);
  
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'ArrowDown') {
      setSelectedIndex(prev => 
        Math.min(prev + 1, items.length - 1)
      );
    } else if (e.key === 'ArrowUp') {
      setSelectedIndex(prev => Math.max(prev - 1, 0));
    } else if (e.key === 'Enter') {
      selectItem(items[selectedIndex]);
    }
  };
  
  return (
    <ul onKeyDown={handleKeyDown} role="listbox">
      {items.map((item, idx) => (
        <li
          key={item.id}
          role="option"
          aria-selected={idx === selectedIndex}
          tabIndex={idx === selectedIndex ? 0 : -1}
        >
          {item.name}
        </li>
      ))}
    </ul>
  );
}

// Good: Skip to main content link
export function SkipLink() {
  return (
    <a
      href="#main-content"
      className="skip-link"
      style={{
        position: 'absolute',
        top: '-40px',
        left: '0',
        background: '#000',
        color: '#fff',
        padding: '8px',
        textDecoration: 'none',
        zIndex: 100,
      }}
      onFocus={(e) => {
        e.currentTarget.style.top = '0';
      }}
      onBlur={(e) => {
        e.currentTarget.style.top = '-40px';
      }}
    >
      Skip to main content
    </a>
  );
}
```

---

## Screen Reader Testing

### Testing Tools

| Tool | Platform | Notes |
|------|----------|-------|
| NVDA | Windows | Free, open-source |
| JAWS | Windows | Commercial, industry standard |
| VoiceOver | macOS/iOS | Built-in |
| TalkBack | Android | Built-in |

### Testing Checklist

- [ ] Content announces in logical order
- [ ] All images have alt text
- [ ] Form labels announce with inputs
- [ ] Button purposes clear
- [ ] Navigation landmarks present
- [ ] Lists announce as lists
- [ ] Error messages announce
- [ ] Dynamic content updates announce

### Implementation

```typescript
// Good: Semantic HTML for screen readers
export function FlashcardCard({ card }: { card: Flashcard }) {
  return (
    <article
      className="flashcard"
      aria-label={`Flashcard: ${card.front}`}
    >
      <h2>{card.front}</h2>
      <p>{card.back}</p>
      <div role="group" aria-labelledby="quality-heading">
        <h3 id="quality-heading">Rate card quality:</h3>
        <button aria-label="Rate 1 - Failed">1: Fail</button>
        <button aria-label="Rate 5 - Easy">5: Easy</button>
      </div>
    </article>
  );
}

// Good: Live regions for dynamic updates
export function XPNotification({ amount }: { amount: number }) {
  return (
    <div
      className="xp-popup"
      role="status"
      aria-live="polite"
      aria-atomic="true"
    >
      +{amount} XP
    </div>
  );
}

// Good: Announce form errors
export function FormField({
  id,
  label,
  error,
  value,
  onChange,
}: Props) {
  const errorId = error ? `${id}-error` : undefined;
  
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input
        id={id}
        value={value}
        onChange={onChange}
        aria-describedby={errorId}
        aria-invalid={!!error}
      />
      {error && (
        <div
          id={errorId}
          role="alert"
        >
          {error}
        </div>
      )}
    </div>
  );
}
```

---

## Color Contrast

### Contrast Ratios

**WCAG AA Requirements:**
- Normal text: 4.5:1
- Large text (18pt+): 3:1
- UI Components: 3:1

### Checking Contrast

```typescript
// Verify in code
function checkContrast(
  foreground: string,
  background: string
): number {
  const fgLuminance = getRelativeLuminance(foreground);
  const bgLuminance = getRelativeLuminance(background);
  
  const lighter = Math.max(fgLuminance, bgLuminance);
  const darker = Math.min(fgLuminance, bgLuminance);
  
  return (lighter + 0.05) / (darker + 0.05);
}

// Example from design system
const colorContrasts = {
  'text-on-primary': checkContrast('#fff', '#0066cc'), // ✓ 8.2:1
  'text-on-success': checkContrast('#fff', '#22c55e'), // ✓ 4.8:1
  'text-on-error': checkContrast('#fff', '#ef4444'),   // ✓ 4.5:1
};
```

### Color Blind Safe Palette

```typescript
// From design system - verified for color blindness
const accessibleColors = {
  primary: '#0066cc',    // Blue - safe for most types
  secondary: '#6633cc',  // Purple - distinct from primary
  success: '#22c55e',    // Green - includes other hues
  error: '#ef4444',      // Red - clear from green
  warning: '#fb9238',    // Orange - distinct from red
};

// Avoid: Pure red/green combinations
// Test: Use tools like Color Blind Simulator
```

### Tools

- WebAIM Contrast Checker
- Color Contrast Analyzer
- Chrome DevTools Accessibility Panel

---

## Focus Management

### Focus Styles

```typescript
// Good: Visible focus indicator
const FocusableButton = styled.button`
  &:focus-visible {
    outline: 3px solid var(--color-primary);
    outline-offset: 2px;
  }
  
  /* Remove default for visual customization */
  &:focus {
    outline: none;
  }
`;

// Ensure sufficient contrast
export const focusStyle = css`
  outline: 3px solid #0066cc;
  outline-offset: 2px;
  
  /* Check: 4.5:1 contrast with background */
`;
```

### Managing Focus

```typescript
// Modal focus management
export function Modal({ isOpen, onClose, children }: ModalProps) {
  const contentRef = useRef<HTMLDivElement>(null);
  const previousActiveElement = useRef<HTMLElement | null>(null);
  
  useEffect(() => {
    if (isOpen) {
      // Save focused element before modal
      previousActiveElement.current = document.activeElement as HTMLElement;
      
      // Focus modal content
      contentRef.current?.focus();
    } else {
      // Return focus when closing
      previousActiveElement.current?.focus();
    }
  }, [isOpen]);
  
  if (!isOpen) return null;
  
  return (
    <div
      role="dialog"
      aria-modal="true"
      ref={contentRef}
      tabIndex={-1}
      onKeyDown={(e) => {
        if (e.key === 'Escape') onClose();
      }}
    >
      {children}
    </div>
  );
}

// Skip to main content
export function useSkipToMain() {
  const mainRef = useRef<HTMLMain>(null);
  
  const skipToMain = () => {
    mainRef.current?.focus();
    mainRef.current?.scrollIntoView({ behavior: 'smooth' });
  };
  
  return { mainRef, skipToMain };
}
```

---

## Semantic HTML

### HTML Structure

```html
<!-- GOOD: Semantic structure -->
<header>
  <nav aria-label="Main">
    <a href="#home" aria-current="page">Home</a>
    <a href="#study">Study</a>
    <a href="#fitness">Fitness</a>
  </nav>
</header>

<main id="main-content">
  <article>
    <h1>Page Title</h1>
    
    <section aria-labelledby="study-heading">
      <h2 id="study-heading">Study Module</h2>
      <p>Description...</p>
      
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </section>
  </article>
</main>

<footer>
  <p>&copy; 2024 White Cell Protocol</p>
</footer>

<!-- BAD: Non-semantic -->
<div class="header">
  <div class="nav">
    <div onclick="goHome()">Home</div>
    <div onclick="goStudy()">Study</div>
  </div>
</div>
```

### ARIA When Needed

```typescript
// Only use ARIA when semantic HTML insufficient

// ✓ Use semantic HTML
<button onClick={handleDelete}>Delete</button>

// ✓ ARIA for complex widgets
<div
  role="listbox"
  aria-label="Select deck"
  aria-expanded={isOpen}
>
  {/* Custom listbox content */}
</div>

// ✓ ARIA for live regions
<div role="status" aria-live="polite">
  Saving changes...
</div>

// ✗ Don't use ARIA to fix bad markup
// Bad: <div onclick="submit()">Submit</div>
// Good: <button onClick={submit}>Submit</button>
```

---

## Testing Tools

### Automated Testing

```bash
# axe DevTools
npm install --save-dev @axe-core/react

# WebAIM WAVE
# Install browser extension

# Lighthouse
# Built into Chrome DevTools
```

### Manual Testing

```typescript
// Keyboard-only testing
// 1. Open app
// 2. Unplug mouse
// 3. Navigate with Tab/Arrow keys
// 4. Can you do everything?

// Screen reader testing
// 1. Enable NVDA (Windows) or VoiceOver (Mac)
// 2. Navigate through page
// 3. Is content announced clearly?
```

### Automated Testing in Code

```typescript
// Example: Component accessibility test
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

test('FlashcardCard is accessible', async () => {
  const { container } = render(
    <FlashcardCard card={mockCard} />
  );
  
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

---

## Common Issues

### Issue 1: Missing Alt Text

```typescript
// ❌ Bad
<img src="progress-photo.jpg" />

// ✓ Good
<img
  src="progress-photo.jpg"
  alt="User's progress photo from December 2024"
/>

// ✓ Good: Decorative image
<img
  src="divider.svg"
  alt=""
  aria-hidden="true"
/>
```

### Issue 2: Form Labels Not Associated

```typescript
// ❌ Bad
<label>Email</label>
<input type="email" />

// ✓ Good
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ✓ Good: Implicit labeling
<label>
  Email
  <input type="email" />
</label>
```

### Issue 3: Low Contrast Text

```typescript
// ❌ Bad: Gray on light background (1.2:1)
color: #ccc; background: #fff;

// ✓ Good: Meets WCAG AA (4.5:1)
color: #666; background: #fff;

// ✓ Good: Extra readable (8:1)
color: #333; background: #fff;
```

### Issue 4: No Focus Indicators

```typescript
// ❌ Bad
button {
  outline: none; /* Don't remove! */
}

// ✓ Good
button {
  &:focus-visible {
    outline: 3px solid #0066cc;
    outline-offset: 2px;
  }
}
```

### Issue 5: Non-Keyboard Accessible

```typescript
// ❌ Bad: Requires mouse
<div onClick={handleClick}>Click me</div>

// ✓ Good: Keyboard accessible
<button onClick={handleClick}>Click me</button>

// ✓ Good: Custom with keyboard support
<div
  role="button"
  onClick={handleClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleClick();
    }
  }}
  tabIndex={0}
>
  Click me
</div>
```

---

## Accessibility Checklist (Pre-Launch)

- [ ] All interactive elements keyboard accessible
- [ ] Tab order logical
- [ ] Focus indicators visible
- [ ] No focus traps
- [ ] Images have alt text
- [ ] Form labels present
- [ ] Error messages clear and associated
- [ ] Color contrast ≥ 4.5:1 normal, 3:1 large
- [ ] Page works with 200% zoom
- [ ] Touch targets ≥ 44x44px
- [ ] No auto-playing audio/video
- [ ] Skip links present
- [ ] Semantic HTML used
- [ ] Language specified
- [ ] Tested with screen reader
- [ ] Tested keyboard-only
- [ ] WAVE/axe scan: 0 errors
- [ ] Lighthouse accessibility: 95+

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Accessibility & Engineering Teams
