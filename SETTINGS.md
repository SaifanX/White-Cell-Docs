# Settings Page

**File Path:** `src/pages/Settings.tsx` (261 lines)

## Overview

The Settings page provides comprehensive user account management, preferences, integrations, and application configuration. It allows users to personalize their experience, connect external services, manage data, and control notification/privacy settings.

## Page Purpose

- **Primary Function:** User account and app configuration
- **Target Users:** All authenticated users
- **Key Actions:** Update profile, manage integrations, export data
- **Access:** Bottom navigation or user menu

## Settings Tabs

**7 Tab Organization:**

### Tab 1: Profile

**User Profile Information:**

```typescript
{
  name: string,
  email: string (read-only),
  age: number,
  bio: string,
  profileImage: string (URL),
  timezone: string,
  availableTime: number (minutes/day),
  primaryFocus: string,
  experienceLevel: string
}
```

**Editable Fields:**
- Name: Text input (min 2 chars)
- Bio: Text area (max 500 chars)
- Age: Number input
- Timezone: Searchable dropdown
- Profile Photo: File upload (JPG, PNG)
- Available Time: Slider (15-120 minutes)
- Primary Focus: Select (Discipline/Fitness/Study/Mind/Creator)
- Experience Level: Radio buttons

**Display Info (Read-Only):**
- Email address
- Account creation date
- Account status (Active)
- Last login timestamp

**Actions:**
- Save changes → toast notification
- Delete profile picture
- Download profile data

### Tab 2: Theme & Display

**Theme Settings:**

```typescript
{
  theme: "light" | "dark" | "auto",
  accentColor: string (hex),
  fontSize: "small" | "medium" | "large",
  compactMode: boolean,
  animations: boolean,
  colorBlindMode: "none" | "deuteranopia" | "protanopia" | "tritanopia",
  reducedMotion: boolean
}
```

**Theme Selection:**
- Light Mode: "☀️ Light"
- Dark Mode: "🌙 Dark"
- Auto: "🔄 System (matches OS)"

**Accent Color Picker:**
- Predefined colors: Blue, Purple, Green, Orange, Red, Pink, Cyan, Yellow
- Custom hex input
- Live preview of selected color
- Apply to buttons, links, highlights

**Typography:**
- Font Size Slider: Small → Large
- Preview: "This is how text will look"
- Affects all app text

**Display Options:**
- Toggle: Compact Mode (reduce padding, smaller cards)
- Toggle: Enable Animations (Framer Motion)
- Toggle: Reduce Motion (accessibility)
- Toggle: Color Blind Mode (palette adaptation)

**Preview Panel:**
- Shows how settings will look
- Updates in real-time
- Sample components displayed

### Tab 3: Language & Localization

**Language Selection:**

```typescript
{
  language: string (language code),
  dateFormat: string,
  timeFormat: "12h" | "24h",
  numberFormat: string,
  currencySymbol: string,
  firstDayOfWeek: "monday" | "sunday"
}
```

**Available Languages:**
- English (en)
- Spanish (es)
- French (fr)
- German (de)
- Japanese (ja)
- Mandarin (zh)
- Hindi (hi)
- Arabic (ar)
- Portuguese (pt)
- Russian (ru)

**Localization Options:**
- Date Format: MM/DD/YYYY, DD/MM/YYYY, YYYY-MM-DD
- Time Format: 12-hour (AM/PM) or 24-hour
- Number Format: 1,000.00 or 1.000,00
- Currency: $ (USD), € (EUR), £ (GBP), ¥ (JPY), etc.
- First Day of Week: Sunday or Monday (affects calendars)

**Effects:**
- Updates app UI immediately
- Persists to user profile
- Calendar, numbers, dates all update
- Component labels translate

### Tab 4: Integrations

**Connected Services:**

```typescript
{
  github: { connected: boolean, username: string, createdAt: Date },
  notion: { connected: boolean, workspaceId: string, createdAt: Date },
  spotify: { connected: boolean, username: string, createdAt: Date },
  lichess: { connected: boolean, username: string, createdAt: Date },
  gmail: { connected: boolean, email: string, createdAt: Date },
  openai: { connected: boolean, apiKeySet: boolean, createdAt: Date }
}
```

**Integration Cards:**

**GitHub:**
- Status: [Connected] or [Connect]
- Shows: Username, profile link
- Action: View profile, disconnect
- Permissions: Public repo access

**Notion:**
- Status: [Connected] or [Connect]
- Shows: Workspace name, permission date
- Action: View workspace, disconnect
- Permissions: Database read/write

