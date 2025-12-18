# Environment Variables & Configuration Guide

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Overview

Complete environment configuration guide for White Cell Protocol. This document covers all required and optional environment variables, their purposes, and how to set them up for local development and production.

---

## Quick Start

Create a `.env.local` file in the project root with the required variables listed below.

---

## Environment Variable Categories

### 1. Convex Backend Configuration

```bash
# REQUIRED - Convex project setup
VITE_CONVEX_URL=https://YOUR_PROJECT.convex.cloud

# Example:
VITE_CONVEX_URL=https://white-cell-protocol.convex.cloud
```

**Purpose:** Connects the frontend to your Convex backend.

**Setup Instructions:**
1. Create a project at convex.dev
2. Run `npx convex auth`
3. Copy the deployment URL from dashboard
4. Set as VITE_CONVEX_URL

**Verification:**
```bash
# Test connection
npx convex query api.public.getAppStatus
```

---

### 2. Authentication Configuration

#### Email OTP Service

```bash
# REQUIRED - Email provider for OTP
VITE_EMAIL_PROVIDER=resend  # Options: resend, sendgrid, mailgun

# Resend Configuration (Recommended)
RESEND_API_KEY=re_xxxxxxxxxxxxx

# SendGrid Configuration (Alternative)
SENDGRID_API_KEY=SG.xxxxxxxxxxxxx
SENDGRID_FROM_EMAIL=noreply@whitecelprotocol.com

# Mailgun Configuration (Alternative)
MAILGUN_API_KEY=key-xxxxxxxxxxxxx
MAILGUN_DOMAIN=mail.whitecelprotocol.com
MAILGUN_FROM_EMAIL=noreply@whitecelprotocol.com
```

**Purpose:** Enables passwordless OTP authentication via email.

**Setup Instructions:**

**For Resend:**
1. Go to resend.com
2. Create account and API key
3. Add domain (optional for production)
4. Set RESEND_API_KEY in environment

**For SendGrid:**
1. Go to sendgrid.com
2. Create account
3. Generate API key in Settings > API Keys
4. Add From Email to settings
5. Set SENDGRID_API_KEY and SENDGRID_FROM_EMAIL

**OTP Email Template:**
```html
Hi {{ userName }},

Your login code is: {{ otpCode }}

This code expires in 10 minutes.

If you didn't request this code, ignore this email.

- White Cell Protocol Team
```

---

#### SMS OTP Service (Optional)

```bash
# SMS OTP Provider
VITE_SMS_PROVIDER=twilio  # Options: twilio, vonage

# Twilio Configuration
TWILIO_ACCOUNT_SID=ACxxxxxxxxxxxxx
TWILIO_AUTH_TOKEN=xxxxxxxxxxxxx
TWILIO_PHONE_NUMBER=+1234567890

# Vonage Configuration (Alternative)
VONAGE_API_KEY=xxxxxxxx
VONAGE_API_SECRET=xxxxxxxxxxxxx
VONAGE_FROM_NUMBER=WhiteCellProtocol
```

**Purpose:** Enables OTP login via SMS (optional feature).

**Setup Instructions:**

**For Twilio:**
1. Go to twilio.com
2. Create account and verify phone
3. Get Account SID and Auth Token from console
4. Purchase or verify phone number for sending
5. Set environment variables

**Testing SMS:**
```bash
# Use test credentials for development
TWILIO_ACCOUNT_SID=ACxxxxx (test SID)
TWILIO_AUTH_TOKEN=xxxxx (test token)
TWILIO_PHONE_NUMBER=+15005550006 (Twilio test number)
```

---

#### OAuth Integration

```bash
# GitHub OAuth
VITE_GITHUB_CLIENT_ID=Ov23lixxx
VITE_GITHUB_CLIENT_SECRET=xxxxxxxxxxxxx
GITHUB_REDIRECT_URI=http://localhost:5173/auth/github/callback

# Spotify OAuth
VITE_SPOTIFY_CLIENT_ID=xxxxxxxxxxxxxx
VITE_SPOTIFY_CLIENT_SECRET=xxxxxxxxxxxxxx
SPOTIFY_REDIRECT_URI=http://localhost:5173/auth/spotify/callback

# Google OAuth (Optional)
VITE_GOOGLE_CLIENT_ID=xxxxx.apps.googleusercontent.com
VITE_GOOGLE_CLIENT_SECRET=GOCSPX-xxxxx
GOOGLE_REDIRECT_URI=http://localhost:5173/auth/google/callback

# Notion Integration (OAuth)
VITE_NOTION_CLIENT_ID=xxxxxxxxxxxxx
VITE_NOTION_CLIENT_SECRET=xxxxxxxxxxxxx
NOTION_REDIRECT_URI=http://localhost:5173/auth/notion/callback
```

