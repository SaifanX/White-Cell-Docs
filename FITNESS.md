# Fitness Module

**File Path:** `src/pages/Fitness.tsx` (594 lines)

## Overview

The Fitness & Combat module is a comprehensive fitness tracking system that combines workout logging, macro/nutrition tracking, progress photo documentation, rest timer, exercise library, goal management, and personal record tracking. It's designed for athletes, fitness enthusiasts, and anyone optimizing their physical health.

## Page Purpose

- **Primary Function:** Complete fitness and nutrition management platform
- **Target Users:** Athletes, fitness enthusiasts, bodybuilders, health-conscious users
- **Core Feature:** Integrated macro tracking with AI food recognition
- **Integration:** Unified calendar, Spotify for workout music, XP system

## Key Features

### 1. Workout Logging System

**Log Workout:**
```typescript
{
  type: string (cardio, strength, flexibility, sports),
  duration: number (minutes),
  calories: number (estimated),
  notes: string,
  exercises: Exercise[],
  date: Date,
  intensity: "low" | "moderate" | "high" | "very-high"
}
```

**Exercise Tracking:**
- Exercise name (searchable database)
- Sets × Reps (e.g., 3×8, 5×5)
- Weight/resistance (kg or lbs)
- Distance (for cardio)
- Time duration
- Rest period between sets
- Notes on form/difficulty

**Workout Types:**
- Strength training (exercises with weights)
- Cardio (running, cycling, swimming, rowing)
- Flexibility (yoga, stretching, mobility)
- Sports (basketball, soccer, tennis)
- Mixed (combination workout)

### 2. Measurement & Progress Tracking

**Log Measurements:**
```typescript
{
  weight: number (kg or lbs),
  bodyFatPercentage: number (optional),
  muscleMass: number (optional),
  date: Date,
  notes: string
}
```

**Tracked Metrics:**
- Weight (primary metric, daily tracking)
- Body fat percentage
- Muscle mass
- Waist, chest, arm, leg circumferences
- Workout frequency (per week)
- Total workout time

**Progress Analytics:**
- Weight trend line (7, 30, 90 day views)
- Identify plateaus (no change > 2 weeks)
- Predict goal achievement date
- Compare measurements over time
- Visual progress heatmap

### 3. Personal Records (PRs)

**Log PR:**
```typescript
{
  exercise: string,
  value: number,
  unit: string (kg, lbs, reps, time, distance),
  date: Date,
  notes: string
}
```

**PR Tracking:**
- Deadlift PR: 315 lbs (Dec 10, 2024)
- Squat PR: 285 lbs (Dec 5, 2024)
- Bench press PR: 225 lbs (Nov 28, 2024)
- 5K run: 19:45 (Dec 1, 2024)
- Plank hold: 3:45 (Oct 15, 2024)

**PR Notifications:**
- Celebrate new PRs with badges
- Award bonus XP (50 XP + 10 XP per lb/kg improvement)
- Track PR history per exercise
- Show progression timeline

### 4. Macro & Nutrition Tracking

**Log Meal:**
```typescript
{
  foodName: string,
  quantity: number,
  unit: string (grams, ml, servings, etc.),
  macros: {
    calories: number,
    protein: number (grams),
    carbs: number (grams),
    fats: number (grams)
  },
  date: Date,
  mealType: "breakfast" | "lunch" | "dinner" | "snack"
}
```

**Food Database Options:**

1. **OpenFoodFacts** (Free, 2M+ foods)
   - Barcode scanning (UPC lookup)
   - Nutritional information
   - User-contributed data

2. **Nutritionix API** (Accurate restaurant/common foods)
   - Restaurant menus
   - Brand foods
   - Detailed macro breakdown

3. **Custom Foods** (User-created)
   - Save favorite meals
   - Calculate macros manually
   - Reuse for quick logging

**Macro Goals:**
```typescript
{
  dailyCalories: number,
  proteinGrams: number,
  carbsGrams: number,
  fatsGrams: number,
  proteinPercentage: number,
  date: Date
}
```

