# Admin Page

**File Path:** `src/pages/Admin.tsx` (398 lines)

## Overview

The Admin page is a comprehensive administrative dashboard for managing the White Cell Protocol platform. It provides system administrators with tools for user management, analytics monitoring, security controls, feature flags, content moderation, and system health tracking. Access is restricted to users with admin role.

## Page Purpose

- **Primary Function:** Administrative system control panel
- **Target Users:** Platform admins (role-based access control)
- **Key Actions:** Manage users, monitor analytics, control features
- **Security Level:** Highest - requires admin authentication
- **Update Frequency:** Real-time system monitoring

## Admin Tab Structure

**8 Major Admin Tabs:**

### Tab 1: Overview (Dashboard)

**System Status Summary:**

```typescript
{
  totalUsers: number,
  activeUsers: number (last 24h),
  newUsersToday: number,
  totalSessions: number,
  averageSessionDuration: number (minutes),
  serverStatus: "online" | "warning" | "error",
  apiHealth: number (0-100%),
  databaseHealth: number (0-100%),
  storageUsage: number (bytes),
  errorRate: number (percentage)
}
```

**Key Metrics Display:**

| Metric | Display | Status |
|--------|---------|--------|
| Total Users | "12,847" | Trending: +234 this week |
| Active Users (24h) | "3,456" | 26.9% engagement |
| New Users Today | "142" | ↑ 15% vs yesterday |
| Total Sessions | "18,934" | Avg 1.47 sessions/user |
| Avg Session Duration | "47 min" | ↑ 8% from last week |

**Health Checks:**
- Server Status: ✅ Online
- API Health: ████████░░ 94%
- Database Health: ███████████ 100%
- Cache Health: ██████░░░░ 62%
- Storage: 847 GB / 1 TB (85% used)

**Alert Panel:**
- Critical issues highlighted in red
- Warning issues in yellow
- Info messages in blue
- Auto-refresh every 30 seconds

### Tab 2: Users Management

**User Management Dashboard:**

```typescript
{
  userId: string,
  email: string,
  name: string,
  role: "user" | "creator" | "admin" | "moderator",
  status: "active" | "suspended" | "banned" | "pending",
  createdAt: Date,
  lastLogin: Date,
  level: number,
  totalXP: number,
  subscriptionTier: "free" | "pro" | "premium",
  accountStatus: {
    emailVerified: boolean,
    twoFactorEnabled: boolean,
    apiAccess: boolean,
    suspended: boolean,
    banReason?: string
  }
}
```

**User Table Columns:**
- Checkbox (bulk select)
- Email / Name
- Role (Admin/Creator/User)
- Status (Active/Suspended/Banned)
- Level
- Joined Date
- Last Login
- Subscription
- Actions (Edit, Suspend, Ban, Delete)

**Table Features:**
- Sortable columns (click header)
- Filterable by role, status, subscription
- Search by email/name
- Pagination (50 users per page)
- Bulk actions (select multiple users)
- Export users to CSV

**User Actions:**

**View User Profile:**
- Account details
- Email address
- Phone (if provided)
- Linked integrations
- Subscription status
- Account created date
- Last activity

**Edit User:**
- Change name
- Update email
- Modify role
- Change subscription tier
- Add/remove features
- Edit user notes

**Manage Status:**
- Activate: Unlock previously suspended account
- Suspend: Prevent login (temporary)
- Ban: Permanent account deactivation
- Delete: Remove account (with confirmation)

**Bulk Actions:**
- Send email to selected users
- Batch change subscription tier
- Batch update roles
- Export selected users data
- Batch suspend/ban

**Filters & Search:**
```typescript
{
  role: "user" | "creator" | "admin" | "all",
  status: "active" | "suspended" | "banned" | "pending" | "all",
  subscriptionTier: "free" | "pro" | "premium" | "all",
  dateRange: { start: Date, end: Date },
  searchQuery: string,
  sortBy: "email" | "created" | "lastLogin" | "level",
  sortDirection: "asc" | "desc"
}
```

### Tab 3: Analytics

**Comprehensive Analytics Dashboard:**

