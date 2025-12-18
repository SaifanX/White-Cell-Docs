# Setup & Deployment Guide

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Local Development Setup](#local-development-setup)
2. [Development Server](#development-server)
3. [Production Build](#production-build)
4. [Deployment Platforms](#deployment-platforms)
5. [Database Setup](#database-setup)
6. [CI/CD Pipeline](#cicd-pipeline)
7. [Docker Setup](#docker-setup)
8. [Monitoring & Debugging](#monitoring--debugging)
9. [Troubleshooting](#troubleshooting)
10. [Performance Optimization](#performance-optimization)

---

## Local Development Setup

### Prerequisites

**System Requirements:**
- Node.js: 18.0.0 or higher
- pnpm: 8.0.0 or higher (or npm/yarn)
- Git: Latest version
- 2GB RAM minimum
- 5GB disk space

**Verify Installation:**
```bash
node --version    # Should be v18+
pnpm --version    # Should be 8+
git --version     # Should show version
```

### Step 1: Clone Repository

```bash
# Clone the repository
git clone https://github.com/yourorg/white-cell-protocol.git

# Navigate to project
cd white-cell-protocol--main

# Verify directory structure
ls -la
# Output should show: src/, convex/, docs/, package.json, etc.
```

### Step 2: Install Dependencies

```bash
# Using pnpm (recommended)
pnpm install

# Or using npm
npm install

# Verify installation
pnpm list | head -20
```

**What gets installed:**
- React 18 & dependencies
- TypeScript & type definitions
- Convex SDK
- Tailwind CSS
- shadcn/ui components
- Development tools (Vite, ESLint, etc.)

### Step 3: Environment Configuration

```bash
# Copy environment template
cp .env.example .env.local

# Edit with your API keys
nano .env.local  # or code .env.local
```

**Minimum required for local development:**
```bash
VITE_CONVEX_URL=https://your-project.convex.cloud
VITE_GROQ_API_KEY=gsk_xxxxxxxxxxxxx
VITE_EMAIL_PROVIDER=resend
RESEND_API_KEY=re_xxxxxxxxxxxxx
VITE_GITHUB_CLIENT_ID=Ov23li...
VITE_GITHUB_CLIENT_SECRET=...
DEBUG=true
LOG_LEVEL=debug
```

For detailed environment setup, see [ENVIRONMENT_VARIABLES.md](./ENVIRONMENT_VARIABLES.md).

### Step 4: Database Setup (Convex)

```bash
# Authenticate with Convex
npx convex auth

# This opens browser to:
# https://dashboard.convex.dev/t/login

# After authentication, a .env.local is auto-updated with:
CONVEX_DEPLOYMENT=...
```

**Initialize Convex backend:**
```bash
# Deploy functions to development database
npx convex dev

# This command:
# 1. Starts local Convex dev environment
# 2. Watches for function changes
# 3. Hot-reloads backend
# 4. Shows connection info

# Keep this terminal open while developing
```

### Step 5: Start Development Server

```bash
# In a new terminal (keep convex dev running in another)
pnpm run dev

# Expected output:
#
#   VITE v4.x.x  local
#
#   ➜  Local:   http://localhost:5173/
#   ➜  press h to show help
```

**Open in browser:**
- Local: http://localhost:5173/
- You should see the White Cell Protocol landing page

### Verification Checklist

```bash
✓ Node.js v18+
✓ pnpm v8+
✓ Repository cloned
✓ Dependencies installed
✓ .env.local configured
✓ Convex authenticated
✓ npx convex dev running
✓ pnpm run dev running
✓ Browser shows http://localhost:5173/ ✓
```

---

## Development Server

### Running the App

**Start all services:**

```bash
# Terminal 1: Convex Backend
npx convex dev

# Terminal 2: Frontend Dev Server
pnpm run dev

# Terminal 3 (optional): TypeScript Compiler in Watch Mode
pnpm run type-check --watch
```

### Key Development Commands

```bash
# Start development server
pnpm run dev

# Check for TypeScript errors
pnpm run type-check

# Run linter (ESLint)
pnpm run lint

# Fix linting issues automatically
pnpm run lint:fix

# Format code (Prettier)
pnpm run format

# Run tests (if configured)
pnpm run test

# Build for production
pnpm run build

# Preview production build locally
pnpm run preview
```

### Hot Module Replacement (HMR)

The dev server supports hot reloading:

```typescript
// Changes in src/components/Button.tsx
// Will auto-refresh without page reload
// State is preserved (if configured)
```

**Configure HMR:**
```typescript
// vite.config.ts
export default defineConfig({
  server: {
    hmr: {
      protocol: 'ws',
      host: 'localhost',
      port: 5173
    }
  }
});
```

### Debug Mode

Enable verbose logging:

```bash
# Set debug environment
DEBUG=true pnpm run dev

# Or add to .env.local:
DEBUG=true
LOG_LEVEL=debug
TRACE_QUERIES=true
```

**Debug output includes:**
- All API calls
- Convex query/mutation logs
- Component render timings
- State changes

---

## Production Build

### Build Process

```bash
# Create optimized production build
pnpm run build

# Expected output:
#
#   ✓ 1234 modules transformed.
#   dist/index.html                    0.89 kB
#   dist/assets/main.xxxxx.js        245.67 kB → gzipped: 75.32 kB
#   dist/assets/main.xxxxx.css        45.23 kB → gzipped: 12.45 kB
#
# ✓ build completed in 25.34s
```

### Build Output

```
dist/
├── index.html              # Entry point
├── assets/
│   ├── index.xxxxx.js      # Main bundle
│   ├── index.xxxxx.css     # Compiled styles
│   └── vendor.xxxxx.js     # Vendor code (React, etc.)
└── public/                 # Copied static files
    ├── manifest.json
    ├── robots.txt
    ├── sitemap.xml
    └── assets/
```

### Build Optimization

**Current build size:**
```
Main JS:  ~75 KB (gzipped)
CSS:      ~12 KB (gzipped)
Total:    ~87 KB (gzipped)
```

**Bundle analysis:**
```bash
# Analyze bundle composition
pnpm run build --analyze

# This generates a visualization of bundle size
```

### Production Environment Variables

```bash
# Update .env.production with prod values
VITE_CONVEX_URL=https://your-project-prod.convex.cloud
NODE_ENV=production
DEBUG=false
LOG_LEVEL=warn
```

### Preview Production Build

```bash
# Build and preview locally
pnpm run build
pnpm run preview

# Open http://localhost:4173/
# Uses production build settings
```

---

## Deployment Platforms

### Option 1: Vercel (Recommended)

**Why Vercel?**
- Automatic deployment from Git
- Free tier available
- Built-in optimization
- Global CDN
- Easy environment variables

**Setup Steps:**

1. **Create Vercel Account**
   - Go to vercel.com
   - Sign in with GitHub

2. **Import Project**
   ```bash
   # In Vercel dashboard: Import Project
   # Select: white-cell-protocol repository
   ```

3. **Configure Project**
   - Framework: Vite
   - Build Command: `pnpm run build`
   - Output Directory: `dist`
   - Install Command: `pnpm install`

4. **Add Environment Variables**
   ```
   In Vercel Dashboard > Settings > Environment Variables
   Add all variables from .env.production
   ```

5. **Deploy**
   ```bash
   # Automatically triggers on push to main
   # Or manually in Vercel dashboard
   ```

6. **Verify Deployment**
   - Vercel provides URL: `https://your-project-xxxxx.vercel.app`
   - Check deployment logs for errors

**Vercel Configuration File (optional):**

```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "buildCommand": "pnpm run build",
        "outputDirectory": "dist"
      }
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "destination": "/index.html",
      "status": 200
    }
  ]
}
```

---

### Option 2: Netlify

**Setup Steps:**

1. **Connect Repository**
   - Go to netlify.com
   - Click "New site from Git"
   - Connect GitHub account
   - Select repository

2. **Configure Build**
   - Build Command: `pnpm run build`
   - Publish Directory: `dist`
   - Node Version: 18

3. **Environment Variables**
   ```
   Site Settings > Build & Deploy > Environment
   Add all .env.production variables
   ```

4. **Deploy**
   ```bash
   # Automatic on push to main
   # Or trigger manually in Netlify dashboard
   ```

**Netlify Configuration:**

```toml
# netlify.toml
[build]
  command = "pnpm run build"
  publish = "dist"
  node_version = "18"

[build.environment]
  NODE_ENV = "production"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

---

### Option 3: AWS Amplify

**Setup Steps:**

1. **Create Amplify App**
   ```bash
   amplify init
   # Follow prompts to setup AWS account
   ```

2. **Configure Build**
   ```bash
   amplify add hosting
   # Select: Hosting with Amplify Console
   # Or: Manual deployment with AWS S3
   ```

3. **Deploy**
   ```bash
   # Build locally
   pnpm run build

   # Deploy to S3
   amplify publish

   # Or manually upload dist/ to S3
   aws s3 sync dist/ s3://your-bucket-name
   ```

---

### Option 4: Traditional VPS/Server

**Requirements:**
- Node.js 18+ installed
- Nginx or Apache as reverse proxy
- SSL certificate (Let's Encrypt)
- PM2 for process management

**Setup:**

1. **SSH into Server**
   ```bash
   ssh user@your-server.com
   ```

2. **Install Dependencies**
   ```bash
   # Update system
   sudo apt update && sudo apt upgrade

   # Install Node.js
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs

   # Install pnpm
   npm install -g pnpm

   # Install PM2
   npm install -g pm2
   ```

3. **Clone Repository**
   ```bash
   git clone https://github.com/yourorg/white-cell-protocol.git
   cd white-cell-protocol--main
   ```

4. **Install & Build**
   ```bash
   pnpm install
   pnpm run build
   ```

5. **Configure PM2**
   ```bash
   # Create ecosystem.config.js
   cat > ecosystem.config.js << 'EOF'
   module.exports = {
     apps: [{
       name: 'white-cell-protocol',
       script: './dist/index.html',
       instances: 'max',
       exec_mode: 'cluster',
       env: {
         NODE_ENV: 'production'
       }
     }]
   };
   EOF

   # Start app
   pm2 start ecosystem.config.js
   pm2 save
   ```

6. **Configure Nginx**
   ```nginx
   # /etc/nginx/sites-available/white-cell-protocol
   server {
     listen 80;
     server_name whitecelprotocol.com;
     
     # Redirect HTTP to HTTPS
     return 301 https://$server_name$request_uri;
   }

   server {
     listen 443 ssl http2;
     server_name whitecelprotocol.com;
     
     ssl_certificate /etc/letsencrypt/live/whitecelprotocol.com/fullchain.pem;
     ssl_certificate_key /etc/letsencrypt/live/whitecelprotocol.com/privkey.pem;
     
     root /var/www/white-cell-protocol/dist;
     
     location / {
       try_files $uri $uri/ /index.html;
     }
     
     location ~* ^/api/ {
       proxy_pass https://your-project.convex.cloud;
     }
   }
   ```

7. **Enable SSL**
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot certonly --nginx -d whitecelprotocol.com
   ```

---

## Database Setup

### Convex Database Configuration

**Development Database:**
```bash
# Automatically created when running
npx convex dev

# Database URL auto-added to .env.local:
CONVEX_DEPLOYMENT=dev:your-project
```

**Production Database:**
```bash
# Deploy to production
npx convex deploy

# Select environment: production
# Database automatically created and configured
```

### Schema Migration

**Initial Setup:**
```bash
# Your Convex functions automatically create schema
# Tables created on first mutation call
npx convex deploy
```

**Handling Schema Changes:**

1. **Modify Convex function**
   ```typescript
   // src/convex/study.ts
   export const createNote = mutation(
     args: { ... },
     async (ctx, args) => {
       // New field added
       await ctx.db.insert("notes", {
         ...
         newField: "value"
       });
     }
   );
   ```

2. **Deploy changes**
   ```bash
   npx convex deploy
   ```

3. **Migrate existing data (if needed)**
   ```typescript
   // Create migration action
   export const migrateSchema = action({
     handler: async (ctx) => {
       // Update existing documents
     }
   });
   ```

### Data Backup & Restore

**Automatic Backups:**
- Convex automatically backs up data daily
- 30-day retention period
- Accessible via dashboard

**Manual Export:**
```bash
# Export all data
npx convex dump > backup.zip

# This creates a backup of all tables
```

**Restore from Backup:**
```bash
# Contact Convex support or use dashboard
# Select backup date
# Restore to dev or production database
```

---

## CI/CD Pipeline

### GitHub Actions

**Create workflow file:**

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'pnpm'
      
      - name: Install pnpm
        run: npm install -g pnpm
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Type check
        run: pnpm run type-check
      
      - name: Lint
        run: pnpm run lint
      
      - name: Build
        run: pnpm run build

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Vercel
        uses: vercel/action@v5
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
```

**Setup GitHub Secrets:**
1. Go to repository Settings > Secrets
2. Add:
   - `VERCEL_TOKEN`
   - `VERCEL_ORG_ID`
   - `VERCEL_PROJECT_ID`

### Automated Testing

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests
        run: npm run test:unit
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

---

## Docker Setup

### Dockerfile

```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package.json pnpm-lock.yaml ./

# Install dependencies
RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile

# Copy source
COPY . .

# Build
RUN pnpm run build

# Production stage
FROM node:18-alpine

WORKDIR /app

# Install serve to run static site
RUN npm install -g serve

# Copy built app from builder
COPY --from=builder /app/dist ./dist

# Expose port
EXPOSE 3000

# Start app
CMD ["serve", "-s", "dist", "-l", "3000"]
```

### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      VITE_CONVEX_URL: ${VITE_CONVEX_URL}
      VITE_GROQ_API_KEY: ${VITE_GROQ_API_KEY}
      # Add all other env vars
    restart: unless-stopped

  # Optional: Nginx reverse proxy
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - /etc/letsencrypt:/etc/letsencrypt:ro
    depends_on:
      - app
```

**Build and run:**
```bash
# Build image
docker build -t white-cell-protocol .

# Run container
docker run -p 3000:3000 \
  -e VITE_CONVEX_URL=https://your-project.convex.cloud \
  -e VITE_GROQ_API_KEY=gsk_... \
  white-cell-protocol

# Or with Docker Compose
docker-compose up -d
```

---

## Monitoring & Debugging

### Error Tracking (Sentry)

```bash
# Install Sentry
npm install @sentry/react @sentry/tracing

# Initialize in main.tsx
import * as Sentry from "@sentry/react";

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  environment: import.meta.env.NODE_ENV,
  tracesSampleRate: 1.0,
});
```

### Analytics (PostHog)

```bash
# Install PostHog
npm install posthog-js

# Initialize
import posthog from 'posthog-js'

posthog.init(import.meta.env.VITE_POSTHOG_API_KEY, {
  api_host: 'https://app.posthog.com'
})
```

### Logs

**View logs in production:**

**Vercel:**
```
Dashboard > Project > Deployments > [Deployment] > Logs
```

**Netlify:**
```
Site > Deploys > [Deploy] > Deploy Log
```

**VPS:**
```bash
# View PM2 logs
pm2 logs white-cell-protocol

# View Nginx logs
tail -f /var/log/nginx/error.log
```

### Performance Monitoring

```typescript
// Monitor performance metrics
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log('Performance:', entry.name, entry.duration);
  }
});

observer.observe({ entryTypes: ['navigation', 'resource'] });
```

---

## Troubleshooting

### Build Issues

**Issue: "Cannot find module"**
```bash
# Clear node_modules and reinstall
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

**Issue: "Port 5173 already in use"**
```bash
# Kill process using port
lsof -ti:5173 | xargs kill -9

# Or use different port
pnpm run dev -- --port 3000
```

### Runtime Issues

**Issue: "Convex connection failed"**
1. Verify `VITE_CONVEX_URL` is correct
2. Run `npx convex auth` to re-authenticate
3. Check if `npx convex dev` is running

**Issue: "Environment variables not loading"**
1. Verify `.env.local` exists
2. Restart dev server
3. Variables must start with `VITE_` to be available in browser

**Issue: "API requests returning 401"**
1. Check API key hasn't expired
2. Verify OAuth redirect URIs match
3. Ensure token is being sent in headers

### Deployment Issues

**Issue: "Deployment failed: Build error"**
1. Check build log for specific error
2. Run `pnpm run build` locally to reproduce
3. Ensure all environment variables are set in dashboard

**Issue: "Page shows 404 in production"**
1. Verify SPA routing is configured (see Vercel/Netlify configs above)
2. Check if `index.html` exists in build output
3. Clear browser cache and CDN cache

**Issue: "Slow initial load"**
1. Check bundle size: `pnpm run build --analyze`
2. Enable compression in server
3. Consider code splitting and lazy loading

---

## Performance Optimization

### Code Splitting

```typescript
// Lazy load components
const StudyModule = lazy(() => import('./pages/Study'));
const FitnessModule = lazy(() => import('./pages/Fitness'));

<Suspense fallback={<LoadingSpinner />}>
  <StudyModule />
</Suspense>
```

### Image Optimization

```typescript
// Use optimized images
import { Image } from '@/components/OptimizedImage';

<Image
  src="/workout.jpg"
  alt="Workout"
  width={800}
  height={600}
  priority={false}
/>
```

### Caching Strategy

```typescript
// Cache static assets
service-worker.js:
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open('v1').then((cache) => {
      return cache.addAll([
        '/',
        '/index.css',
        '/main.js'
      ]);
    })
  );
});
```

### Database Query Optimization

```typescript
// Use indexes and batching
// Instead of:
const users = await Promise.all(
  userIds.map(id => getUser(id))
);

// Use batch query:
const users = await db.query('users')
  .filter(u => userIds.includes(u._id))
  .collect();
```

---

## Deployment Checklist

- [ ] All tests passing
- [ ] No TypeScript errors
- [ ] No linting errors
- [ ] Production environment variables configured
- [ ] Database backups created
- [ ] SSL certificate valid
- [ ] Performance benchmarks reviewed
- [ ] Error tracking configured
- [ ] Analytics enabled
- [ ] Monitoring alerts set up
- [ ] Rollback plan documented
- [ ] Team notified of deployment

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** DevOps & Backend Team