**Purpose:** Enables third-party logins and integrations.

**Setup Instructions:**

**GitHub OAuth:**
1. Go to github.com/settings/developers/apps
2. Create New OAuth App
3. Set Authorization callback URL: `http://localhost:5173/auth/github/callback`
4. Copy Client ID and Secret
5. Store in environment variables

**Spotify OAuth:**
1. Go to developer.spotify.com/dashboard
2. Create app
3. Accept terms and create
4. Set Redirect URI: `http://localhost:5173/auth/spotify/callback`
5. Copy Client ID and Secret
6. Whitelist the redirect URI

**Important Note:** For production, update REDIRECT_URIs to your production domain.

---

### 3. AI/LLM Integration

#### Groq LLM API

```bash
# REQUIRED for quiz generation
VITE_GROQ_API_KEY=gsk_xxxxxxxxxxxxx
GROQ_API_BASE=https://api.groq.com/openai/v1

# Model Configuration
GROQ_MODEL=llama-2-70b-chat
GROQ_MAX_TOKENS=2048
GROQ_TEMPERATURE=0.7
```

**Purpose:** Generates AI quizzes for study module.

**Setup Instructions:**
1. Go to console.groq.com
2. Create account
3. Generate API key
4. Set VITE_GROQ_API_KEY
5. Select Llama 2 70B for best results

**Rate Limits:**
- Free: 30 requests/minute, 14,400/day
- Paid: Up to 9000 requests/minute

**Usage Example:**
```typescript
const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${process.env.VITE_GROQ_API_KEY}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    model: 'llama-2-70b-chat',
    messages: [
      { role: 'user', content: 'Generate 5 physics questions' }
    ],
    max_tokens: 2048,
    temperature: 0.7
  })
});
```

---

#### Google Gemini API

```bash
# OPTIONAL for advanced AI features
VITE_GEMINI_API_KEY=AIzaSy_xxxxxxxxxxxxx
GEMINI_MODEL=gemini-1.5-pro
GEMINI_TEMPERATURE=0.9
```

**Purpose:** Advanced AI analysis, fitness insights, debate responses.

**Setup Instructions:**
1. Go to ai.google.dev
2. Create API key
3. Enable Gemini API
4. Set VITE_GEMINI_API_KEY

---

#### Perplexity API (Research Mode)

```bash
# OPTIONAL for research mode in AI chat
VITE_PERPLEXITY_API_KEY=pplx-xxxxxxxxxxxxx
PERPLEXITY_MODEL=pplx-7b-chat
```

**Purpose:** Real-time web search in AI research mode.

**Setup Instructions:**
1. Go to perplexity.ai
2. Sign up for API
3. Generate API key
4. Set VITE_PERPLEXITY_API_KEY

---

### 4. External Service APIs

#### Lichess Chess API

```bash
# REQUIRED for chess puzzles
VITE_LICHESS_API_KEY=xxxxxxxxxxxxxxxx
LICHESS_API_BASE=https://lichess.org/api
LICHESS_PUZZLE_COUNT=5  # Puzzles to fetch per session

# OAuth for user statistics (optional)
LICHESS_CLIENT_ID=xxxxxxxxxxxxx
LICHESS_CLIENT_SECRET=xxxxxxxxxxxxx
LICHESS_REDIRECT_URI=http://localhost:5173/auth/lichess/callback
```

**Purpose:** Fetches chess puzzles and tracks user rating.

**Setup Instructions:**
1. Go to lichess.org/account/security
2. Create Personal API token
3. Set VITE_LICHESS_API_KEY
4. Test with: `curl https://lichess.org/api/puzzle/random -H "Authorization: Bearer YOUR_KEY"`

**Rate Limits:**
- Authenticated: 200 requests/hour
- Puzzle fetch: 1 per 10 seconds recommended

---

#### OpenFoodFacts API (Nutrition)

