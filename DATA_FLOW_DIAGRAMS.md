# Data Flow Diagrams

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [User Action to UI Flow](#user-action-to-ui-flow)
2. [Authentication Flow Diagram](#authentication-flow-diagram)
3. [Study Module Flow](#study-module-flow)
4. [Fitness Module Flow](#fitness-module-flow)
5. [Real-time Sync Flow](#real-time-sync-flow)
6. [File Upload Flow](#file-upload-flow)
7. [Notification Flow](#notification-flow)

---

## User Action to UI Flow

### Basic Read Operation (Query)

```
User Opens Study Dashboard
         ↓
    React Component Mounts
         ↓
    useQuery Hook Fires
    (api.study.getDecks)
         ↓
    Convex Client Sends Query
         ↓
    ┌─────────────────────┐
    │   Convex Backend    │
    │                     │
    │  Query Function:    │
    │  1. Auth Check      │
    │  2. DB Query        │
    │  3. Filter Results  │
    └─────────────────────┘
         ↓
    Real-time Subscription
    Returns Data
         ↓
    Component Re-renders
    With Decks
         ↓
    UI Updates
    Show Decks
```

### Basic Write Operation (Mutation)

```
User Clicks "Create Note"
         ↓
Form Submission Handler
         ↓
Call useMutation Hook
(api.study.createNote)
         ↓
Client Validates Input
(title, content length)
         ↓
Send Mutation to Backend
with { title, content }
         ↓
    ┌─────────────────────┐
    │   Convex Backend    │
    │                     │
    │  Mutation Function: │
    │  1. Auth Check      │
    │  2. Validate Input  │
    │  3. DB Insert       │
    │  4. XP Award        │
    │  5. Return ID       │
    └─────────────────────┘
         ↓
Loading State
(Show spinner)
         ↓
On Success:
- Show confirmation
- Update local cache
- Navigate if needed
- Clear form
         ↓
Re-render Component
```

### Error Handling Flow

```
User Action
         ↓
Try Mutation
         ↓
    ┌──────────────────────┐
    │   Convex Execution   │
    └──────────────────────┘
         ↓
    ┌──────────────────┐
    │ Error Thrown?    │
    └──────────────────┘
     /              \
   YES              NO
   /                  \
Catch Error      Mutation Success
   ↓                   ↓
Classify Error    Update UI
   ├─ Auth?       ├─ Clear form
   ├─ Validation? ├─ Show success
   ├─ Network?    ├─ Refresh data
   └─ Other?      └─ Navigate
   ↓
Show User Message
(Non-technical)
   ↓
Optional: Retry
```

---

## Authentication Flow Diagram

### OTP Email Login

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT (Browser)                       │
└─────────────────────────────────────────────────────────────┘
                           │
                    User enters email
                           │
                           ↓
                    ┌─────────────────┐
                    │ Login Form       │
                    │ [user@mail.com] │
                    └─────────────────┘
                           │
                    Click "Send OTP"
                           │
                    Validation: Valid Email?
                    YES ↓ NO → Error message
                           │
            ┌──────────────────────────────────┐
            │  Send Mutation: requestOTP       │
            │  - email                         │
            │  - timestamp                     │
            └──────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────────┐
        │      BACKEND (Convex Functions)      │
        │                                      │
        │  1. Validate email format            │
        │  2. Check rate limit                 │
        │     (max 3 per hour)                 │
        │  3. Generate 6-digit code            │
        │  4. Store in DB (10 min expiry)      │
        │  5. Send via Email Service           │
        │  6. Return success                   │
        └──────────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────┐
        │   Email Service (Resend/SendGrid) │
        │                                   │
        │  Send HTML Email with OTP         │
        │  "Your code: 123456"              │
        └──────────────────────────────────┘
                           │
                           ↓
        User receives email, copies code
                           │
                           ↓
        ┌──────────────────────────────────┐
        │  OTP Verification Form           │
        │  [1][2][3][4][5][6]              │
        │          [Verify]                │
        └──────────────────────────────────┘
                           │
                    Click "Verify"
                           │
            ┌──────────────────────────────────┐
            │  Send Mutation: verifyOTP        │
            │  - email                         │
            │  - code                          │
            └──────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────────┐
        │      BACKEND Verification            │
        │                                      │
        │  1. Validate code exists              │
        │  2. Check expiration                 │
        │  3. Match with email                 │
        │  4. Check attempt limit              │
        │  5. Mark OTP as used                 │
        │  6. Create/update user               │
        │  7. Generate JWT token               │
        │  8. Create session                   │
        │  9. Return user data + token         │
        └──────────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────┐
        │  CLIENT: Save Token              │
        │  localStorage.setItem(            │
        │    'jwt_token',                  │
        │    token                         │
        │  )                               │
        │  localStorage.setItem(            │
        │    'user_id',                    │
        │    user._id                      │
        │  )                               │
        └──────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────┐
        │  Set Convex Auth Context         │
        │  useAuthStore.setAuth({          │
        │    user,                         │
        │    token,                        │
        │  })                              │
        └──────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────┐
        │  Redirect to Dashboard           │
        │  / (Home) → /dashboard           │
        └──────────────────────────────────┘
                           │
                           ↓
        ┌──────────────────────────────────┐
        │  Dashboard Loads                 │
        │  useQuery hooks fetch data       │
        │  with authenticated token        │
        └──────────────────────────────────┘
```

---

## Study Module Flow

### Flashcard Review Cycle

```
┌────────────────────────────────────────────┐
│     User on Study Dashboard                │
└────────────────────────────────────────────┘
           │
           │ Select Deck
           ↓
┌────────────────────────────────────────────┐
│  Query: getFlashcardsDueForReview          │
│  Parameters: { deckId }                    │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  BACKEND: Query Execution                  │
│  1. Get user ID from auth                  │
│  2. Find deck (check ownership)            │
│  3. Query cards WHERE nextReviewDate < now │
│  4. Sort by interval ascending             │
│  5. Return 50 cards max                    │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  Frontend: Display First Card              │
│  - Front side visible                      │
│  - Progress shown (1/50)                   │
└────────────────────────────────────────────┘
           │
           │ User presses Space to flip
           ↓
┌────────────────────────────────────────────┐
│  Show Back Side (Answer)                   │
│  - Smooth animation                        │
│  - Haptic feedback                         │
└────────────────────────────────────────────┘
           │
           │ User rates quality (1-5)
           ↓
┌────────────────────────────────────────────┐
│  Mutation: reviewFlashcard                 │
│  Parameters:                               │
│  - cardId                                  │
│  - quality (1-5)                           │
│  - timestamp                               │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  BACKEND: SM-2 Calculation                 │
│                                            │
│  IF quality < 3:                           │
│  - nextInterval = 1 day                    │
│  - repetitions = 1                         │
│  - easiness = max(1.3, ease - 0.2)         │
│                                            │
│  ELSE (quality >= 3):                      │
│  - IF repetitions == 0:                    │
│    interval = 1                            │
│  - ELSE IF repetitions == 1:               │
│    interval = 3                            │
│  - ELSE:                                   │
│    interval = prev * easiness              │
│  - repetitions += 1                        │
│  - easiness = ease + 0.1 - (5-q) * 0.08    │
│                                            │
│  nextReviewDate = now + interval days      │
│  totalReviews += 1                         │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  BACKEND: Update Records                   │
│  1. Update card review data in DB          │
│  2. Award XP based on quality              │
│  3. Check for milestones                   │
│  4. Update user stats                      │
│  5. Return success                         │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  Frontend: Show Feedback                   │
│  - Haptic: success pattern                 │
│  - Animation: card flip                    │
│  - XP popup: "+20 XP"                      │
│  - Streak update                           │
└────────────────────────────────────────────┘
           │
           │ Move to Next Card
           ↓
┌────────────────────────────────────────────┐
│  Update Progress                           │
│  - Progress bar: 2/50                      │
│  - Remaining cards: 48                     │
│  - Load next card                          │
└────────────────────────────────────────────┘
           │
           │ Loop for remaining cards
           └─→ Repeat review cycle
```

---

## Real-time Sync Flow

### Convex Real-time Updates

```
┌────────────────────────────────────────────┐
│  User A: Updates Note Title                │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  Mutation: updateNote                      │
│  { noteId, title: "New Title" }            │
└────────────────────────────────────────────┘
           │
           ↓
┌────────────────────────────────────────────┐
│  CONVEX: Process Mutation                  │
│  1. Update DB                              │
│  2. Trigger subscription updates           │
│  3. Broadcast to all subscribers           │
└────────────────────────────────────────────┘
           │
           ├─→ User A Receives Update
           │   - Reflects immediately
           │   - Optimistic update shown
           │
           ├─→ User B Listening to Same Note
           │   - Gets real-time update
           │   - useQuery automatically syncs
           │   - Component re-renders
           │
           └─→ Other Users Not Listening
               - No unnecessary updates sent
               - Only those subscribed get data
```

---

## File Upload Flow

### Progress Photo Upload

```
User Opens Fitness Module
         │
         ↓
   Click "Take Photo"
         │
         ├─→ Request camera permission
         │   (Browser API: getUserMedia)
         │
         ↓
   Capture Photo
   (Canvas → Blob)
         │
         ↓
         ┌──────────────────────────────┐
         │  Prepare Upload              │
         │  1. Compress image           │
         │  2. Create FormData          │
         │  3. Add metadata             │
         └──────────────────────────────┘
         │
         ↓
   Call Mutation: uploadProgressPhoto
         │
         ↓
    ┌────────────────────────────────────┐
    │  BACKEND: Store File               │
    │                                    │
    │  1. Validate file                  │
    │     (size, format)                 │
    │  2. Generate unique filename       │
    │  3. Upload to storage              │
    │  4. Create record in photos table  │
    │  5. Return URL                     │
    └────────────────────────────────────┘
         │
         ↓
    Update Progress Photos Gallery
         │
         ↓
    Show Success Message
    Award 50 XP
```

---

## Notification Flow

### Badge Awarded Notification

```
User Action Completed
(e.g., 100 workouts)
         │
         ↓
   Mutation Handler
   Checks Badge Conditions
         │
         ↓
   Criteria Met?
   (100 workouts logged)
         │
   YES  ↓
         │
    Award Badge
    Mutation: awardBadge
         │
         ↓
    ┌──────────────────────────────┐
    │  BACKEND                     │
    │  1. Create badge record      │
    │  2. Award XP                 │
    │  3. Trigger notification     │
    └──────────────────────────────┘
         │
         ├─→ In-App Notification
         │   - Toast/Alert
         │   - Badge icon
         │   - Celebration animation
         │
         ├─→ Push Notification
         │   - Service Worker
         │   - "You earned a badge!"
         │   - Click to view
         │
         └─→ Email Notification
             - Send template email
             - Share on social (optional)
```

---

## Offline Flow

### Offline Task Completion

```
User Offline
(No internet)
         │
         ↓
   Complete Task
   (Check off item)
         │
         ↓
   useOfflineSync Hook
         │
         ↓
   ┌─────────────────────────────┐
   │  Check if Online            │
   │  navigator.onLine === false │
   └─────────────────────────────┘
         │
   NO ↓
         │
   Queue Action Locally
   localStorage.setItem(
     'pending-actions',
     JSON.stringify([...])
   )
         │
         ↓
   Show "Syncing..." Badge
   on Task Item
         │
         │ ... User Goes Online ...
         │
         ↓
   'online' Event Fires
         │
         ↓
   useOfflineSync Detects
         │
         ↓
   Sync Pending Actions
   forEach action:
   1. Execute mutation
   2. Remove from queue
   3. Show status
         │
         ↓
   Update UI
   - Remove "Syncing" badge
   - Task fully synced
   - Streak updated
   - XP awarded
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Architecture & Engineering Teams
