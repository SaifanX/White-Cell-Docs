# Support Page

**File Path:** `src/pages/Support.tsx` (296 lines)

## Overview

The Support page is the customer help and support hub. It provides multiple channels for users to get help, access documentation, report bugs, suggest features, view FAQs, and contact the support team directly.

## Page Purpose

- **Primary Function:** Customer support hub
- **Target Users:** All users needing help
- **Key Actions:** Send support message, view FAQs, access resources
- **Multiple Channels:** Email, Discord, bug reports, feature requests
- **Support Email:** saifanmohammed871@gmail.com

## Page Structure

### Section 1: Navigation & Header

**Back Button:**
- "← Back to Home"
- Navigates to `/`
- Gray variant button

**Page Header:**
- HelpCircle icon (large)
- Title: "Support Center"
- Subtitle: "We're here to help you get the most out of White Cell Protocol"

### Section 2: Support Options Grid

**4 Quick Access Cards:**

**Grid Layout:**
- Desktop: 4-column grid
- Tablet: 2-column grid
- Mobile: 1-column
- Gap: 16px
- Responsive heights

**Option 1: Documentation**
- Icon: 📖 Book (blue)
- Title: "Documentation"
- Description: "Browse our comprehensive guides and tutorials"
- Color: text-blue-500
- Action: Navigate to `/docs`

**Option 2: Community**
- Icon: 💬 MessageCircle (purple)
- Title: "Community"
- Description: "Join our Discord community for peer support"
- Color: text-purple-500
- Action: Open Discord link (external)
- Link: https://discord.gg/whitecellprotocol

**Option 3: Report a Bug**
- Icon: 🐛 Bug (red)
- Title: "Report a Bug"
- Description: "Help us improve by reporting issues"
- Color: text-red-500
- Action: Navigate to `/settings`
- Note: Bug report form in settings

**Option 4: Feature Request**
- Icon: 💡 Lightbulb (yellow)
- Title: "Feature Request"
- Description: "Suggest new features and improvements"
- Color: text-yellow-500
- Action: Navigate to `/settings`
- Note: Feature request form in settings

**Card Features:**
- Click to execute action
- Hover: Scale 1.05, border highlight
- Icon centered and large
- Title and description
- Smooth transitions

### Section 3: Contact Support Form

**Form Layout:**
- Left column (lg:) on desktop
- Above FAQs on mobile
- Card container with border
- Padding: 24px

**Form Header:**
- 📧 Mail icon
- Title: "Contact Support"
- Description: "Send us a message and we'll get back to you as soon as possible"

**Form Fields:**

**1. Name Field**
- Label: "Name"
- Input type: text
- Placeholder: "Your name"
- Required: true
- Validation: Non-empty

**2. Email Field**
- Label: "Email"
- Input type: email
- Placeholder: "your.email@example.com"
- Required: true
- Validation: Valid email format

**3. Subject Field**
- Label: "Subject"
- Input type: text
- Placeholder: "What do you need help with?"
- Required: true
- Validation: Non-empty

**4. Message Field**
- Label: "Message"
- Input type: textarea
- Placeholder: "Describe your issue or question..."
- Rows: 5
- Required: true
- Validation: Non-empty

**Submit Button:**
- Type: submit
- Text: "📧 Send Message"
- Full width
- Mail icon on left
- Disabled if form invalid

**Form Handling:**

```typescript
const handleSubmit = (e: React.FormEvent) => {
  e.preventDefault();
  
  // Validation
  if (!name || !email || !subject || !message) {
    toast.error("Please fill in all fields");
    return;
  }

  // Create mailto link
  const mailtoLink = `mailto:saifanmohammed871@gmail.com?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(`Name: ${name}\nEmail: ${email}\n\nMessage:\n${message}`)}`;
  window.location.href = mailtoLink;
  
  // Success feedback
  toast.success("Opening your email client...");
  
  // Reset form
  setName("");
  setEmail("");
  setSubject("");
  setMessage("");
};
```

**Functionality:**
- Validate all fields required
- Create mailto: link with form data
- Open user's email client
- Auto-populate To, Subject, Body
- Clear form after submit
- Show success toast

### Section 4: FAQ Section

**FAQ Layout:**
- Right column (lg:) on desktop
- Below contact form on mobile
- Card container
- Scrollable if many items

**FAQ Header:**
- ❓ HelpCircle icon
- Title: "Frequently Asked Questions"
- Description: "Quick answers to common questions"

**5 FAQ Items:**

**FAQ 1: Password Reset**
- Q: "How do I reset my password?"
- A: "White Cell Protocol uses email OTP authentication. Simply sign out and sign in again with your email to receive a new code."

**FAQ 2: Offline Mode**
- Q: "Can I use the app offline?"
- A: "Yes! Install the PWA version of the app for offline access to most features. Some features requiring real-time data will need an internet connection."

**FAQ 3: Third-Party Integrations**
- Q: "How do I connect third-party integrations?"
- A: "Go to Settings and scroll to the integrations section. You can connect GitHub, Notion, Lichess, and Spotify from there."

**FAQ 4: Data Security**
- Q: "Is my data secure?"
- A: "Yes, we use industry-standard encryption and security practices. Read our Privacy Policy for more details."

**FAQ 5: XP System**
- Q: "How does the XP and leveling system work?"
- A: "You earn XP by completing tasks, workouts, study sessions, and maintaining streaks. Every 100 XP advances you to the next level."