**Daily Macro Summary:**
- Progress rings for each macro (calories, protein, carbs, fats)
- Remaining allowance
- Percentage of goal completed
- Notifications when goal exceeded/underachieved

**AI-Powered Meal Logging:**
- Take photo of meal
- AI identifies food items
- Estimate portion size
- Auto-populate macros
- One-tap logging

### 5. Exercise Library

**Exercise Database:**
- 500+ exercises with descriptions
- Muscle groups targeted
- Difficulty level (beginner to advanced)
- Equipment required (dumbbell, barbell, machine, bodyweight)
- Video demonstrations (links to YouTube)
- Form tips and common mistakes

**Filter & Search:**
- Filter by muscle group (chest, back, shoulders, arms, legs, core)
- Filter by equipment
- Filter by difficulty
- Search by name
- Sort by popularity

**User-Created Exercises:**
- Create custom exercises (for tracking unusual movements)
- Save form tips
- Track PRs on custom exercises
- Share with community (planned)

### 6. Workout Templates

**Save & Reuse:**
```typescript
{
  name: string,
  description: string,
  exercises: [
    {
      exerciseName: string,
      sets: number,
      reps: number,
      notes: string
    }
  ],
  estimatedDuration: number (minutes)
}
```

**Template Examples:**
- PPL (Push/Pull/Legs)
- Upper/Lower split
- Full body (3x/week)
- Functional fitness
- Endurance training
- Custom user templates

**Apply Template:**
- Quick-start workouts from templates
- Modify exercises on-the-fly
- Log actual performance vs plan
- Update template based on experience

### 7. Rest Timer (Active Recovery)

**Customizable Countdown:**
- Set duration: 100s to 1000s (100 second increments)
- Visual progress indicator (conic gradient circle)
- Pause/resume timer
- Skip remaining rest period
- Sound notification on completion
- Haptic feedback (vibration)

**Use Cases:**
- Rest between sets (e.g., 90s rest on compound lifts)
- Active recovery between workouts
- Meditation timer
- Warm-up countdown

**Features:**
- Large, readable time display
- Adjustable text size
- Dark mode optimized
- Continue to next exercise button
- Log notes during rest (planned)

### 8. Progress Photos

**Upload & Track:**
```typescript
{
  photo: File (image),
  category: string (front, side, back, flexed, unflexed),
  notes: string,
  weight: number (at time of photo),
  date: Date
}
```

**Progress Photo Gallery:**
- Grid view by date
- Before/after sliders (side-by-side comparison)
- Filter by category
- Sort by date or weight

**Comparison:**
- View progression over time (3 photos at once)
- Identify changes (muscle gained, fat lost)
- Motivational gallery of progress
- Private by default (shareable with permission)

### 9. Analytics Dashboard

**Pull-to-Refresh:**
- Swipe down to refresh all fitness data
- Haptic feedback on refresh
- Toast notification: "Data refreshed!"

**Key Metrics:**
- Total workouts (this week, this month)
- Total workout hours
- Average workout duration
- Most frequent exercise
- Recent PRs
- Current weight vs goal
- Macro adherence

**Charts & Graphs:**
- Weight trend line (7, 30, 90 day)
- Workout frequency bar chart
- Macro distribution pie chart
- Daily calories logged
- Exercise frequency heatmap

### 10. Fitness Goals

**Goal Types:**
```typescript
{
  goalType: "weight" | "strength" | "endurance" | "body-composition",
  targetValue: number,
  unit: string,
  deadline: Date,
  currentValue: number,
  category: string
}
```

**Examples:**
- Weight loss: 200 lbs → 180 lbs by March 1
- Strength: Deadlift 405 lbs by March
- Endurance: Run 5K in under 20 minutes
- Body composition: 15% body fat
- Muscle gain: 200 lbs total mass

