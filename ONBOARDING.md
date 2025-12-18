# Onboarding Page

**File Path:** `src/pages/Onboarding.tsx` (683 lines)

## Overview

The Onboarding page is a multi-step wizard that guides new users through platform familiarization and personal profile setup. It collects essential user information, preferences, goals, and habits to personalize the experience and configure initial AI recommendations.

## Page Purpose

- **Primary Function:** First-time user setup wizard
- **Target Users:** New users (redirected after signup)
- **Key Action:** Complete personalization questionnaire
- **Duration:** 5-10 minutes (7 steps)

## Onboarding Flow

**Total Steps: 7**

### Step 1: Personal Information

**User Input:**
```typescript
{
  name: string,
  age: string (or number),
  timezone: string (auto-detected)
}
```

**Auto-Population:**
- Name extracted from email prefix
- Timezone detected from browser (Intl.DateTimeFormat)
- Validation: Name required, minimum 2 characters

**UI:**
- Text input: Name
- Number input: Age
- Select: Timezone (searchable dropdown)
- Next button (enabled when name filled)

### Step 2: Life Goals & Priorities

**Goal Selection:**
```typescript
{
  goals: string[] (multiple select),
  primaryFocus: string (single select)
}
```

**Goal Checkboxes:**
- "Build unbreakable habits"
- "Improve fitness & health"
- "Master knowledge & learning"
- "Sharpen strategic thinking"
- "Create content & monetize"
- "Achieve peak performance"
- "Build discipline & willpower"
- "Improve mental health"

**Primary Focus (Single):**
- "Discipline" (habits, routines)
- "Fitness" (workouts, nutrition)
- "Study" (knowledge, learning)
- "Mind" (strategy, thinking)
- "Creator" (content, money)
- "Balanced" (all areas equally)

**Welcome Summary Generated:**
- "Your journey to [focus goal] begins now!"
- Personalized based on primary focus

### Step 3: Schedule & Availability

**Time Input:**
```typescript
{
  availableTime: number (minutes per day, 1-120),
  wakeUpTime: string (HH:MM format),
  sleepTime: string (HH:MM format),
  experienceLevel: "beginner" | "intermediate" | "advanced"
}
```

**Smart Time Estimation:**
- Calculate awake hours from wake/sleep times
- Suggest 15% of awake hours for app usage
- Example: Awake 16 hours → suggest 144 minutes (2.4 hours)
- User can adjust slider

**Slider:**
- Min: 15 minutes (just getting started)
- Max: 120 minutes (serious commitment)
- Default: 30-60 minutes (recommended)

**Experience Level:**
- "I'm new to productivity apps"
- "I've tried some productivity tools"
- "I'm an experienced user"

### Step 4: Interests & Specializations

**Interest Selection:**
```typescript
{
  interests: string[] (multiple select),
  fitnessLevel: string,
  studySubjects: string[]
}
```

**Interests:**
- AI & Technology
- Personal Development
- Entrepreneurship
- Psychology & Philosophy
- Physical Fitness
- Creative Writing
- Competitive Gaming
- Music & Art
- Business & Finance
- Social Impact

**Fitness Level:**
- "Sedentary / Just starting"
- "Light exercise 1-2x/week"
- "Moderate exercise 3-4x/week"
- "High / Training 5+x/week"

**Study Subjects:**
- Mathematics
- Science
- Programming
- Languages
- History
- Philosophy
- Writing
- Arts (multiple select)

### Step 5: Content Platforms & Creation

**Platform Selection:**
```typescript
{
  contentPlatforms: string[] (multiple select)
}
```

**Platforms:**
- Instagram
- YouTube
- TikTok
- Twitter
- Podcasting
- Blogging
- Not interested in creating

**User Type Identification:**
- Creator (1+ platform selected)
- Non-creator (none selected)

### Step 6: Motivation & Psychology

**Motivation Assessment:**
```typescript
{
  motivations: string[] (multiple select)
}
```

**Motivation Types:**
- "I want to be the best version of myself"
- "I want to succeed financially"
- "I want to help others"
- "I want to overcome challenges"
- "I want to build habits & consistency"
- "I want to compete & win"
- "I want to learn continuously"

**Psychological Profile:**
- Identifies user's primary drivers
- Shapes AI coaching tone
- Personalizes reward/motivation messaging

### Step 7: Review & Confirmation

**Summary Display:**
- Name: [user input]
- Primary Goal: [user selection]
- Available Time: [X minutes/day]
- Interests: [comma-separated list]
- Platforms: [comma-separated or "Not creating"]

**Final Actions:**
- Confirm all information
- Optionally edit previous steps
- Submit to create profile
- Redirect to `/empire` dashboard

## State Management

**Comprehensive State:**

```typescript
const [step, setStep] = useState(1);
const [isSubmitting, setIsSubmitting] = useState(false);
const [profileImage, setProfileImage] = useState<string | null>(null);

const [data, setData] = useState({
  name: "",
  age: "",
  timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
  goals: [],
  primaryFocus: "",
  experienceLevel: "beginner",
  availableTime: 30,
  interests: [],
  fitnessLevel: "beginner",
  studySubjects: [],
  contentPlatforms: [],
  wakeUpTime: "06:00",
  sleepTime: "22:00",
  motivations: [],
});
```