```typescript
{
  dateRange: { start: Date, end: Date },
  metrics: {
    dailyActiveUsers: Array<{ date: Date, count: number }>,
    userRetention: Array<{ day: number, percent: number }>,
    signupTrend: Array<{ date: Date, count: number }>,
    churnRate: number (percentage),
    ltv: number (lifetime value in $),
    moduleUsage: { [module: string]: number (percentage) },
    featureAdoption: { [feature: string]: number (percentage) },
    errorMetrics: Array<{ type: string, count: number, trend: string }>,
    performanceMetrics: {
      avgPageLoadTime: number (ms),
      apiLatency: number (ms),
      errorRate: number (percentage)
    }
  }
}
```

**Charts & Visualizations:**

**User Metrics:**
- Daily Active Users: Line chart (30 day)
- User Retention: Cohort analysis
- Sign-up Trend: Bar chart
- Churn Rate: Trend indicator

**Module Usage:**
- Pie chart: Study (28%), Fitness (22%), Discipline (25%), Mind (15%), Creator (10%)
- Table: Module breakdown with user counts
- Trend: % change week-over-week

**Feature Adoption:**
- Top features by usage
- New feature adoption curve
- Feature drop-off detection

**Performance Metrics:**
- Page load time: ████░░ 1.2s
- API latency: ████████░░ 142ms
- Error rate: ░░░░░░░░░░ 0.8%
- Database query time: ████░░░░░░ 45ms

**Custom Reports:**
- Date range picker
- Export to PDF/CSV
- Save report templates
- Schedule email reports

### Tab 4: Security

**Security Management Center:**

```typescript
{
  failedLogins: {
    count: number,
    trend: string,
    topIPs: Array<{ ip: string, attempts: number }>,
    suspiciousActivity: boolean
  },
  apiKeyManagement: Array<{
    name: string,
    userId: string,
    createdAt: Date,
    lastUsed: Date,
    status: "active" | "revoked",
    permissions: string[]
  }>,
  suspiciousActivity: Array<{
    type: string,
    userId: string,
    timestamp: Date,
    details: string,
    severity: "low" | "medium" | "high"
  }>,
  securityAlerts: Array<{
    type: string,
    severity: string,
    timestamp: Date,
    description: string
  }>,
  bannedIPs: string[],
  rateLimitStatus: { endpoint: string, requestsPerMinute: number }[]
}
```

**Security Modules:**

**Suspicious Activity Monitor:**
- Real-time activity alerts
- Unusual login patterns detection
- Mass action detection
- Brute force attempt blocking
- Geo-location anomalies
- Device fingerprint tracking

**API Key Management:**
- List all API keys
- Revoke compromised keys
- Rotate keys
- Set expiration dates
- Track key usage
- Permission management per key

**Failed Login Tracking:**
- List failed login attempts
- Filter by date, user, IP
- Automatic account lockout after 5 failures
- Rate limiting per IP
- CAPTCHA challenge activation

**Suspicious IP Blocking:**
- View currently blocked IPs
- Add to blocklist
- Whitelist trusted IPs
- Geographic blocking rules
- VPN detection
- Proxy detection

**Two-Factor Authentication:**
- View 2FA enrollment stats
- Force 2FA requirement
- Manage recovery codes
- Reset 2FA for accounts

**Audit Log:**
- Admin actions logged
- User access logs
- Data access logs
- Permission changes
- Search & filter audit logs

### Tab 5: AI Metrics

**AI Usage & Cost Tracking:**

```typescript
{
  aiUsage: {
    groqRequests: { count: number, costPerRequest: number, totalCost: number },
    geminiRequests: { count: number, costPerRequest: number, totalCost: number },
    perplexityRequests: { count: number, costPerRequest: number, totalCost: number },
    totalRequests: number,
    totalCost: number,
    averageCostPerUser: number
  },
  modelPerformance: {
    model: string,
    avgResponseTime: number (ms),
    successRate: number (percentage),
    errorRate: number (percentage),
    userSatisfaction: number (1-5)
  },
  usageByFeature: Array<{
    feature: string,
    requests: number,
    costPerUser: number
  }>,
  tokenUsage: {
    totalTokensUsed: number,
    totalTokenCost: number,
    averageTokensPerRequest: number
  }
}
```

**Cost Dashboard:**

| Metric | Value | Trend |
|--------|-------|-------|
| Total API Requests | 1,247,500 | ↑ 12% from last month |
| Groq Requests | 892,000 | $445 (-2%) |
| Gemini Requests | 234,500 | $117 (+5%) |
| Perplexity Requests | 121,000 | $303 (+18%) |
| **Total Cost** | **$865** | ↑ $120 from last month |