**Goal Tracking:**
- Auto-update based on logged data
- Progress notifications (weekly)
- Milestone celebrations (10% progress, 50%, 100%)
- Recommend workouts based on goal type

## UI Layout & Components

**Tab Structure (Swipe Navigation):**

```
Fitness Tabs:
├── Dashboard (Analytics)
├── Log Workout
├── Measurements
├── Nutrition
├── Exercises
├── Templates
├── Progress Photos
├── Rest Timer
├── Goals
└── AI Coach
```

### Dashboard Tab - Analytics Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Fitness Dashboard                    │
│ [Pull to refresh ↓]                  │
├──────────────────────────────────────┤
│                                      │
│ Weekly Stats:                        │
│ Workouts: 4/5 (80%)  [████░]        │
│ Avg Duration: 45 min                 │
│ Total Time: 3.5 hrs                  │
│                                      │
│ Macros (Today):                      │
│ Calories: 2180/2500 [████░░]        │
│ Protein: 150g/150g [██████]         │
│ Carbs: 220g/250g [█████░]           │
│ Fat: 65g/80g [████░░]               │
│                                      │
│ Recent Workouts:                     │
│ Dec 18 - Chest & Triceps (52 min)   │
│ Dec 17 - Back & Biceps (48 min)     │
│ Dec 16 - Legs (55 min)              │
│                                      │
│ Goals:                               │
│ Weight Loss (180 lbs) [████░] 15%   │
│ Deadlift PR (405 lbs) [██░░░] 8%    │
│                                      │
└──────────────────────────────────────┘
```

**Components:**
- Pull-to-refresh: Swipe-down gesture on mobile, haptic feedback
- Metric cards: Grid layout, 2 columns
- Progress rings: Circular progress indicators (Recharts)
- Weekly bar chart: 7 bars for workout frequency
- Goal cards: Linear progress bars

**Backend:**
```typescript
const metrics = useFetch('getFitnessMetrics', {
  userId: currentUser.id,
  timeRange: 'week'
})
// Returns: {
//   workouts: { completed: 4, target: 5 },
//   avgDuration: 45,
//   totalTime: 210,
//   macros: { calories, protein, carbs, fat },
//   recentWorkouts: Workout[],
//   goals: Goal[]
// }
```

### Log Workout Tab - Workout Logger Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Log New Workout                      │
├──────────────────────────────────────┤
│                                      │
│ Workout Type: [Strength ▼]          │
│ Date: [Today ▼]                      │
│ Duration: [60] min                   │
│ Intensity: [High ▼]                  │
│ Calories Burned: [Auto-calc] ~500    │
│                                      │
│ Add Exercises:                       │
│ ┌──────────────────────────────────┐ │
│ │ Bench Press                      │ │
│ │ Set 1: 225 lbs × 8 reps         │ │
│ │ Set 2: 225 lbs × 8 reps         │ │
│ │ Set 3: 225 lbs × 5 reps (RPE 9) │ │
│ │ [+ Add Set] [Edit] [Delete]      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Incline Dumbbell Press           │ │
│ │ Set 1: 60 lbs × 10 reps         │ │
│ │ [+ Add Set] [Edit] [Delete]      │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Add Exercise]                     │
│                                      │
│ Notes: [Felt strong today...]        │
│                                      │
│ [Save Workout] [Save as Template]   │
└──────────────────────────────────────┘
```

**Components:**
- Type dropdown: Strength, Cardio, Flexibility, Sports
- Date picker: Mobile calendar, desktop input
- Duration input: Number field with +/- buttons
- Intensity selector: Low, Moderate, High, Very High (radio buttons)
- Exercise cards: Editable sets/reps/weight
- Notes textarea: Rich text or plain text

**Set Editor:**
```
┌────────────────────────────────────┐
│ Set 1:                             │
│ Reps: [8] Weight: [225] lbs        │
│ RPE: [9/10] (Rate of Perceived)    │
│ [Copy to next] [Remove]            │
└────────────────────────────────────┘
```