**Spotify:**
- Status: [Connected] or [Connect]
- Shows: Email, connected date
- Action: View profile, disconnect, sync playlists
- Permissions: Playlist access

**Lichess:**
- Status: [Connected] or [Connect]
- Shows: Username, rating, connected date
- Action: View profile, disconnect
- Permissions: Public profile, puzzle data

**Gmail:**
- Status: [Connected] or [Connect]
- Shows: Email address, connected date
- Action: Manage labels, disconnect
- Permissions: Label read access

**Custom API Keys:**
- OpenAI API Key: [••••••••••] (masked input)
- Groq API Key: [••••••••••] (masked input)
- Show/hide toggle for verification
- Remove button for each key

### Tab 5: Achievements & Level

**User Progress Display:**

```typescript
{
  level: number,
  totalXP: number,
  nextLevelXP: number,
  progressPercent: number,
  totalAchievements: number,
  achievementCategories: {
    discipline: Achievement[],
    fitness: Achievement[],
    study: Achievement[],
    mind: Achievement[],
    creator: Achievement[],
    special: Achievement[]
  },
  badges: Badge[],
  streaks: {
    taskStreak: number,
    workoutStreak: number,
    studyStreak: number
  }
}
```

**Level & XP Display:**
- Current Level: [X]
- XP Progress Bar: [████████░░] Y/Z XP
- Estimated time to next level
- Total XP earned lifetime
- Leaderboard position (if enabled)

**Achievement Showcase:**
- Grid of earned achievements
- Category tabs: All, Discipline, Fitness, Study, Mind, Creator
- Locked achievements shown as "???" with fuzzy unlock conditions
- Tooltip on hover: Achievement name, description, unlock date
- Share achievement button (social media)

**Badge System:**
- Badges displayed in row
- Different badge types: Streaks, Milestones, Special
- Example: 🔥 30-Day Streak, 💪 1000 Workouts, 🧠 Knowledge Master

**Streak Display:**
- Task Streak: [X] days 🔥
- Workout Streak: [X] days 💪
- Study Streak: [X] days 📚
- All-time records for each

### Tab 6: Data & Privacy

**Data Management:**

```typescript
{
  exportData: {
    format: "json" | "csv",
    includeHistory: boolean,
    dateRange: { start: Date, end: Date },
    selectedCategories: string[]
  },
  privacy: {
    profilePrivate: boolean,
    hideAchievements: boolean,
    hideActivity: boolean,
    hideStatistics: boolean
  },
  dataRetention: {
    autoDeleteAfter: number (months or "never"),
    retainBackups: boolean
  }
}
```

**Export Options:**

**Data Export:**
- Button: "📥 Export All Data"
- Formats: JSON (complete), CSV (spreadsheet)
- Options:
  - Include all history (or last 30/60/90 days)
  - Choose what to export: Profile, Notes, Workouts, Tasks, etc.
- Downloads file to computer
- Processing message: "Preparing your export..."

**Export Contents (JSON):**
```json
{
  "profile": { "name": "...", "email": "...", "level": 10 },
  "xp": [{ "date": "...", "source": "...", "amount": 100 }],
  "tasks": [{ "title": "...", "status": "...", "date": "..." }],
  "workouts": [{ "type": "...", "duration": "...", "date": "..." }],
  "notes": [{ "title": "...", "content": "...", "created": "..." }],
  "achievements": [{ "name": "...", "unlockedAt": "..." }]
}
```

**Privacy Controls:**
- Toggle: Make profile private (hide from leaderboards)
- Toggle: Hide achievements from others
- Toggle: Hide activity feed
- Toggle: Hide statistics
- Save changes

**Data Deletion:**
- Button: "🗑️ Delete Account"
- Confirmation dialog: "Are you sure? This cannot be undone."
- Requires password confirmation
- Option to download backup first
- Schedules deletion for 30 days (grace period)
- Can cancel within grace period

**Data Retention Policy:**
- Auto-delete old data: Never / 6 months / 1 year / 2 years
- Retain backups: Toggle
- Show data size: "[2.3 MB of data stored]"

### Tab 7: Notifications & Sound

**Notification Settings:**

```typescript
{
  email: {
    dailyDigest: boolean,
    achievements: boolean,
    streakReminders: boolean,
    weeklyReport: boolean,
    importantUpdates: boolean
  },
  push: {
    enabled: boolean,
    streakWarning: boolean,
    motivationalTips: boolean,
    newFeatures: boolean,
    challenges: boolean,
    remind: "disabled" | "9am" | "12pm" | "6pm" | "custom"
  },
  sound: {
    enabled: boolean,
    volume: number (0-100%),
    soundType: "default" | "minimal" | "none",
    testSound: function()
  }
}
```

