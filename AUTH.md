# Auth Page (Authentication)

**File Path:** `src/pages/Auth.tsx` (344 lines)

## Overview

The Auth page is the gateway for user authentication in White Cell Protocol. It supports passwordless email OTP (One-Time Password) authentication and guest login, providing a frictionless sign-in experience without requiring password management.

## Page Purpose

- **Primary Function:** User authentication and account access
- **Target Users:** New and existing users
- **Key Actions:** Sign in with email, verify OTP, guest login
- **Security:** Email-based OTP, rate limiting, Convex authentication
- **Redirect:** After auth → `/empire` dashboard or custom redirect

## Authentication Flows

### Flow 1: Email OTP Authentication

**Two-Step Process:**

```typescript
{
  step: "signIn" | { email: string },
  otp: string (6 digits),
  cooldownSeconds: number,
  emailRateLimit: { allowed: boolean, message: string }
}
```

**Step 1: Email Entry**
- User enters email address
- Form validates email format
- Submit triggers OTP generation and email send
- Rate limiting prevents spam (45-second cooldown)
- Feedback message shows: "Wait {X} seconds before requesting another code"

**Step 2: OTP Verification**
- OTP delivered to user's email
- 6-digit input field with individual character slots
- Auto-focus on each slot
- Enter key submits when all 6 digits entered
- Error handling for incorrect codes
- Option to request new code or change email

### Flow 2: Guest Login

**Anonymous Access:**
```typescript
const handleGuestLogin = async () => {
  await signIn("anonymous");
  navigate(redirectAfterAuth || "/");
};
```

**Benefits:**
- Explore app without account
- Create temporary profile
- Convert to permanent account anytime
- Access all core features
- Data persists during session

**Limitations:**
- No data persistence (clears on logout)
- Some advanced features locked
- Can't sync across devices
- Temporary account (30-day expiry)

## UI Components

### Card Layout

**Email Sign-In Card:**
- Logo (clickable → home)
- Title: "Get Started"
- Description: "Enter your email to log in or sign up"
- Email input with Mail icon
- Submit button (arrow icon)
- Cooldown timer display
- Rate limit warning (if applicable)
- Divider: "Or continue with"
- Guest login button

**OTP Verification Card:**
- Title: "Check your email"
- Description: "We've sent a code to {email}"
- 6-slot OTP input component
- Error message (red text)
- Help text: "Didn't receive a code? Try again" (button)
- Verify button (enabled only with 6 digits)
- "Use different email" button

**Footer:**
- "Secured by vly.ai" link
- Branding

## State Management

**Auth State:**
```typescript
const [step, setStep] = useState<"signIn" | { email: string }>("signIn");
const [otp, setOtp] = useState("");
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState<string | null>(null);
const [emailForRateCheck, setEmailForRateCheck] = useState<string>("");
const [cooldownSeconds, setCooldownSeconds] = useState(0);

// Rate limit query
const emailRateLimit = useQuery(
  (api as any).otpRateLimit.checkEmailRateLimit,
  emailForRateCheck ? { email: emailForRateCheck } : "skip"
);
```

**State Transitions:**
- Initial: `step = "signIn"`
- After email: `step = { email: "user@example.com" }`
- After OTP verification: Navigate to `/empire`

## Features

### Rate Limiting

**Purpose:** Prevent brute force attacks and email spam

**Implementation:**
```typescript
const emailRateLimit = useQuery(
  (api as any).otpRateLimit.checkEmailRateLimit,
  emailForRateCheck ? { email: emailForRateCheck } : "skip"
);

if (emailRateLimit && !emailRateLimit.allowed) {
  // Show warning and disable button
}
```

**Limits:**
- 5 OTP requests per email per 15 minutes
- 10 failed attempts per 24 hours
- 45-second cooldown after successful send

**Error Messages:**
- "Too many requests. Please try again in {X} minutes"
- "Account temporarily locked due to failed attempts"

### Cooldown Timer

**User Experience:**
- After OTP sent, show countdown: "⏱️ 45 seconds"
- Button disabled during cooldown
- Show remaining seconds on button
- Automatically re-enable after timer expires

**Implementation:**
```typescript
useEffect(() => {
  if (cooldownSeconds > 0) {
    const timer = setTimeout(() => {
      setCooldownSeconds(cooldownSeconds - 1);
    }, 1000);
    return () => clearTimeout(timer);
  }
}, [cooldownSeconds]);
```

### OTP Input

**InputOTP Component:**
- 6 individual input slots
- Auto-focus next slot after digit entry
- Auto-submit when all 6 digits filled
- Backspace deletes and focuses previous
- Paste support (accepts clipboard)
- Disabled state during verification