**Backend:**
```typescript
async function logWorkout(workout: WorkoutInput) {
  return await mutation('createWorkout', {
    userId: currentUser.id,
    ...workout,
    timestamp: new Date()
  })
  // Awards XP: 5-25 based on duration
  // Updates stats: workout count, total time
  // Triggers notifications if new streak
}
```

### Measurements Tab - Weight Tracker Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Body Measurements                    │
│ Last Updated: 2 days ago             │
├──────────────────────────────────────┤
│                                      │
│ Current Weight: 185 lbs              │
│ Goal Weight: 180 lbs                 │
│ Progress: [██████░░░] 5 lbs lost    │
│                                      │
│ Weight Trend (30 days):              │
│ ┌────────────────────────────────┐  │
│ │ 190                             │  │ (Line chart)
│ │ 188 ╱╲                          │  │
│ │ 186    ╲╱╲                      │  │
│ │ 184        ╲                    │  │
│ │ 182         ╱╲                  │  │
│ │ 180        ╱  ╲___              │  │
│ └────────────────────────────────┘  │
│ Days: [7] [14] [30] [90]            │ ← Range selector
│                                      │
│ Other Measurements:                  │
│ Chest: 42" (↓0.5")                  │
│ Waist: 32" (↓1.2")  ← Best progress │
│ Thighs: 21" (↑0.3")                 │
│ Biceps: 14.5" (↓0.2")               │
│                                      │
│ [+ Log New Measurement]              │
│ [+ Body Fat Test]                    │
│ [View History]                       │
│                                      │
└──────────────────────────────────────┘
```

**Components:**
- Weight display: Large number (32px font), trending arrow
- Progress ring: Circular progress to goal
- Line chart: Recharts library, responsive
- Range buttons: [7] [14] [30] [90] day ranges
- Measurement list: Inline edit, trend arrows
- Call-to-action buttons: [+ Log Measurement]

**Log Measurement Modal:**
```typescript
// Form fields:
- Weight: Number input with unit toggle (lbs/kg)
- Body Fat %: Optional number input
- Muscle Mass: Optional number input
- Date: Date picker (default: today)
- Notes: Textarea (optional)
// [Save] [Cancel]
```

**Backend:**
```typescript
async function logMeasurement(measurement: {
  weight: number,
  unit: 'lbs' | 'kg',
  bodyFat?: number,
  muscleMass?: number,
  date: Date,
  notes?: string
}) {
  return await mutation('createMeasurement', {
    userId: currentUser.id,
    ...measurement
  })
}

// Convex Query - Get weight trend
const weightTrend = useFetch('getMeasurementTrend', {
  userId: currentUser.id,
  days: selectedRange
})
```

### Nutrition Tab - Macro Tracker Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Daily Nutrition                      │
│ [Preset Goals] [Custom]              │
├──────────────────────────────────────┤
│                                      │
│ Goal: 2500 cal | 150P 250C 70F      │
│ Current: 1850 cal | 120P 180C 52F   │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Calories: [████████░░] 74%       │ │
│ │ Protein: [██████░░░░] 80%        │ │
│ │ Carbs: [███████░░░] 72%          │ │
│ │ Fat: [██████░░░░] 74%            │ │
│ └──────────────────────────────────┘ │
│                                      │
│ Logged Meals:                        │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Breakfast (08:00)                │ │
│ │ 2 Eggs, 1 Toast, 1 Coffee       │ │
│ │ 450 cal | 15P 45C 12F           │ │
│ │ [Edit] [Delete]                  │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Lunch (12:30)                    │ │
│ │ Chicken Breast, Brown Rice, Veg │ │
│ │ 650 cal | 55P 70C 18F           │ │
│ │ [Edit] [Delete]                  │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Add Meal] [Scan Barcode]         │
│ [View Detailed Macros]               │
│                                      │
└──────────────────────────────────────┘
```