```bash
# OPTIONAL but recommended for nutrition tracking
VITE_OPENFOODFACTS_API_BASE=https://world.openfoodfacts.org
OPENFOODFACTS_USER_AGENT=WhiteCellProtocol/1.0

# Nutritionix API (Alternative)
VITE_NUTRITIONIX_API_KEY=xxxxxxxxxxxxxxxx
NUTRITIONIX_API_BASE=https://api.nutritionix.com/v1
```

**Purpose:** Food data and nutrition information.

**Setup Instructions:**

**OpenFoodFacts (No API key required):**
1. No authentication needed
2. Can search by barcode or product name
3. Usage: `https://world.openfoodfacts.org/api/v0/product/{barcode}.json`
4. Set USER_AGENT to avoid rate limiting

**Nutritionix:**
1. Go to nutritionix.com/business/api
2. Request API access
3. Generate API key
4. Set VITE_NUTRITIONIX_API_KEY

---

#### Weather API (Optional)

```bash
# For timing suggestions based on weather
VITE_OPENWEATHER_API_KEY=xxxxxxxxxxxxxxxx
OPENWEATHER_API_BASE=https://api.openweathermap.org

# User location (can be auto-detected)
DEFAULT_LATITUDE=40.7128
DEFAULT_LONGITUDE=-74.0060
DEFAULT_TIMEZONE=America/New_York
```

**Purpose:** Weather-based workout and activity suggestions.

**Setup Instructions:**
1. Go to openweathermap.org
2. Sign up
3. Generate API key in API keys section
4. Set VITE_OPENWEATHER_API_KEY

---

### 5. File Storage & Media

```bash
# Convex File Storage (Built-in)
# No additional setup needed - uses Convex infrastructure

# Optional: AWS S3 for backups
AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
AWS_S3_BUCKET=whitecelprotocol-backups
AWS_S3_REGION=us-east-1
```

**Purpose:** Store user PDFs, progress photos, strategy documents.

**Configuration:**
- Primary storage: Convex (free tier included)
- Backup storage: Optional AWS S3
- Max file size: 50MB per file
- Supported formats: PDF, JPEG, PNG, MP4 (for videos)

---

### 6. Analytics & Monitoring

```bash
# Sentry Error Tracking
VITE_SENTRY_DSN=https://xxxxx@sentry.io/12345
SENTRY_ENVIRONMENT=development
SENTRY_RELEASE=1.0.0

# Posthog Analytics
VITE_POSTHOG_API_KEY=phc_xxxxxxxxxxxxx
POSTHOG_API_BASE=https://app.posthog.com

# LogRocket Session Recording (Optional)
VITE_LOGROCKET_ID=your-org/project-name
```

**Purpose:** Error tracking, usage analytics, session debugging.

**Setup Instructions:**

**Sentry:**
1. Go to sentry.io
2. Create project (React)
3. Copy DSN
4. Set VITE_SENTRY_DSN

**PostHog:**
1. Go to posthog.com
2. Create project
3. Copy API key
4. Set VITE_POSTHOG_API_KEY

---

### 7. Email Templates & Communication

```bash
# Email service configuration
EMAIL_FROM_NAME=White Cell Protocol
EMAIL_FROM_ADDRESS=noreply@whitecelprotocol.com

# Notification preferences
ENABLE_EMAIL_NOTIFICATIONS=true
ENABLE_STREAK_REMINDERS=true
ENABLE_ACHIEVEMENT_EMAILS=true
ENABLE_DIGEST_EMAILS=true

# Newsletter
MAILCHIMP_API_KEY=xxxxxxxxxxxxx
MAILCHIMP_AUDIENCE_ID=xxxxxxxxxxxxxx
```

**Purpose:** Configure transactional and marketing emails.

**Email Types:**
- OTP login codes
- Streak warnings (if > 2 days since activity)
- Achievement unlocked
- Weekly digest
- Newsletter (if user opted in)

---

### 8. Feature Flags & Experimental Features

```bash
# Beta features
ENABLE_RESEARCH_MODE=false
ENABLE_MEMORY_PALACE=true
ENABLE_CODE_SANDBOX=true
ENABLE_SPEED_READING=false
ENABLE_STEAM_LIBRARY_INTEGRATION=false

# Gamification
ENABLE_LEADERBOARDS=true
ENABLE_BADGES=true
ENABLE_STREAKS=true
ENABLE_POWER_INDEX=true

# Admin features
ENABLE_ADMIN_PANEL=true
ENABLE_ANALYTICS_DASHBOARD=true
ENABLE_USER_MANAGEMENT=true
```

**Purpose:** Enable/disable features without code changes.

---