**Keyboard Shortcuts:**
- `Enter`: Submit OTP
- `Backspace`: Delete and focus previous
- `Tab`: Navigate between slots
- `Cmd/Ctrl+V`: Paste OTP code

### Auto-Submit OTP

```typescript
onKeyDown={(e) => {
  if (e.key === "Enter" && otp.length === 6 && !isLoading) {
    const form = (e.target as HTMLElement).closest("form");
    if (form) {
      form.requestSubmit();
    }
  }
}}
```

## Error Handling

**Validation Errors:**

| Error | Cause | Resolution |
|-------|-------|-----------|
| Invalid email | Wrong format | Check email and retry |
| Incorrect OTP | Wrong 6 digits | Request new code |
| Rate limit exceeded | Too many attempts | Wait 15 minutes |
| Account locked | Multiple failures | Contact support |
| Email not verified | System error | Resend verification |

**User Feedback:**
- Error messages in red text
- Clear explanation of issue
- Actionable steps to resolve
- Toast notifications for status
- Loading spinner during requests

## Security Features

**Authentication Methods:**
- Passwordless OTP (no password storage)
- Email verification (ownership proof)
- Rate limiting (brute force protection)
- Session tokens (Convex managed)
- HTTPS only (secure transmission)
- OTP expiration (6-minute validity)

**Data Protection:**
- No passwords stored
- OTP never shown in logs
- Email obfuscation in error messages
- Rate limit by IP + email
- Account lockout after failures

**Third-Party Security:**
- Auth provider: Convex (vly.ai)
- Email sender: Secure SMTP
- Token management: Secure session storage
- CSRF protection built-in

## Responsive Design

**Mobile (< 640px):**
- Full-width card (min-width 350px)
- Large input fields (48px height)
- Large buttons (44px height)
- Single column layout
- Touch-optimized spacing

**Desktop (> 1024px):**
- Centered card on screen
- Larger text inputs
- Hover states on buttons
- Smooth transitions

## Accessibility

- Semantic form structure
- Label associations
- Email input type (mobile keyboard)
- Focus management
- Error announcements
- Color contrast (WCAG AA)
- Keyboard navigation
- Screen reader support

## API Integration

**Mutations:**
```typescript
const { signIn } = useAuth();

// Email sign-in
await signIn("email-otp", formData);

// Guest login
await signIn("anonymous");

// OTP verification
await signIn("email-otp", formData); // with code field
```

**Queries:**
```typescript
const emailRateLimit = useQuery(
  api.otpRateLimit.checkEmailRateLimit,
  { email: emailForRateCheck }
);
```

## Redirect Logic

**After Authentication:**
```typescript
useEffect(() => {
  if (!authLoading && isAuthenticated) {
    const redirect = redirectAfterAuth || "/empire";
    navigate(redirect);
  }
}, [authLoading, isAuthenticated]);
```

**Default Redirects:**
- Authenticated user (no redirect param) → `/empire`
- With redirect param → Use specified path
- Guest login → `/` (home)

## Loading States

**Email Submission:**
- Button shows spinner
- Input disabled
- Error cleared
- Show 45-second cooldown after success

**OTP Verification:**
- Button shows: "Verifying..." with spinner
- All inputs disabled
- Loading state persists until response

## Connection to Home

**Logo Click Action:**
```typescript
<Logo onClick={() => navigate("/")} />
```

- Clickable logo redirects to home
- Hover scale effect
- Useful for flow interruption

## Data Stored After Auth

**User Account Created:**
```typescript
{
  userId: string,
  email: string,
  emailVerified: boolean,
  createdAt: Date,
  lastLogin: Date,
  authProvider: "email-otp" | "anonymous",
  sessionToken: string
}
```

## Guest Account Details

**Temporary Profile:**
```typescript
{
  userId: string (temporary),
  email: null,
  isGuest: true,
  createdAt: Date,
  expiresAt: Date (30 days from creation),
  sessionToken: string,
  canConvertToRegular: true
}
```

## Future Features

- [ ] Magic link authentication
- [ ] Social login (Google, GitHub)
- [ ] Biometric authentication (Touch ID, Face ID)
- [ ] SMS OTP backup
- [ ] Account recovery options
- [ ] Two-factor authentication
- [ ] Device fingerprinting
- [ ] Passwordless long-term tokens

## Technical Stack

- **Auth Library:** Convex Auth
- **OTP Generation:** Backend email service
- **Email:** SMTP with vly.ai
- **State:** React useState
- **Forms:** Native HTML forms
- **Validation:** Built-in HTML5
- **Rate Limiting:** Convex backend
- **Session Management:** Secure tokens