**Macro Rings:**
- 4 circular progress rings (calories, protein, carbs, fat)
- Color-coded:
  - Calories: Orange
  - Protein: Blue
  - Carbs: Green
  - Fat: Pink
- Green ring: On track (80-120% of goal)
- Yellow ring: Close (60-80% or 120-150%)
- Red ring: Off track (< 60% or > 150%)

**Meal Entry:**
```
┌──────────────────────────────────────┐
│ Add Meal                             │
├──────────────────────────────────────┤
│                                      │
│ Meal Type: [Breakfast ▼]            │
│ Time: [08:00]                        │
│ Foods: [Search food...]              │
│                                      │
│ [Scan Barcode]  [Take Photo]        │
│                                      │
│ Selected Foods:                      │
│ 2 × Eggs (200g) - 155 cal           │
│ 1 × Whole Wheat Bread (30g) - 80 cal
│ 1 × Butter (10g) - 72 cal           │
│                                      │
│ Total: 307 cal | 12P 30C 8F        │
│ [Add] [Cancel]                       │
│                                      │
└──────────────────────────────────────┘
```

**Barcode Scanner:**
- Mobile camera integration
- Scan UPC code
- Auto-populate food from database
- Quantity selector
- One-tap add to meal

**Backend:**
```typescript
// Convex Query - Get daily nutrition
const dailyNutrition = useFetch('getDailyNutrition', {
  userId: currentUser.id,
  date: new Date()
})
// Returns: { meals, totals, goals, trending }

// Convex Mutation - Log meal
async function logMeal(meal: MealInput) {
  return await mutation('createMealLog', {
    userId: currentUser.id,
    ...meal,
    timestamp: new Date()
  })
}

// External API - Food search (OpenFoodFacts/Nutritionix)
const foodResults = await searchFoodDatabase(searchQuery)
```

### Rest Timer Tab - Timer Component

**Appearance:**
```
┌────────────────────────────────────┐
│ Rest Between Sets                  │
├────────────────────────────────────┤
│                                    │
│        45 : 02                     │ ← Countdown (large)
│                                    │
│  ┌──────────────────────────────┐ │
│  │████████░░░░░░░░░░░░░░░░░░░│ │ ← Progress ring
│  └──────────────────────────────┘ │
│                                    │
│  Next: Incline Dumbbell Press     │
│  (3 more sets to go)               │
│                                    │
│  [← Skip] [Add 30s →]              │
│                                    │
│  Sound: ON [🔊]                    │
│  Vibration: ON [📳]                │
│                                    │
└────────────────────────────────────┘
```

**Timer Display:**
- Font-size: 72px
- Font-family: Monospace (tabular-nums)
- Font-weight: Bold
- Color: Green (normal), Yellow (30s left), Red (time up)
- Format: MM:SS

**Progress Ring:**
- SVG circle, circumference 314px
- Stroke-width: 8px
- Animated: stroke-dasharray animated as timer counts
- Color: Primary (blue/cyan)
- Full circle = time up

**Controls:**
- Skip button: Finish timer immediately
- Add 30s button: Extend by 30 seconds
- Pause/Resume: Optional
- Sound toggle: Bell icon
- Vibration toggle: Phone vibration icon

**Notifications:**
- At 10s: Haptic buzz
- At 5s: Haptic buzz
- At 0s: Sound alert + haptic buzz + visual pulse

**Backend:**
```typescript
// Convex Mutation - Log rest period
async function completeRestPeriod(duration: number) {
  return await mutation('logRestPeriod', {
    userId: currentUser.id,
    duration,
    workoutSessionId: currentSessionId
  })
}
```