**Email Notifications:**
- Toggle: Daily digest email
- Toggle: Achievement notifications
- Toggle: Streak break reminders
- Toggle: Weekly progress report
- Toggle: Important updates & announcements
- Frequency: Real-time / Daily / Weekly

**Push Notifications:**
- Global toggle: Enable push notifications
- Toggle: Streak break warnings (alert if streak at risk)
- Toggle: Motivational tips & encouragement
- Toggle: New features & updates
- Toggle: Challenge invitations
- Time selector: When to remind (disable, 9am, 12pm, 6pm, custom)
- Browser permission check

**Sound & Haptics:**
- Toggle: Enable sound effects
- Volume slider (0-100%)
- Sound type selector:
  - Default: Full sounds (ding, whoosh, success)
  - Minimal: Subtle tones
  - None: No sounds
- Test button: Play sample sound
- Haptic feedback toggle (mobile)

## State Management

**Settings State:**
```typescript
const [settings, setSettings] = useState({
  theme: "dark",
  accentColor: "#3b82f6",
  language: "en",
  notifications: {
    email: true,
    push: true,
    sound: true,
    volume: 75
  },
  // ... other settings
});

const [isSaving, setIsSaving] = useState(false);
const [hasChanges, setHasChanges] = useState(false);
```

**Hooks:**
- `useSettings()` - Access shared settings
- `useTheme()` - Apply theme changes
- `useLanguage()` - Handle translations
- `useNotifications()` - Manage notification preferences

## API Mutations

**Update Settings:**
```typescript
const updateSettings = useMutation(api.users.updateSettings);

const handleSave = async () => {
  setIsSaving(true);
  try {
    await updateSettings({ settings });
    toast.success("Settings saved!");
    setHasChanges(false);
  } catch (error) {
    toast.error("Failed to save settings");
  } finally {
    setIsSaving(false);
  }
};
```

**Integration Mutations:**
```typescript
const connectIntegration = useMutation(api.integrations.connect);
const disconnectIntegration = useMutation(api.integrations.disconnect);
const exportData = useMutation(api.users.exportData);
const deleteAccount = useMutation(api.users.requestAccountDeletion);
```

## Features

### Auto-Save

```typescript
useEffect(() => {
  if (!hasChanges) return;
  
  const timeout = setTimeout(() => {
    handleSave();
  }, 2000); // Auto-save after 2 seconds of no changes
  
  return () => clearTimeout(timeout);
}, [hasChanges]);
```

### Search Functionality

- Search across all settings
- Keywords: "dark mode", "export", "github", "notifications"
- Highlights matching settings
- Auto-scroll to relevant section

### Keyboard Shortcuts

- `Ctrl+S` / `Cmd+S`: Save settings
- `Ctrl+/` / `Cmd+/`: Open search
- `Tab`: Navigate between tabs
- `Enter`: Confirm dialogs

### Mobile Responsiveness

**Mobile (< 640px):**
- Full-width tabs
- Single column settings
- Bottom sheet dialogs
- Larger toggle switches

**Tablet/Desktop (> 768px):**
- Side-by-side layout (tabs left, content right)
- Wider form fields
- Multi-column where applicable

## Performance

- Lazy load integration status
- Debounce theme changes (250ms)
- Cache language files
- Minimize re-renders with memoization

## Accessibility

- Label all form controls
- Color not only information indicator
- Sufficient contrast ratios
- Keyboard-navigable tabs
- Focus indicators on buttons
- ARIA labels for icons
- Screen reader support for toggles

## Connected Pages

- Profile data synced with `/empire` dashboard
- Language changes applied site-wide
- Theme changes persist across pages
- Integration statuses visible in relevant modules

## Related Components

- `ThemeToggle.tsx` - Quick theme switcher
- `LanguageSelector.tsx` - Language picker
- `UserProfile.tsx` - Profile info component
- Integration buttons in modules

## Future Features

- [ ] Two-factor authentication
- [ ] Session management (view active devices)
- [ ] App usage analytics
- [ ] Recommended content based on settings
- [ ] Settings presets (templates)
- [ ] Settings sync across devices
- [ ] Bulk data operations
- [ ] Advanced privacy controls

## Technical Stack

- **State Management:** React useState + hooks
- **Data:** Convex mutations & queries
- **Theming:** Tailwind CSS + CSS variables
- **Forms:** React Hook Form (optional)
- **Animations:** Framer Motion for transitions
- **Storage:** Browser localStorage for local preferences
- **Security:** Encrypted sensitive data (API keys)
