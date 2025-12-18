# Authentication Flow Documentation

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Overview

Complete documentation of White Cell Protocol's passwordless authentication system. This document covers all authentication flows, security measures, session management, and implementation details.

---

## Table of Contents

1. [Authentication Overview](#authentication-overview)
2. [OTP Email Flow](#otp-email-flow)
3. [OAuth Integration](#oauth-integration)
4. [Session Management](#session-management)
5. [Security Measures](#security-measures)
6. [Error Handling](#error-handling)
7. [Implementation Guide](#implementation-guide)
8. [Testing Guide](#testing-guide)

---

## Authentication Overview

### Supported Authentication Methods

1. **Passwordless OTP (Email)** - Primary method
2. **OAuth (GitHub, Spotify, Google, Notion)** - Third-party login
3. **Guest Login** - Limited access without account
4. **Session-based tokens** - Token management

### Security Standards

- OWASP guidelines compliance
- Rate limiting on all endpoints
- HTTPS/TLS encryption required
- Secure session cookies (HttpOnly, Secure, SameSite)
- Automatic session expiration
- CSRF protection

---

## OTP Email Flow

### Complete Flow Sequence

```
┌─────────────────────────────────────────────────────────────────┐
│ User visits White Cell Protocol                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ Landing Page - Two Options:                                     │
│ [1] "Send me a link" (Email OTP)                               │
│ [2] "Continue with GitHub/Spotify" (OAuth)                      │
│ [3] "Continue as Guest" (Guest)                                │
└─────────────────────────────────────────────────────────────────┘
                              │
                    ┌─────────┼─────────┬────────────┐
                    │         │         │            │
                    ▼         ▼         ▼            ▼
         ┌──────────────┐ ┌─────────┐ ┌──────┐ ┌────────────┐
         │ OTP (Email)  │ │ OAuth   │ │Guest │ │ Guest Login│
         │   Flow       │ │ Flow    │ │Login │ │   Step 1   │
         └──────────────┘ └─────────┘ └──────┘ └────────────┘
```

### Step 1: Request OTP

**User Action:** Enters email and clicks "Send me a link"

**API Call:**
```typescript
POST /api/auth/request-otp
Content-Type: application/json

{
  "email": "user@example.com"
}
```

**Backend Process:**

```typescript
// src/convex/auth.ts
export const requestOTP = mutation({
  args: { email: v.string() },
  handler: async (ctx, { email }) => {
    // 1. Validate email format
    if (!isValidEmail(email)) {
      throw new Error('InvalidEmail');
    }

    // 2. Check if user exists
    let user = await ctx.db
      .query("users")
      .filter(u => u.email === email)
      .first();

    // 3. Generate 6-digit OTP
    const otp = generateOTP(); // Random 6 digits
    const expiresAt = new Date(Date.now() + 10 * 60 * 1000); // 10 min

    // 4. Store OTP in database
    await ctx.db.insert("otp_tokens", {
      email,
      token: otp,
      expiresAt,
      attempts: 0,
      maxAttempts: 3,
      isUsed: false,
      createdAt: new Date()
    });

    // 5. Send email with OTP
    await ctx.runAction(api.actions.email.sendOTP, {
      email,
      otp,
      expiresAt
    });

    // 6. Return success
    return {
      success: true,
      message: "OTP sent to email",
      maskedEmail: maskEmail(email) // e.g., u***@example.com
    };
  }
});
```

**Email Template:**

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto; }
      .container { max-width: 500px; margin: 0 auto; padding: 20px; }
      .code { font-size: 32px; font-weight: bold; letter-spacing: 4px; color: #0066cc; }
      .expiry { color: #666; font-size: 12px; margin-top: 10px; }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>Your White Cell Protocol Login Code</h2>
      <p>Enter this code to log in to your account:</p>
      <div class="code">{{ OTP_CODE }}</div>
      <p class="expiry">This code expires in 10 minutes.</p>
      <p>If you didn't request this code, you can ignore this email.</p>
      <p>- White Cell Protocol Team</p>
    </div>
  </body>
</html>
```

**Response:**

```json
{
  "success": true,
  "message": "OTP sent to email",
  "maskedEmail": "u***@example.com",
  "expiresIn": 600 // seconds
}
```

### Step 2: User Receives Email

**Email arrives** with 6-digit OTP code

**Typical delivery time:** < 1 second

### Step 3: Enter OTP

**User Action:** Enters 6-digit code in input field

```typescript
// Frontend Component
const [otp, setOtp] = useState('');
const [email, setEmail] = useState('');

const handleSubmitOTP = async () => {
  // Validate OTP format
  if (!/^\d{6}$/.test(otp)) {
    showError('OTP must be 6 digits');
    return;
  }

  try {
    const result = await convex.mutation(
      api.auth.verifyOTP,
      { email, otp }
    );

    // Store session
    localStorage.setItem('authToken', result.token);
    
    // Redirect to dashboard
    navigate('/dashboard');
  } catch (error) {
    showError(error.message);
  }
};
```

### Step 4: Verify OTP

**API Call:**
```typescript
POST /api/auth/verify-otp
Content-Type: application/json

{
  "email": "user@example.com",
  "otp": "123456"
}
```

**Backend Verification:**

```typescript
export const verifyOTP = mutation({
  args: {
    email: v.string(),
    otp: v.string()
  },
  handler: async (ctx, { email, otp }) => {
    // 1. Validate input
    if (!isValidEmail(email) || !/^\d{6}$/.test(otp)) {
      throw new Error('InvalidInput');
    }

    // 2. Find OTP token
    const otpRecord = await ctx.db
      .query("otp_tokens")
      .filter(t => t.email === email && !t.isUsed)
      .order("_creationTime", "desc")
      .first();

    if (!otpRecord) {
      throw new Error('OTPNotFound');
    }

    // 3. Check if expired
    if (otpRecord.expiresAt < new Date()) {
      throw new Error('OTPExpired');
    }

    // 4. Check if max attempts exceeded
    if (otpRecord.attempts >= otpRecord.maxAttempts) {
      throw new Error('TooManyAttempts');
    }

    // 5. Verify OTP matches
    if (otpRecord.token !== otp) {
      // Increment attempts
      await ctx.db.patch(otpRecord._id, {
        attempts: otpRecord.attempts + 1
      });

      throw new Error('InvalidOTP');
    }

    // 6. Mark OTP as used
    await ctx.db.patch(otpRecord._id, {
      isUsed: true
    });

    // 7. Find or create user
    let user = await ctx.db
      .query("users")
      .filter(u => u.email === email)
      .first();

    if (!user) {
      user = await ctx.db.insert("users", {
        email,
        name: email.split('@')[0], // Default name
        username: generateUsername(email),
        role: "user",
        subscription: "free",
        createdAt: new Date(),
        updatedAt: new Date(),
        lastLogin: new Date(),
        isActive: true,
        isBanned: false,
        totalXP: 0,
        currentLevel: 1,
        currentStreak: 0,
        longestStreak: 0
      });
    } else {
      // Update last login
      await ctx.db.patch(user._id, {
        lastLogin: new Date()
      });
    }

    // 8. Generate session token
    const token = generateJWT({
      userId: user._id,
      email: user.email,
      role: user.role
    });

    // 9. Create session record
    await ctx.db.insert("sessions", {
      userId: user._id,
      token,
      expiresAt: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000), // 30 days
      type: "otp",
      createdAt: new Date(),
      isValid: true
    });

    return {
      success: true,
      token,
      user: {
        _id: user._id,
        email: user.email,
        name: user.name,
        role: user.role
      },
      expiresIn: 30 * 24 * 60 * 60 // 30 days in seconds
    };
  }
});
```

### Step 5: Session Created

**Response:**

```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "_id": "user_123",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "user"
  },
  "expiresIn": 2592000
}
```

**Frontend:**
```typescript
// Store token
localStorage.setItem('authToken', token);

// Set auth context
setAuthUser(user);

// Redirect to dashboard
navigate('/dashboard');
```

### Step 6: Access Granted

User is now logged in and can access all authenticated features.

---

## OAuth Integration

### GitHub OAuth Flow

```
┌────────────────────────────────────────────────────┐
│ User clicks "Continue with GitHub"                 │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ Redirect to GitHub OAuth Endpoint                  │
│ https://github.com/login/oauth/authorize?        │
│   client_id=...&redirect_uri=...&scope=user:email │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ User grants permission to White Cell Protocol      │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ GitHub redirects to callback with authorization    │
│ code parameter                                      │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ Backend exchanges code for access token            │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ Fetch user profile from GitHub API                 │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ Create or link user account                        │
│ Generate session token                             │
└────────────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────┐
│ Redirect to dashboard with token                   │
└────────────────────────────────────────────────────┘
```

### Implementation

**Step 1: Initiate OAuth**

```typescript
// Frontend Component
const handleGitHubLogin = () => {
  const clientId = import.meta.env.VITE_GITHUB_CLIENT_ID;
  const redirectUri = `${window.location.origin}/auth/github/callback`;
  const scope = 'user:email';
  
  const githubAuthUrl = `https://github.com/login/oauth/authorize?` +
    `client_id=${clientId}&` +
    `redirect_uri=${encodeURIComponent(redirectUri)}&` +
    `scope=${scope}`;
  
  window.location.href = githubAuthUrl;
};
```

**Step 2: Callback Handler**

```typescript
// Frontend: pages/auth/github/callback.tsx
export function GitHubCallback() {
  const navigate = useNavigate();

  useEffect(() => {
    const code = new URLSearchParams(window.location.search).get('code');
    
    if (!code) {
      navigate('/login?error=NoAuthCode');
      return;
    }

    // Send code to backend
    convex.mutation(api.auth.handleGitHubCallback, { code })
      .then((result) => {
        // Store token
        localStorage.setItem('authToken', result.token);
        // Redirect
        navigate('/dashboard');
      })
      .catch((error) => {
        navigate(`/login?error=${error.message}`);
      });
  }, []);

  return <LoadingScreen message="Logging in with GitHub..." />;
}
```

**Step 3: Backend Exchange**

```typescript
// Backend
export const handleGitHubCallback = mutation({
  args: { code: v.string() },
  handler: async (ctx, { code }) => {
    // 1. Exchange code for access token
    const tokenResponse = await fetch(
      'https://github.com/login/oauth/access_token',
      {
        method: 'POST',
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          client_id: process.env.VITE_GITHUB_CLIENT_ID,
          client_secret: process.env.VITE_GITHUB_CLIENT_SECRET,
          code
        })
      }
    );

    const { access_token } = await tokenResponse.json();

    // 2. Fetch user profile
    const userResponse = await fetch('https://api.github.com/user', {
      headers: {
        'Authorization': `Bearer ${access_token}`
      }
    });

    const githubUser = await userResponse.json();

    // 3. Fetch user email
    const emailResponse = await fetch('https://api.github.com/user/emails', {
      headers: {
        'Authorization': `Bearer ${access_token}`
      }
    });

    const emails = await emailResponse.json();
    const primaryEmail = emails.find(e => e.primary)?.email || emails[0].email;

    // 4. Find or create user
    let user = await ctx.db
      .query("users")
      .filter(u => u.email === primaryEmail)
      .first();

    if (!user) {
      user = await ctx.db.insert("users", {
        email: primaryEmail,
        name: githubUser.name || githubUser.login,
        username: githubUser.login,
        avatar: githubUser.avatar_url,
        role: "user",
        subscription: "free",
        createdAt: new Date(),
        updatedAt: new Date(),
        lastLogin: new Date(),
        isActive: true,
        isBanned: false,
        totalXP: 0,
        currentLevel: 1,
        currentStreak: 0,
        longestStreak: 0
      });
    } else {
      // Update avatar if available
      if (githubUser.avatar_url) {
        await ctx.db.patch(user._id, {
          avatar: githubUser.avatar_url,
          lastLogin: new Date()
        });
      }
    }

    // 5. Generate session token
    const token = generateJWT({
      userId: user._id,
      email: user.email,
      role: user.role
    });

    // 6. Create session
    await ctx.db.insert("sessions", {
      userId: user._id,
      token,
      expiresAt: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000),
      type: "oauth",
      createdAt: new Date(),
      isValid: true
    });

    return {
      success: true,
      token,
      user: {
        _id: user._id,
        email: user.email,
        name: user.name,
        role: user.role
      }
    };
  }
});
```

### Spotify OAuth

Similar flow, but:
- Authorization URL: `https://accounts.spotify.com/authorize`
- Scopes: `user-read-private user-read-email`
- Token endpoint: `https://accounts.spotify.com/api/token`

### Google OAuth

Similar flow with Google's OAuth 2.0 endpoints.

---

## Guest Login

### Guest Flow

```
User clicks "Continue as Guest"
        │
        ▼
Generate unique guest ID
Create guest session (24-hour expiry)
        │
        ▼
Limited access:
  - Can view content
  - Cannot create notes/workouts
  - Cannot sync data
        │
        ▼
Prompt to create account
```

**Implementation:**

```typescript
export const loginAsGuest = mutation({
  handler: async (ctx) => {
    const guestId = `guest_${nanoid()}`;
    const expiresAt = new Date(Date.now() + 24 * 60 * 60 * 1000); // 24 hours

    const token = generateJWT({
      userId: guestId,
      role: "guest",
      isGuest: true
    });

    await ctx.db.insert("sessions", {
      userId: guestId,
      token,
      expiresAt,
      type: "guest",
      createdAt: new Date(),
      isValid: true
    });

    return { token, expiresIn: 24 * 60 * 60 };
  }
});
```

---

## Session Management

### Token Structure

```typescript
// JWT Token payload
{
  aud: "white-cell-protocol",
  iss: "white-cell-protocol",
  sub: "user_123",                    // User ID
  email: "user@example.com",
  role: "user",                       // "user" | "creator" | "admin"
  isGuest: false,
  iat: 1702900800,                    // Issued at
  exp: 1705492800,                    // Expires (30 days)
  jti: "session_xxx"                  // JWT ID (unique per session)
}
```

### Token Storage

**Frontend:**
```typescript
// Store in localStorage for persistence
localStorage.setItem('authToken', token);

// Use in API requests
const headers = {
  'Authorization': `Bearer ${localStorage.getItem('authToken')}`
};
```

**Secure Cookie Alternative:**
```typescript
// Backend sets secure cookie
response.setHeader('Set-Cookie', [
  `authToken=${token}; `,
  `HttpOnly; `,
  `Secure; `,
  `SameSite=Lax; `,
  `Max-Age=${30 * 24 * 60 * 60}`
].join(''));
```

### Refresh Token Flow (Optional)

If implementing refresh tokens:

```typescript
// 1. Short-lived access token (15 min)
const accessToken = generateJWT({ ...payload }, { expiresIn: '15m' });

// 2. Long-lived refresh token (30 days)
const refreshToken = generateJWT({ ...payload }, { expiresIn: '30d' });

// 3. Store refresh token in secure database
await ctx.db.insert("refresh_tokens", {
  userId,
  token: hash(refreshToken),
  expiresAt: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000),
  isRevoked: false,
  createdAt: new Date()
});

// 4. When access token expires, use refresh token
export const refreshAccessToken = mutation({
  args: { refreshToken: v.string() },
  handler: async (ctx, { refreshToken }) => {
    const decoded = verifyJWT(refreshToken);
    
    const stored = await ctx.db
      .query("refresh_tokens")
      .filter(t => t.userId === decoded.sub && !t.isRevoked)
      .first();

    if (!stored || stored.expiresAt < new Date()) {
      throw new Error('RefreshTokenExpired');
    }

    const newAccessToken = generateJWT({ ...payload }, { expiresIn: '15m' });
    return { accessToken: newAccessToken };
  }
});
```

### Logout

```typescript
// Frontend
const handleLogout = () => {
  // Clear storage
  localStorage.removeItem('authToken');
  
  // Revoke session on backend (optional)
  convex.mutation(api.auth.logout, {});
  
  // Clear auth context
  setAuthUser(null);
  
  // Redirect to login
  navigate('/login');
};

// Backend
export const logout = mutation({
  handler: async (ctx) => {
    const userId = ctx.userId;
    
    // Invalidate all sessions for this user
    const sessions = await ctx.db
      .query("sessions")
      .filter(s => s.userId === userId && s.isValid)
      .collect();

    for (const session of sessions) {
      await ctx.db.patch(session._id, { isValid: false });
    }

    return { success: true };
  }
});
```

---

## Security Measures

### Rate Limiting

```typescript
// Rate limit OTP requests
const OTP_RATE_LIMIT = {
  perEmail: 3,          // 3 OTPs per email per hour
  window: 60 * 60 * 1000 // 1 hour
};

const recentOTPs = await ctx.db
  .query("otp_tokens")
  .filter(t => 
    t.email === email && 
    t.createdAt > new Date(Date.now() - OTP_RATE_LIMIT.window)
  )
  .collect();

if (recentOTPs.length >= OTP_RATE_LIMIT.perEmail) {
  throw new Error('TooManyOTPRequests');
}
```

### CSRF Protection

```typescript
// Generate CSRF token
const csrfToken = crypto.randomBytes(32).toString('hex');

// Store in session
await ctx.db.patch(sessionId, { csrfToken });

// Validate on state-changing requests
const validateCSRF = (token, sessionToken) => {
  const session = verifyToken(sessionToken);
  return cryptoTimingSafeEqual(token, session.csrfToken);
};
```

### Email Verification

```typescript
// Verify email ownership before using in account
export const verifyEmailOwnership = mutation({
  args: {
    token: v.string(),
    newEmail: v.string()
  },
  handler: async (ctx, { token, newEmail }) => {
    // 1. Verify user is authenticated
    const userId = ctx.userId;
    
    // 2. Check verification token
    const verification = await ctx.db
      .query("email_verifications")
      .filter(v => v.token === token && !v.isUsed)
      .first();

    if (!verification) {
      throw new Error('InvalidVerificationToken');
    }

    // 3. Update email
    await ctx.db.patch(userId, { email: newEmail });

    // 4. Mark token as used
    await ctx.db.patch(verification._id, { isUsed: true });

    return { success: true };
  }
});
```

### Password Reset Security

Even though passwordless, if implementing additional security:

```typescript
// Generate secure reset token
const resetToken = crypto.randomBytes(32).toString('hex');
const resetTokenHash = sha256(resetToken);

// Store with expiration
await ctx.db.insert("password_resets", {
  userId,
  tokenHash: resetTokenHash,
  expiresAt: new Date(Date.now() + 60 * 60 * 1000), // 1 hour
  isUsed: false,
  createdAt: new Date()
});

// Send reset link
await sendEmail({
  to: user.email,
  subject: "Reset Your Account",
  html: `<a href="https://app.com/reset?token=${resetToken}">Reset Link</a>`
});
```

### Session Security

```typescript
// Set secure cookie flags
const cookieOptions = {
  httpOnly: true,        // No JS access
  secure: true,          // HTTPS only
  sameSite: 'Lax',       // CSRF protection
  maxAge: 30 * 24 * 60 * 60 * 1000, // 30 days
  path: '/',
  domain: 'whitecelprotocol.com'
};

// Validate session on each request
export const validateSession = async (token) => {
  try {
    const decoded = verifyJWT(token);
    
    const session = await db
      .query("sessions")
      .filter(s => s.token === token && s.isValid)
      .first();

    if (!session || session.expiresAt < new Date()) {
      return null;
    }

    return decoded;
  } catch (error) {
    return null;
  }
};
```

---

## Error Handling

### Authentication Error Codes

| Code | Description | User Message |
|------|-------------|--------------|
| `InvalidEmail` | Email format invalid | "Please enter a valid email" |
| `OTPNotFound` | OTP doesn't exist | "Code expired or incorrect" |
| `OTPExpired` | OTP past expiration | "Code has expired. Request a new one" |
| `InvalidOTP` | Wrong OTP entered | "Incorrect code. Try again" |
| `TooManyAttempts` | Max attempts exceeded | "Too many attempts. Request a new code" |
| `TooManyOTPRequests` | Rate limited | "Too many requests. Try again later" |
| `InvalidToken` | Session token invalid | "Your session expired. Please login again" |
| `TokenExpired` | Session token expired | "Your session expired. Please login again" |
| `Unauthorized` | User not authenticated | "Please login to continue" |
| `Forbidden` | Insufficient permissions | "You don't have permission" |

### Error Responses

```json
{
  "error": {
    "code": "InvalidOTP",
    "message": "Incorrect code. Try again",
    "timestamp": "2024-01-01T12:00:00Z",
    "details": {
      "attemptsRemaining": 2
    }
  }
}
```

---

## Implementation Guide

### Frontend Setup

```typescript
// src/hooks/useAuth.ts
export function useAuth() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [token, setToken] = useState(null);

  useEffect(() => {
    // Check for existing token on mount
    const storedToken = localStorage.getItem('authToken');
    if (storedToken) {
      validateToken(storedToken)
        .then(user => setUser(user))
        .catch(() => localStorage.removeItem('authToken'))
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);

  const login = async (email, otp) => {
    const result = await convex.mutation(api.auth.verifyOTP, { email, otp });
    localStorage.setItem('authToken', result.token);
    setUser(result.user);
    setToken(result.token);
  };

  const logout = async () => {
    await convex.mutation(api.auth.logout, {});
    localStorage.removeItem('authToken');
    setUser(null);
    setToken(null);
  };

  return { user, loading, token, login, logout };
}
```

### Protected Routes

```typescript
// src/components/ProtectedRoute.tsx
function ProtectedRoute({ children }) {
  const { user, loading } = useAuth();
  const navigate = useNavigate();

  if (loading) {
    return <LoadingScreen />;
  }

  if (!user) {
    navigate('/login');
    return null;
  }

  return children;
}
```

---

## Testing Guide

### Unit Tests

```typescript
// tests/auth.test.ts
describe('Authentication', () => {
  describe('OTP Flow', () => {
    test('generates and sends OTP', async () => {
      const email = 'test@example.com';
      const result = await mutation(api.auth.requestOTP, { email });
      
      expect(result.success).toBe(true);
      expect(result.maskedEmail).toBe('t***@example.com');
    });

    test('verifies correct OTP', async () => {
      const email = 'test@example.com';
      
      // Request OTP
      await mutation(api.auth.requestOTP, { email });
      
      // Get OTP from database
      const otp = '123456'; // In test, mock or use test OTP
      
      // Verify
      const result = await mutation(api.auth.verifyOTP, { email, otp });
      
      expect(result.success).toBe(true);
      expect(result.token).toBeDefined();
      expect(result.user).toBeDefined();
    });

    test('rejects expired OTP', async () => {
      // Test that expired OTPs are rejected
    });

    test('enforces rate limiting', async () => {
      // Test that too many OTP requests are rejected
    });
  });

  describe('OAuth Flow', () => {
    test('handles GitHub callback', async () => {
      // Test GitHub OAuth callback
    });

    test('creates user on first OAuth login', async () => {
      // Test new user creation
    });

    test('links existing user to OAuth', async () => {
      // Test existing user linking
    });
  });

  describe('Session Management', () => {
    test('creates session on login', async () => {
      // Test session creation
    });

    test('invalidates session on logout', async () => {
      // Test session invalidation
    });

    test('expires old sessions', async () => {
      // Test session expiration
    });
  });
});
```

### Integration Tests

```typescript
// tests/auth-flow.integration.ts
describe('Full Authentication Flows', () => {
  test('complete OTP login flow', async () => {
    // 1. Request OTP
    // 2. Verify OTP
    // 3. Create session
    // 4. Access protected resource
    // 5. Logout
  });

  test('complete OAuth login flow', async () => {
    // 1. Initiate OAuth
    // 2. Simulate GitHub callback
    // 3. Verify user created
    // 4. Verify session created
  });

  test('guest login with account upgrade', async () => {
    // 1. Login as guest
    // 2. Browse content
    // 3. Upgrade to full account
    // 4. Migrate data
  });
});
```

### Manual Testing

```
OTP Flow Testing:
1. [ ] Valid email, correct OTP
2. [ ] Invalid email format
3. [ ] Expired OTP (wait 10+ minutes)
4. [ ] Wrong OTP (test max attempts)
5. [ ] Rate limit (request 4+ OTPs quickly)
6. [ ] Email delivery (check inbox)

OAuth Testing:
1. [ ] GitHub login
2. [ ] Spotify login
3. [ ] Google login
4. [ ] Multiple OAuth accounts same email
5. [ ] OAuth revoke flow

Session Testing:
1. [ ] Token stored in localStorage
2. [ ] Token sent in API requests
3. [ ] Token validates correctly
4. [ ] Logout clears token
5. [ ] Expired session redirects to login
6. [ ] Session timeout (30 days)

Security Testing:
1. [ ] CSRF tokens validated
2. [ ] Rate limiting enforced
3. [ ] Secure cookies set (if used)
4. [ ] HTTPS required in production
5. [ ] Email verification working
6. [ ] Password reset flow (if applicable)
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Security & Auth Team