### Progress Photos Tab - Photo Gallery Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Progress Photos                      │
│ [Upload] [Compare] [Timeline]        │
├──────────────────────────────────────┤
│                                      │
│ Grid Layout (3 columns):             │
│                                      │
│ ┌──────┐ ┌──────┐ ┌──────┐          │
│ │Front │ │Front │ │Side  │          │
│ │Today │ │1 mo  │ │3 mo  │          │
│ │185lb │ │187lb │ │185lb │          │
│ └──────┘ └──────┘ └──────┘          │
│                                      │
│ ┌──────┐ ┌──────┐ ┌──────┐          │
│ │Back  │ │Front │ │Side  │          │
│ │6 mo  │ │1 yr  │ │1 yr  │          │
│ │182lb │ │180lb │ │180lb │          │
│ └──────┘ └──────┘ └──────┘          │
│                                      │
│ [+ Upload Photo]                     │
│                                      │
└──────────────────────────────────────┘
```

**Photo Card:**
- Width: 200px, Height: 250px
- Image: Responsive, aspect ratio 3:4
- Category badge: Front, Side, Back (corner)
- Date: Overlay at bottom
- Weight: Sub-text below
- Click: Open fullscreen view or before/after slider

**Upload Modal:**
```
┌──────────────────────────────────┐
│ Upload Progress Photo            │
├──────────────────────────────────┤
│                                  │
│ Category: [Front ▼]              │
│ [Take Photo] [Choose from Gallery]│
│ Weight at time: [185] lbs        │
│ Notes: [Optional...]             │
│                                  │
│ [Cancel] [Upload]                │
│                                  │
└──────────────────────────────────┘
```

**Before/After Slider:**
```
Old Photo | New Photo
    ↓ Click & drag to compare
```

**Backend:**
```typescript
async function uploadProgressPhoto(photo: {
  file: File,
  category: 'front' | 'side' | 'back',
  weight: number,
  date: Date,
  notes?: string
}) {
  // Compress image before upload
  const compressed = await compressImage(photo.file)
  return await mutation('createProgressPhoto', {
    userId: currentUser.id,
    photo_url: await file.upload(compressed),
    ...photo
  })
}

// Convex Query - Get progress photos
const photos = useFetch('getProgressPhotos', {
  userId: currentUser.id,
  category: selectedCategory || undefined
})
```

### Exercises Tab - Exercise Library Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Exercise Library                     │
│ [All] [Favorites] [Custom]           │ ← Tabs
│ Muscle: [All ▼] Equipment: [All ▼]   │ ← Filters
│ [Search exercises...]                │
├──────────────────────────────────────┤
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Bench Press           [♡ Favorite]│ │
│ │ Muscle: Chest, Triceps           │ │
│ │ Equipment: Barbell, Bench        │ │
│ │ Difficulty: Intermediate         │ │
│ │ [Watch Video] [Log Set] [Details]│ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Incline Dumbbell Press           │ │
│ │ Muscle: Upper Chest, Delts      │ │
│ │ Equipment: Dumbbells, Bench     │ │
│ │ Difficulty: Intermediate         │ │
│ │ [Watch Video] [Log Set] [Details]│ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ Create Custom Exercise]           │
│                                      │
└──────────────────────────────────────┘
```

**Exercise Card:**
- Title: Font-size 18px, bold
- Muscle groups: Badge pills, color-coded
- Equipment: Icon + text
- Difficulty: Star rating (1-5)
- Action buttons: [Watch Video] [Log] [Details]
- Favorite heart: Toggles between filled/outline

**Exercise Details Modal:**
- Video embed (YouTube)
- Form tips & cues
- Common mistakes
- Variations
- Similar exercises
- Track as favorite

**Backend:**
```typescript
// Convex Query - Get exercises
const exercises = useFetch('getExercises', {
  search: searchQuery,
  muscle: selectedMuscle || undefined,
  equipment: selectedEquipment || undefined
})

// Convex Mutation - Add to favorites
async function toggleFavoriteExercise(exerciseId: string) {
  return await mutation('toggleFavoriteExercise', {
    userId: currentUser.id,
    exerciseId
  })
}

// Convex Mutation - Create custom exercise
async function createCustomExercise(exercise: {
  name: string,
  muscles: string[],
  description: string
}) {
  return await mutation('createCustomExercise', {
    userId: currentUser.id,
    ...exercise
  })
}
```