## Features

### Smart Time Calculation

```typescript
useEffect(() => {
  if (data.wakeUpTime && data.sleepTime) {
    const wake = parseInt(data.wakeUpTime.split(":")[0]);
    const sleep = parseInt(data.sleepTime.split(":")[0]);
    const awakeHours = sleep > wake ? sleep - wake : 24 - wake + sleep;
    const suggestedTime = Math.min(Math.floor(awakeHours * 0.15), 120);
    if (data.availableTime === 30) {
      setData(prev => ({ ...prev, availableTime: suggestedTime }));
    }
  }
}, [data.wakeUpTime, data.sleepTime]);
```

### Navigation Controls

**Step Navigation:**
- `handleNext()` - Move to next step (validates current)
- `handleBack()` - Return to previous step
- `handleSkip()` - Skip onboarding, go to `/empire`
- `handleSubmit()` - Submit data, redirect to `/empire`

**Validation:**
- Step 1: Name required (minimum 2 chars)
- Other steps: Optional (encourage but not required)

### Haptic Feedback

```typescript
if (navigator.vibrate) {
  navigator.vibrate(10); // 10ms vibration on button click
}
```

### Image Upload

**Profile Photo (Optional):**
```typescript
const handleImageUpload = (e: React.ChangeEvent<HTMLInputElement>) => {
  const file = e.target.files?.[0];
  if (file) {
    const reader = new FileReader();
    reader.onloadend = () => {
      setProfileImage(reader.result as string);
    };
    reader.readAsDataURL(file);
  }
};
```

- Accept JPG, PNG, GIF
- Convert to data URL
- Display preview
- Optional (can skip)

## Animations

- **Page transitions:** Fade + scale (Framer Motion)
- **Button interactions:** Hover scale (1.02x), tap (0.98x)
- **Input fields:** Focus highlight with color transition
- **Progress indicator:** Show current step (X/7)

## Responsive Design

**Mobile (< 640px):**
- Full-width form
- Single column
- Large button targets
- Bottom navigation arrows
- Font scaling for readability

**Desktop (> 1024px):**
- Centered form (max-width: 500px)
- Larger form inputs
- Multi-column for multi-select (when applicable)
- Progress bar at top

## Accessibility

- Semantic HTML for form structure
- Label associated with inputs
- Keyboard navigation between steps
- Focus management
- Screen reader support
- High contrast (WCAG AA)
- Error messages clear and actionable

## API Integration

**Mutation:**
```typescript
const completeOnboarding = (useMutation as any)((api as any).users.completeOnboarding);

const handleSubmit = async () => {
  setIsSubmitting(true);
  try {
    await completeOnboarding({ onboardingData: data });
    const summary = generateWelcomeSummary();
    toast.success(summary, { duration: 5000 });
    setTimeout(() => {
      navigate("/empire");
    }, 1000);
  } catch (error) {
    toast.error("Failed to complete onboarding");
    setIsSubmitting(false);
  }
};
```

## Data Stored

**User Profile Created:**
```typescript
{
  userId: string (auto-generated),
  email: string (from auth),
  name: string,
  age: number,
  timezone: string,
  goals: string[],
  primaryFocus: string,
  experienceLevel: string,
  dailyAvailableTime: number,
  interests: string[],
  fitnessLevel: string,
  studySubjects: string[],
  contentPlatforms: string[],
  wakeUpTime: string,
  sleepTime: string,
  motivations: string[],
  profileImage: File (optional),
  completedOnboarding: boolean,
  onboardingCompletedAt: Date
}
```

## Connected Components

- **LichessConnect:** Optional (Step 6+) - Link chess account

## Personalization Engine

**Based on Onboarding Data:**

| Input | Output |
|-------|--------|
| Primary Focus | Load that module first |
| Available Time | Set daily goal recommendations |
| Fitness Level | Calibrate workout difficulty |
| Interests | Filter content recommendations |
| Content Platforms | Enable Creator module features |
| Motivations | Personalize reward messaging |

## Skip Option

Users can click "Skip Onboarding" at any step:
- Navigates directly to `/empire`
- Uses default values for missed data
- Can update in Settings later
- Default experience configured

## Completion Notification

**Summary Generated:**
```
Welcome [Name]! Your journey to [focus goal] begins now. 
[X] minutes daily will transform your life. 🚀
```

**Examples:**
- "Welcome Sarah! Your journey to mastering knowledge begins now. 45 minutes daily will transform your life. 🚀"
- "Welcome Marcus! Your journey to total life domination begins now. 90 minutes daily will transform your life. 🚀"

## Post-Onboarding

**Next Steps:**
1. Show welcome message
2. Redirect to `/empire` dashboard
3. Highlight primary module
4. Show daily check-in
5. Display initial AI recommendations
6. Suggest first task to try

## Technical Stack

- **Form State:** React useState
- **Routing:** React Router
- **Mutations:** Convex
- **Animations:** Framer Motion
- **Date/Time:** Native JavaScript
- **File Handling:** FileReader API
- **Notifications:** Sonner toast
- **Responsive:** Tailwind CSS grid
- **Haptics:** Vibration API