### 9. Performance & Caching

```bash
# Redis caching (optional)
REDIS_URL=redis://localhost:6379
REDIS_PASSWORD=xxxxxxxxxxxxx
CACHE_TTL=300  # 5 minutes

# CDN Configuration
CDN_URL=https://cdn.whitecelprotocol.com
ENABLE_CDN=true

# Request timeout
API_TIMEOUT=30000  # 30 seconds
```

**Purpose:** Improve performance and reduce database load.

---

### 10. Development & Debugging

```bash
# Debug mode
DEBUG=true
VITE_DEBUG_MODE=true
LOG_LEVEL=debug  # Options: debug, info, warn, error

# Hot reload
VITE_HMR=true

# Verbose logging
VERBOSE_LOGGING=false
TRACE_QUERIES=false
```

**Purpose:** Development-time debugging and troubleshooting.

---

## Environment Files by Stage

### `.env.local` (Local Development)

```bash
# Copy this template and fill in your values

VITE_CONVEX_URL=https://your-project.convex.cloud

# Authentication
VITE_EMAIL_PROVIDER=resend
RESEND_API_KEY=re_xxxxxxxxxxxxxx

# OAuth
VITE_GITHUB_CLIENT_ID=Ov23li...
VITE_GITHUB_CLIENT_SECRET=...
GITHUB_REDIRECT_URI=http://localhost:5173/auth/github/callback

VITE_SPOTIFY_CLIENT_ID=...
VITE_SPOTIFY_CLIENT_SECRET=...
SPOTIFY_REDIRECT_URI=http://localhost:5173/auth/spotify/callback

# AI APIs
VITE_GROQ_API_KEY=gsk_...

# Chess API
VITE_LICHESS_API_KEY=...

# Debug
DEBUG=true
LOG_LEVEL=debug
```

---

### `.env.staging`

```bash
VITE_CONVEX_URL=https://your-project-staging.convex.cloud

# Use staging API keys
RESEND_API_KEY=re_... (staging key)
VITE_GROQ_API_KEY=... (staging key)

# OAuth (staging URLs)
GITHUB_REDIRECT_URI=https://staging.whitecelprotocol.com/auth/github/callback
SPOTIFY_REDIRECT_URI=https://staging.whitecelprotocol.com/auth/spotify/callback

# Analytics
VITE_SENTRY_DSN=https://...@sentry.io/... (staging project)
SENTRY_ENVIRONMENT=staging

DEBUG=false
LOG_LEVEL=info
```

---

### `.env.production`

```bash
VITE_CONVEX_URL=https://your-project-prod.convex.cloud

# Production API keys (use separate keys from staging)
RESEND_API_KEY=re_... (prod key)
VITE_GROQ_API_KEY=... (prod key)

# OAuth (production URLs)
GITHUB_REDIRECT_URI=https://whitecelprotocol.com/auth/github/callback
SPOTIFY_REDIRECT_URI=https://whitecelprotocol.com/auth/spotify/callback

# Analytics
VITE_SENTRY_DSN=https://...@sentry.io/... (prod project)
SENTRY_ENVIRONMENT=production

# Security
DEBUG=false
LOG_LEVEL=warn
ENABLE_ADMIN_PANEL=false  # Only enable for admins
```

---

## Setup Instructions by Role

### For New Frontend Developer

1. **Clone repository**
   ```bash
   git clone https://github.com/yourorg/white-cell-protocol.git
   cd white-cell-protocol--main
   ```

2. **Copy environment template**
   ```bash
   cp .env.example .env.local
   ```

3. **Fill in required values**
   - Ask team lead for `VITE_CONVEX_URL`
   - Ask team lead for development API keys

4. **Install dependencies**
   ```bash
   pnpm install
   ```

5. **Run development server**
   ```bash
   pnpm run dev
   ```

### For Backend Developer (Convex)

1. **Install Convex CLI**
   ```bash
   npm install -g convex
   ```

2. **Authenticate with Convex**
   ```bash
   npx convex auth
   ```

3. **Create backend functions**
   - Functions go in `src/convex/`
   - Queries, mutations, actions automatically available

4. **Deploy**
   ```bash
   npx convex deploy
   ```

### For DevOps/Deployment

1. **Production deployment**
   - Set all `.env.production` variables in CI/CD
   - Use secret manager (GitHub Secrets, AWS Secrets Manager, etc.)
   - Never commit `.env` files