### Goals Tab - Goal Tracking Component

**Appearance:**
```
┌──────────────────────────────────────┐
│ Fitness Goals                        │
│ [Active: 3] [Completed: 5]           │
├──────────────────────────────────────┤
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Cut to 180 lbs           [menu]  │ │
│ │ Type: Weight Loss                │ │
│ │ Target: 180 lbs | Current: 185 lbs
│ │ [████████░░░░] 50%              │ │
│ │ Deadline: Jan 31 (44 days)       │ │
│ │ [+5%] [+10%] [Custom] [Edit]    │ │
│ └──────────────────────────────────┘ │
│                                      │
│ ┌──────────────────────────────────┐ │
│ │ Deadlift PR 405 lbs              │ │
│ │ Type: Strength                   │ │
│ │ Target: 405 lbs | Current: 385 lbs
│ │ [██████░░░░░░] 40%              │ │
│ │ Deadline: Feb 15 (59 days)       │ │
│ │ [+5%] [+10%] [Custom] [Edit]    │ │
│ └──────────────────────────────────┘ │
│                                      │
│ [+ New Goal]                         │
│                                      │
└──────────────────────────────────────┘
```

**Goal Card:**
- Title: Font-size 16px, bold
- Type badge: Weight Loss, Strength, Endurance, etc.
- Target vs Current: "Target: 180 lbs | Current: 185 lbs"
- Progress bar: Animated fill, color green if on track
- Deadline: "Jan 31 (44 days)" - red if < 7 days
- Quick action buttons: [+5%] [+10%] [Custom] [Edit]

**Create Goal Dialog:**
- Goal type selector: Radio buttons
- Target value input: Number field
- Deadline picker: Date input
- Description: Textarea
- [Create Goal] button

**Backend:**
```typescript
async function createFitnessGoal(goal: {
  type: 'weight' | 'strength' | 'endurance' | 'composition',
  targetValue: number,
  unit: string,
  deadline: Date,
  description?: string
}) {
  return await mutation('createFitnessGoal', {
    userId: currentUser.id,
    ...goal,
    currentValue: getCurrentValue(goal.type)
  })
}

// Convex Query - Get active goals
const goals = useFetch('getFitnessGoals', {
  userId: currentUser.id,
  status: 'active'
})
```

## State Management

**Major State Variables:**

```typescript
// Workout logging
const [workoutType, setWorkoutType] = useState("strength");
const [workoutDuration, setWorkoutDuration] = useState(0);
const [workoutNotes, setWorkoutNotes] = useState("");
const [selectedExercises, setSelectedExercises] = useState([]);

// Nutrition
const [mealFood, setMealFood] = useState("");
const [mealQuantity, setMealQuantity] = useState("");
const [mealType, setMealType] = useState("lunch");
const [foodSearchResults, setFoodSearchResults] = useState([]);

// Measurements
const [currentWeight, setCurrentWeight] = useState("");
const [bodyFat, setBodyFat] = useState("");
const [measurementNotes, setMeasurementNotes] = useState("");

// Rest Timer
const [timerDuration, setTimerDuration] = useState(90);
const [timerRunning, setTimerRunning] = useState(false);

// Progress Photos
const [photoFile, setPhotoFile] = useState(null);
const [photoCategory, setPhotoCategory] = useState("front");
const [photoNotes, setPhotoNotes] = useState("");
```

## Convex Queries & Mutations

**Queries:**
- `api.workouts.getWorkouts` - Fetch all workouts
- `api.workouts.getWorkoutsByDate(date)` - Fetch workouts for specific date
- `api.workouts.getMeasurements` - Fetch weight/body metrics
- `api.nutrition.getMeals(date)` - Fetch meals for date
- `api.workouts.getPersonalRecords` - Fetch all PRs
- `api.workouts.getWorkoutGoals` - Fetch fitness goals