**Per-User Metrics:**
- Average cost per user: $0.067
- Cost trend per user: ↑ 8%
- Most expensive feature: Creator AI
- Cheapest feature: Discipline Pomodoro

**Model Performance:**
- Groq latency: 245ms avg
- Gemini latency: 890ms avg
- Perplexity latency: 1500ms avg
- Success rate: 99.2%
- Error rate: 0.8%

**Usage by Feature:**
- Study AI Coach: 34% of requests
- Creator AI: 28% of requests
- Discipline AI: 18% of requests
- Mind Strategy: 12% of requests
- Sentinel AI: 8% of requests

**Cost Control Tools:**
- Set monthly budget caps
- Alert thresholds
- Model usage optimization recommendations
- Feature rate limiting
- Auto-enable/disable features by cost

### Tab 6: Feature Flags

**Feature Control & Experimentation:**

```typescript
{
  flags: Array<{
    flagName: string,
    description: string,
    enabled: boolean,
    percentageRollout: number (0-100%),
    targetAudience: {
      userIds: string[],
      roles: string[],
      subscriptionTiers: string[],
      countries: string[],
      dateRange: { start: Date, end: Date }
    },
    createdAt: Date,
    createdBy: string,
    modifiedAt: Date,
    modifiedBy: string,
    experiment: {
      isExperiment: boolean,
      controlGroup: number (%),
      treatmentGroup: number (%),
      startDate: Date,
      endDate: Date,
      results: {}
    }
  }>
}
```

**Feature Management:**

**View All Flags:**
- List of all feature flags
- Current status (on/off)
- Rollout percentage
- Target audience
- Last modified timestamp
- Author

**Create New Flag:**
- Flag name: string
- Description: text
- Default status: on/off
- Rollout percentage: 0-100%
- Target: specific users/roles/subscription
- Date range for flag
- Approval workflow

**Edit Flag:**
- Toggle enable/disable
- Adjust rollout percentage (gradual rollout)
- Modify target audience
- Update description
- Change dates
- View change history

**A/B Testing:**
- Define control group (%)
- Define treatment group (%)
- Assign metrics to track
- Set duration
- Auto-calculate sample size
- View test results
- Export test data

**Gradual Rollout:**
- 0% → 10% → 25% → 50% → 100%
- Automatic or manual progression
- Rollback capability
- Monitor metrics at each step

**Feature Flags Examples:**

| Flag | Status | Rollout | Audience |
|------|--------|---------|----------|
| `feature.new-sidebar` | Enabled | 100% | All users |
| `feature.advanced-analytics` | Enabled | 50% | Pro+ subscribers |
| `feature.ai-coach` | Enabled | 75% | All users |
| `beta.video-coaching` | Enabled | 10% | Beta testers |
| `experiment.new-ui` | Enabled | 30% | Random sample |

### Tab 7: System & Monitoring

**System Health & Infrastructure:**

```typescript
{
  systemStatus: {
    uptime: number (percentage),
    responseTime: number (ms),
    databaseStatus: "healthy" | "degraded" | "down",
    cacheStatus: "healthy" | "degraded" | "down",
    storageStatus: "healthy" | "warning" | "critical"
  },
  infrastructure: {
    activeConnections: number,
    requestsPerSecond: number,
    dataTransferRate: number (GB/s),
    cpuUsage: number (percentage),
    memoryUsage: number (percentage),
    diskUsage: number (percentage)
  },
  logs: Array<{
    timestamp: Date,
    level: "error" | "warning" | "info",
    service: string,
    message: string,
    stackTrace?: string
  }>,
  alerts: Array<{
    severity: "critical" | "high" | "medium" | "low",
    message: string,
    timestamp: Date,
    resolved: boolean
  }>
}
```

**Real-Time Monitoring:**

**System Health:**
- Uptime: 99.97% (last 30 days)
- Current response time: 142ms
- Database: ✅ Healthy
- Cache: ✅ Healthy
- Storage: ⚠️ 85% used

**Resource Usage:**
- CPU: ████████░░ 78%
- Memory: ██████░░░░ 64%
- Disk: ███████░░░░ 71%
- Network: ████░░░░░░ 34%

**Active Connections:**
- Total: 12,847
- API connections: 8,234
- WebSocket: 4,613
- Database connections: 145

**Request Rate:**
- Requests/sec: 2,847
- Average latency: 142ms
- P95 latency: 890ms
- P99 latency: 2,340ms