2. **Environment validation**
   ```bash
   npm run validate:env
   ```

3. **Deploy to production**
   ```bash
   npm run build
   npm run deploy:prod
   ```

---

## Security Best Practices

### DO:
- ✅ Use environment variables for ALL secrets
- ✅ Use separate keys for each environment (dev, staging, prod)
- ✅ Rotate API keys regularly (quarterly minimum)
- ✅ Use least privilege principle (restrict API key permissions)
- ✅ Monitor API key usage for suspicious activity
- ✅ Store secrets in a secure vault (1Password, HashiCorp Vault, etc.)
- ✅ Use Convex managed authentication for user data

### DON'T:
- ❌ Commit `.env` or `.env.local` files to Git
- ❌ Share API keys in chat, email, or code review
- ❌ Use same API keys across environments
- ❌ Log sensitive data (API keys, tokens, passwords)
- ❌ Commit production keys to repository
- ❌ Use generic names for secrets ("key", "password")

### Environment Variable Naming:
- Public variables (frontend): Prefix with `VITE_`
- Server-only variables: No prefix
- All variables: UPPER_SNAKE_CASE
- Group related variables: `SERVICE_API_KEY`, `SERVICE_ENDPOINT`, `SERVICE_TIMEOUT`

---

## Validation & Testing

### Validate Environment Setup

```bash
# Check all required variables are set
npm run validate:env

# Test API connectivity
npm run test:apis

# Test specific API
npm run test:api groq
npm run test:api lichess
npm run test:api github
```

### Environment Variable Validation Script

```typescript
// scripts/validateEnv.ts
const required = [
  'VITE_CONVEX_URL',
  'VITE_GROQ_API_KEY',
  'VITE_GITHUB_CLIENT_ID',
  'RESEND_API_KEY'
];

const missing = required.filter(key => !process.env[key]);

if (missing.length > 0) {
  console.error('Missing required env vars:', missing);
  process.exit(1);
}

console.log('✓ All required environment variables set');
```

---

## Troubleshooting

### Issue: "Cannot connect to Convex"
**Solution:** Verify `VITE_CONVEX_URL` is correct
```bash
npx convex query api.public.getAppStatus
```

### Issue: "GitHub login not working"
**Solution:** Check redirect URI matches exactly
```bash
# Verify in github.com/settings/developers/apps
# Should be: http://localhost:5173/auth/github/callback (dev)
# Or: https://whitecelprotocol.com/auth/github/callback (prod)
```

### Issue: "OTP emails not sending"
**Solution:** Check Resend API key and domain verification
```bash
# Test Resend API
curl -X POST https://api.resend.com/emails \
  -H 'Authorization: Bearer re_YOUR_API_KEY' \
  -d '{"from":"noreply@...","to":"test@...","subject":"Test","html":"..."}'
```

### Issue: "Groq quota exceeded"
**Solution:** Check rate limits and upgrade plan
```bash
# Check usage at console.groq.com
# Consider upgrading or implementing caching
```

---

## Migration Guide

### Rotating API Keys

1. **Generate new key** with service provider
2. **Update staging environment** and test thoroughly
3. **Update production environment**
4. **Test in production** with new key
5. **Revoke old key** after 7 days

### Switching Email Providers

1. **Update VITE_EMAIL_PROVIDER** setting
2. **Update email configuration** variables
3. **Test OTP flow** in staging
4. **Migrate user consent** if applicable
5. **Update email templates** if needed

### Adding New API

1. **Create env variables** following naming convention
2. **Add to `.env.example`**
3. **Add validation** in validation script
4. **Document** in this guide
5. **Update all environments**

---

## Summary Table

| Variable | Environment | Required | Purpose |
|----------|-------------|----------|---------|
| VITE_CONVEX_URL | All | Yes | Backend connection |
| VITE_GROQ_API_KEY | All | Yes | AI quiz generation |
| VITE_EMAIL_PROVIDER | All | Yes | OTP authentication |
| RESEND_API_KEY | All | Yes | Email service |
| VITE_GITHUB_CLIENT_ID | All | Yes | OAuth login |
| VITE_LICHESS_API_KEY | All | Yes | Chess puzzles |
| VITE_GEMINI_API_KEY | All | No | Advanced AI |
| VITE_SENTRY_DSN | All | No | Error tracking |
| DEBUG | Dev/Staging | Yes | Debug mode |
| LOG_LEVEL | All | Yes | Logging level |

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** DevOps & Backend Team