**Mutations:**
- `api.workouts.logWorkout(workout)` - Log new workout
- `api.workouts.logMeasurement(measurement)` - Log weight/body metrics
- `api.workouts.logPersonalRecord(pr)` - Log new PR
- `api.nutrition.logMeal(meal)` - Log food/macros
- `api.workouts.createWorkoutGoal(goal)` - Create fitness goal
- `api.workouts.uploadProgressPhoto(photo)` - Upload progress photo

## XP & Gamification

**XP Awards:**
- Log workout: 5-25 XP (based on duration: short=5, medium=15, long=25)
- Log PR: 50 XP + 10 XP per lb/kg improvement
- Achieve weekly workout goal: 50 XP
- Hit macro goal (3 days running): 25 XP
- Complete fitness goal: 100 XP
- Progress photo (consistency): 5 XP per month

**Streaks:**
- Daily workout streak
- Macro-tracking streak
- Weekly workout frequency (3+x/week)
- PR achievement streak

**Badges:**
- First workout logged
- 10 workouts
- 50 workouts
- First PR
- 100 pound club (deadlift)
- 50 pound club (bench press)
- Consistency champion (60-day streak)

## Mobile Features

- **Pull-to-Refresh:** Swipe to refresh analytics
- **Haptic Feedback:** On timer completion, logging success
- **Camera Integration:** Photo upload for progress pics
- **Barcode Scanner:** Scan food UPC codes for quick nutrition logging
- **Swipe Navigation:** Between fitness tabs

## Performance Optimizations

- Real-time Convex queries auto-cache and update
- Nutrition search debounced (500ms) to reduce API calls
- Macro calculations server-side (avoid client recalculation)
- Progress photo compression on upload
- Measurement history paginated (show 30 most recent)

## Accessibility

- Large buttons for workout logging (56px minimum)
- Sufficient color contrast for macro rings
- Alt text for progress photo thumbnails
- Keyboard navigation for exercise selection
- Screen reader support for charts

## Related Components

- **ExerciseLibrary:** Search and browse exercises
- **MacroTracker:** Detailed nutrition breakdown
- **WorkoutTemplates:** Save and apply workout routines
- **RestTimer:** Countdown between sets
- **ProgressPhotos:** Gallery of transformation photos
- **BarcodeScanner:** UPC code scanning
- **FoodSearchResults:** Search and select foods
- **CreateFoodForm:** Custom food creation
- **AIChatbot:** Fitness advice and motivation

## Integration Points

1. **Discipline Module:** Log workout as task
2. **Study Module:** Track study + workout balance
3. **Mind Module:** Coordinate with meditation/stoic wisdom
4. **Creator Module:** Create fitness content from progress
5. **Unified Calendar:** Show workouts on calendar
6. **Spotify Player:** Background workout music
7. **Sentinel AI:** Fitness coaching and advice

## Future Features

1. **Advanced AI:**
   - Workout program generation (linear progression, periodization)
   - Smart nutrition recommendations (based on goals)
   - Form analysis from video (planned)

2. **Social Features:**
   - Share progress photos with friends
   - Workout buddy coordination
   - Group challenges
   - Leaderboards

3. **Integrations:**
   - Apple Health/Google Fit sync
   - Fitbit/Garmin sync
   - Withings scale auto-sync (weight)
   - MyFitnessPal import

4. **Advanced Metrics:**
   - VO2 max tracking
   - Heart rate variability
   - Sleep quality correlation
   - Training stress score

## Technical Stack

- **Frontend:** React 18, TypeScript
- **Backend:** Convex (queries, mutations, file storage)
- **AI:** Groq LLM (meal recognition, program generation)
- **APIs:** OpenFoodFacts (free), Nutritionix (paid)
- **Real-time:** Convex auto-sync queries
- **Storage:** Convex file storage (progress photos)
- **UI:** shadcn/ui components, Tailwind CSS
- **Animations:** Framer Motion for progress rings