**FAQ Item Structure:**
```
Q: "Your question here?"
─────────────────────
A: "Detailed answer explaining..."
```

**Styling:**
- Question: Bold, larger font
- Answer: Regular, muted gray
- Border bottom between items
- Last item has no border
- Padding around each item

### Section 5: Additional Resources

**Resource Links Card:**

**Resource 1: Email Support**
- Label: "Email Support"
- Contact: "saifanmohammed871@gmail.com"
- Button: "📧 Email" (outline)
- Action: window.location.href = mailto:
- Visual: Muted background

**Resource 2: Instagram**
- Label: "Instagram"
- Handle: "@white_cell_protocol"
- Button: "🔗 Follow" (outline)
- Action: window.open() external
- Link: https://www.instagram.com/white_cell_protocol?igsh=Z3BhdGIzcmYzZ3N3
- Visual: Muted background

**Layout:**
- Vertical stack
- Each resource in box
- Icon on right side
- Flex justify-between
- Muted background (bg-muted/50)
- Rounded corners

## State Management

**Support Page State:**
```typescript
const [name, setName] = useState("");
const [email, setEmail] = useState("");
const [subject, setSubject] = useState("");
const [message, setMessage] = useState("");

const handleSubmit = (e: React.FormEvent) => {
  // Form handling logic
};
```

**Form Validation:**
- All fields required
- Email format validated
- Client-side validation only
- Mailto fallback approach

## Features

### Mailto Integration

**Email Client Open:**
```typescript
const mailtoLink = `mailto:saifanmohammed871@gmail.com?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(`Name: ${name}\nEmail: ${email}\n\nMessage:\n${message}`)}`;
window.location.href = mailtoLink;
```

**Behavior:**
- Opens user's default email client
- Pre-fills To, Subject, Body
- User adds additional details if needed
- Personal email client used (not web form)
- Fallback if no email client configured

**Supported Email Clients:**
- Desktop: Outlook, Gmail, Apple Mail, Thunderbird
- Mobile: Default email app
- Web: Gmail, Outlook web

### Toast Notifications

**Success Toast:**
- Message: "Opening your email client..."
- Duration: 3 seconds
- Green color (success)

**Error Toast:**
- Message: "Please fill in all fields"
- Duration: 3 seconds
- Red color (error)

## Animations

**Page Load:**
- Section animations: Fade + slide
- Cards: Staggered delays
- Duration: 300-600ms
- Easing: ease-in-out

**Interactions:**
- Button hover: Scale 1.05
- Click: Scale 0.98
- Smooth transitions

**Form States:**
- Input focus: Color highlight
- Valid state: No feedback (implicit)
- Error state: Red border + message

## Responsive Design

**Mobile (< 640px):**
- 1-column layout
- Full-width forms
- Contact form above FAQ
- Single column support options
- Touch-friendly sizing

**Tablet (640-1024px):**
- 2-column for support options
- Contact form + FAQ side by side
- Balanced spacing

**Desktop (> 1024px):**
- 4-column support grid
- Contact form left, FAQ right
- Full hover states
- Optimal reading widths

## Accessibility

- Semantic form elements
- Label associations
- Form validation feedback
- Color + text for status
- Sufficient contrast
- Keyboard navigation
- Focus indicators
- Screen reader support
- Error messages clear

## SEO

**Meta Tags:**
- Title: "Support - White Cell Protocol | Help Center, FAQ & Contact"
- Description: "Get help with White Cell Protocol. Contact support, browse documentation, report bugs, and find answers to frequently asked questions. Email support available at saifanmohammed871@gmail.com."
- Keywords: "white cell protocol support, help center, customer support, FAQ, frequently asked questions, contact support, technical support, app help, troubleshooting, bug report, feature request"
- Type: article

## Performance

**Optimizations:**
- Minimal state management
- Static content
- Lazy load animations
- No external data fetching
- Optimized form inputs

**Metrics:**
- FCP: < 1s
- LCP: < 2s
- CLS: < 0.05

## Related Components

- `Button.tsx` - Action buttons
- `Card.tsx` - Section containers
- `Input.tsx` - Form inputs
- `Textarea.tsx` - Message field
- `Label.tsx` - Form labels
- `SEOHead.tsx` - Meta tags

## Related Pages

- `/documentation` - User guide
- `/` - Home page
- `/settings` - Bug/feature request
- External: Discord, Instagram, Email

## Contact Information

**Primary Contact:**
- Email: saifanmohammed871@gmail.com
- Response time: 24-48 hours

**Social Media:**
- Instagram: @white_cell_protocol
- Discord: https://discord.gg/whitecellprotocol

**Support Channels:**
1. Email (fastest for urgent issues)
2. Discord (community help)
3. In-app documentation
4. Settings forms (structured reports)

## Future Enhancements

- [ ] Live chat support
- [ ] Help ticket system
- [ ] Video tutorials
- [ ] Community forums
- [ ] Knowledge base
- [ ] Status page
- [ ] Response time tracking
- [ ] Support chatbot

## Technical Stack

- **Forms:** React useState
- **Routing:** React Router
- **Notifications:** Sonner toast
- **Styling:** Tailwind CSS
- **Animations:** Framer Motion
- **Icons:** Lucide React
- **Email:** Mailto protocol
- **External Links:** window.open()
- **SEO:** Custom SEOHead