**Recent Logs:**
- Error log viewer with filtering
- Search by service, timestamp, message
- Export logs to file
- Stream live logs
- Alert on error spikes

**Scheduled Tasks:**
- Backup jobs
- Cleanup jobs
- Email sending queue
- Data sync jobs
- Status of each job

### Tab 8: Notifications & Communication

**Admin Communication Tools:**

```typescript
{
  announcement: {
    title: string,
    content: string,
    targetAudience: string[] (role/segment),
    priority: "low" | "medium" | "high" | "critical",
    channels: ["email", "push", "in-app"],
    scheduledAt: Date,
    sentAt: Date,
    readCount: number,
    clickCount: number
  },
  emailTemplates: Array<{
    name: string,
    subject: string,
    body: string,
    variables: string[],
    lastUsed: Date,
    usageCount: number
  }>,
  notificationQueue: {
    pending: number,
    processing: number,
    failed: number,
    delivered: number
  }
}
```

**Announcement Management:**

**Send Announcement:**
- Title + body text
- Rich text editor (formatting, images)
- Target audience selector:
  - All users
  - By role (admin, creator, user)
  - By subscription (free, pro, premium)
  - By activity level
  - Geographic targeting
- Select channels: Email, push, in-app
- Schedule send time
- Preview before sending
- Send confirmation

**Email Templates:**
- Welcome email
- Password reset
- Verification email
- Promotional emails
- System alerts
- Weekly digest
- Edit/customize templates
- Test send to admin email

**Notification Queue:**
- View pending notifications
- Retry failed notifications
- Monitor sending status
- Queue statistics
- Performance metrics
- Throttle settings

**Bulk Operations:**
- Send to segment
- Multi-language support
- Schedule campaigns
- A/B test messages

## State Management

**Admin State:**
```typescript
const [adminData, setAdminData] = useState({
  activeTab: "overview",
  users: [],
  systemStatus: {},
  analytics: {},
  securityAlerts: [],
  featureFlags: [],
  metrics: {}
});

const [filters, setFilters] = useState({
  dateRange: { start: Date.now() - 30*24*60*60*1000, end: Date.now() },
  selectedUsers: [],
  searchQuery: ""
});
```

## API Integration

**Admin Queries & Mutations:**
```typescript
// Queries
const systemStatus = useQuery(api.admin.getSystemStatus);
const users = useQuery(api.admin.getUsers, { filters, pagination });
const analytics = useQuery(api.admin.getAnalytics, { dateRange });
const securityAlerts = useQuery(api.admin.getSecurityAlerts);
const featureFlags = useQuery(api.admin.getFeatureFlags);

// Mutations
const updateUser = useMutation(api.admin.updateUser);
const suspendUser = useMutation(api.admin.suspendUser);
const toggleFeatureFlag = useMutation(api.admin.toggleFeatureFlag);
const sendAnnouncement = useMutation(api.admin.sendAnnouncement);
const updateSystemSettings = useMutation(api.admin.updateSystemSettings);
```

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Tab` | Navigate tabs |
| `Ctrl+S` | Save changes |
| `Ctrl+F` | Search users |
| `Ctrl+/` | Command palette |
| `Escape` | Close dialogs |

## Accessibility

- All tables keyboard navigable
- Chart descriptions for screen readers
- Color + icons for status
- Focus indicators
- ARIA labels
- Form validation clear

## Security Features

- Admin-only access (role verified)
- All admin actions logged
- Session timeout after 15 minutes
- Require re-authentication for sensitive actions
- IP whitelist support
- Audit trail for all changes
- Data encryption in transit and at rest

## Performance

- Lazy load tabs
- Virtualize long tables
- Cache analytics data
- Debounce real-time updates
- Pagination for large datasets

## Future Features

- [ ] Advanced user segmentation
- [ ] Predictive analytics
- [ ] ML-based anomaly detection
- [ ] Custom dashboards
- [ ] API rate limiting controls
- [ ] Subscription management
- [ ] Content moderation tools
- [ ] Custom reports builder

## Technical Stack

- **Data:** Convex real-time queries
- **Tables:** React-table with sorting/filtering
- **Charts:** Recharts or Chart.js
- **Styling:** Tailwind CSS
- **Authentication:** Admin role verification
- **Logging:** Structured logging system
- **Alerts:** Real-time notification system
