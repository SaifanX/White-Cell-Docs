# Mobile Features Deep Dive

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Responsive Design](#responsive-design)
2. [Touch Interactions](#touch-interactions)
3. [Mobile Gestures](#mobile-gestures)
4. [Haptic Feedback](#haptic-feedback)
5. [Push Notifications](#push-notifications)
6. [Offline Functionality](#offline-functionality)
7. [Camera Integration](#camera-integration)
8. [Mobile Performance](#mobile-performance)
9. [Device Compatibility](#device-compatibility)

---

## Responsive Design

### Breakpoints

```
Mobile-first approach (design for mobile, enhance for desktop)

xs: 320px (iPhone SE)
sm: 375px (iPhone 12/13)
md: 768px (iPad mini)
lg: 1024px (iPad/Desktop)
xl: 1280px (Desktop)
2xl: 1536px (Large desktop)
```

### Mobile Layout Adjustments

#### Study Module Mobile

```
Desktop:
┌─────────────────┐
│  Flashcard      │
│                 │
├─────────────────┤
│ 1: F 2: H 3: Ok │
│ 4: G 5: E       │
└─────────────────┘

Mobile:
┌──────────────────┐
│   Flashcard      │
│   (Full height)  │
├──────────────────┤
│ [1:F][2:H][3:Ok] │
│ [4:G][5:E]       │
└──────────────────┘
```

#### Fitness Module Mobile

```
Workout Logger - Desktop:
┌────────────────────────────────────┐
│ Exercise | Sets | Reps | Weight | | 
├────────────────────────────────────┤
│ Bench    │ 4   │ 8   │ 100kg  | E │
│ Squat    │ 4   │ 10  │ 150kg  | E │
└────────────────────────────────────┘

Workout Logger - Mobile:
┌──────────────────────┐
│ Bench Press          │
├──────────────────────┤
│ Sets: 4              │
│ Reps: 8              │
│ Weight: 100kg        │
├──────────────────────┤
│ [Delete] [Save]      │
└──────────────────────┘
```

### CSS Grid Adjustments

```css
/* Desktop */
.workout-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
}

/* Mobile */
@media (max-width: 640px) {
  .workout-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 0.5rem;
  }
}

/* Tablet */
@media (max-width: 1024px) and (min-width: 640px) {
  .workout-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 0.75rem;
  }
}
```

---

## Touch Interactions

### Touch Targets

**Minimum Touch Target Size:** 44x44px (iOS) / 48x48px (Android)

```typescript
// components/TouchButton.tsx
const TouchButton = styled.button`
  /* Minimum size for touch */
  min-height: 44px;
  min-width: 44px;
  padding: 12px 16px;
  
  /* Comfortable size */
  font-size: 16px; /* Prevents auto-zoom on iOS */
  
  /* Touch feedback */
  &:active {
    opacity: 0.8;
    transform: scale(0.98);
  }
  
  @media (pointer: fine) {
    /* Hover only on devices with hover support (desktop) */
    &:hover {
      background-color: var(--color-primary-dark);
    }
  }
`;
```

### Tap Feedback

```typescript
// hooks/useTapFeedback.ts
export function useTapFeedback() {
  const [tappedId, setTappedId] = useState<string | null>(null);
  
  const handleTapStart = (id: string) => {
    setTappedId(id);
  };
  
  const handleTapEnd = () => {
    setTappedId(null);
  };
  
  return {
    tappedId,
    handleTapStart,
    handleTapEnd,
    isTapped: (id: string) => tappedId === id,
  };
}

// Component usage
function FlashcardCard({ card }: { card: Flashcard }) {
  const { isTapped, handleTapStart, handleTapEnd } = useTapFeedback();
  
  return (
    <div
      onTouchStart={() => handleTapStart(card._id)}
      onTouchEnd={handleTapEnd}
      onClick={() => handleTapStart(card._id)}
      style={{
        transform: isTapped(card._id) ? 'scale(0.98)' : 'scale(1)',
        opacity: isTapped(card._id) ? 0.8 : 1,
        transition: 'all 150ms ease-out',
      }}
    >
      {card.question}
    </div>
  );
}
```

---

## Mobile Gestures

### Swipe Detection

```typescript
// hooks/useSwipe.ts
interface SwipeHandlers {
  onSwipeLeft?: () => void;
  onSwipeRight?: () => void;
  onSwipeUp?: () => void;
  onSwipeDown?: () => void;
}

export function useSwipe({
  onSwipeLeft,
  onSwipeRight,
  onSwipeUp,
  onSwipeDown,
}: SwipeHandlers) {
  const [touchStart, setTouchStart] = useState<{
    x: number;
    y: number;
  } | null>(null);
  
  const handleTouchStart = (e: TouchEvent) => {
    const touch = e.touches[0];
    setTouchStart({ x: touch.clientX, y: touch.clientY });
  };
  
  const handleTouchEnd = (e: TouchEvent) => {
    if (!touchStart) return;
    
    const touch = e.changedTouches[0];
    const deltaX = touch.clientX - touchStart.x;
    const deltaY = touch.clientY - touchStart.y;
    const distance = Math.sqrt(deltaX ** 2 + deltaY ** 2);
    
    if (distance < 50) return; // Minimum swipe distance
    
    // Determine direction
    if (Math.abs(deltaX) > Math.abs(deltaY)) {
      // Horizontal swipe
      if (deltaX > 0) onSwipeRight?.();
      else onSwipeLeft?.();
    } else {
      // Vertical swipe
      if (deltaY > 0) onSwipeDown?.();
      else onSwipeUp?.();
    }
    
    setTouchStart(null);
  };
  
  return { handleTouchStart, handleTouchEnd };
}

// Usage
function StudySession() {
  const { handleTouchStart, handleTouchEnd } = useSwipe({
    onSwipeLeft: () => moveToNextCard(),
    onSwipeRight: () => moveToPreviousCard(),
  });
  
  return (
    <div
      onTouchStart={handleTouchStart as any}
      onTouchEnd={handleTouchEnd as any}
      className="flashcard-container"
    >
      {/* Flashcard content */}
    </div>
  );
}
```

### Long Press Detection

```typescript
export function useLongPress(
  callback: () => void,
  delay: number = 500
) {
  const pressTimer = useRef<ReturnType<typeof setTimeout> | null>(null);
  
  const handleTouchStart = () => {
    pressTimer.current = setTimeout(callback, delay);
  };
  
  const handleTouchEnd = () => {
    if (pressTimer.current) {
      clearTimeout(pressTimer.current);
    }
  };
  
  return {
    onTouchStart: handleTouchStart,
    onTouchEnd: handleTouchEnd,
    onMouseDown: handleTouchStart,
    onMouseUp: handleTouchEnd,
  };
}

// Usage - long press to delete
function NoteItem({ note }: { note: Note }) {
  const { onTouchStart, onTouchEnd } = useLongPress(() => {
    showConfirm('Delete this note?', () => deleteNote(note._id));
  });
  
  return (
    <div
      className="note-item"
      onTouchStart={onTouchStart}
      onTouchEnd={onTouchEnd}
      onMouseDown={onTouchStart}
      onMouseUp={onTouchEnd}
    >
      {note.title}
    </div>
  );
}
```

---

## Haptic Feedback

### Vibration API

```typescript
// lib/haptics.ts
export enum HapticPattern {
  LIGHT = 'light',      // 10ms
  MEDIUM = 'medium',    // 20ms
  HEAVY = 'heavy',      // 30ms
  SUCCESS = 'success',  // 50ms double tap
  ERROR = 'error',      // Warning pattern
  WARNING = 'warning',  // Alert pattern
}

export function triggerHaptic(pattern: HapticPattern) {
  if (!navigator.vibrate) {
    console.warn('Vibration API not supported');
    return;
  }
  
  const patterns: Record<HapticPattern, number | number[]> = {
    [HapticPattern.LIGHT]: 10,
    [HapticPattern.MEDIUM]: 20,
    [HapticPattern.HEAVY]: 30,
    [HapticPattern.SUCCESS]: [50, 20, 50],  // Tap, pause, tap
    [HapticPattern.ERROR]: [20, 30, 20, 30, 20],
    [HapticPattern.WARNING]: [50, 100, 50],
  };
  
  navigator.vibrate(patterns[pattern]);
}

// Hook for components
export function useHaptic() {
  return {
    triggerLight: () => triggerHaptic(HapticPattern.LIGHT),
    triggerMedium: () => triggerHaptic(HapticPattern.MEDIUM),
    triggerHeavy: () => triggerHaptic(HapticPattern.HEAVY),
    triggerSuccess: () => triggerHaptic(HapticPattern.SUCCESS),
    triggerError: () => triggerHaptic(HapticPattern.ERROR),
    triggerWarning: () => triggerHaptic(HapticPattern.WARNING),
  };
}
```

### Integration with User Actions

```typescript
function FlashcardReview({ card }: { card: Flashcard }) {
  const { triggerSuccess, triggerMedium } = useHaptic();
  
  const handleReview = async (quality: number) => {
    // Haptic for selection
    triggerMedium();
    
    // Submit review
    try {
      await reviewCard(card._id, quality);
      
      // Haptic for success
      if (quality >= 4) {
        triggerSuccess();
      }
    } catch (err) {
      // Haptic for error handled elsewhere
    }
  };
  
  return (
    <div className="review-buttons">
      <button onClick={() => handleReview(1)}>Fail</button>
      <button onClick={() => handleReview(2)}>Hard</button>
      <button onClick={() => handleReview(3)}>OK</button>
      <button onClick={() => handleReview(4)}>Good</button>
      <button onClick={() => handleReview(5)}>Easy</button>
    </div>
  );
}
```

---

## Push Notifications

### Web Push Configuration

```typescript
// lib/notifications.ts
export async function requestNotificationPermission() {
  if (!('Notification' in window)) {
    console.log('This browser does not support notifications');
    return false;
  }
  
  if (Notification.permission === 'granted') {
    return true;
  }
  
  if (Notification.permission !== 'denied') {
    const permission = await Notification.requestPermission();
    return permission === 'granted';
  }
  
  return false;
}

export function sendLocalNotification(
  title: string,
  options?: NotificationOptions
) {
  if ('serviceWorker' in navigator && 'Notification' in window) {
    navigator.serviceWorker.ready.then(registration => {
      registration.showNotification(title, options);
    });
  }
}

// Register for push notifications
export async function subscribeToNotifications() {
  const registration = await navigator.serviceWorker.ready;
  const subscription = await registration.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: process.env.REACT_APP_VAPID_PUBLIC_KEY,
  });
  
  // Send subscription to server
  await fetch('/api/notifications/subscribe', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(subscription),
  });
  
  return subscription;
}
```

### Notification Triggers

```typescript
// Convex action
export const sendStudyReminder = async (
  ctx: ActionCtx,
  args: { userId: string }
) => {
  const user = await ctx.runQuery(api.users.getUserById, {
    userId: args.userId,
  });
  
  if (!user) return;
  
  // Get number of cards due
  const dueCards = await ctx.runQuery(api.study.getFlashcardsDueForReview, {
    deckId: user.lastActiveDeckId,
  });
  
  if (dueCards.cards.length === 0) return;
  
  // Send push notification
  await fetch('https://push-service.example.com/send', {
    method: 'POST',
    body: JSON.stringify({
      userId: args.userId,
      title: '📚 Time to Review',
      body: `You have ${dueCards.cards.length} cards to review`,
      icon: '/study-icon.png',
      badge: '/badge.png',
      data: {
        click_action: '/study',
      },
    }),
  });
};
```

### Notification Types

| Type | Trigger | Message | Action |
|------|---------|---------|--------|
| Study Reminder | 2 cards due | "5 cards due for review" | Open Study |
| Workout Reminder | 24h since last | "Time for a workout?" | Log Workout |
| Streak Warning | 1 day before loss | "Your streak ends tomorrow!" | Complete Task |
| Achievement | Goal reached | "You earned a badge!" | View Badge |
| Community | Friend activity | "Friend logged workout" | View Profile |

---

## Offline Functionality

### Service Worker Setup

```typescript
// public/sw.js
const CACHE_NAME = 'white-cell-v1';
const STATIC_ASSETS = [
  '/',
  '/index.html',
  '/manifest.json',
  '/favicon.ico',
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      return cache.addAll(STATIC_ASSETS);
    })
  );
});

self.addEventListener('fetch', event => {
  const { request } = event;
  const url = new URL(request.url);
  
  // API requests - network first, fallback to cache
  if (url.pathname.startsWith('/api')) {
    event.respondWith(
      fetch(request)
        .then(response => {
          const clone = response.clone();
          caches.open(CACHE_NAME).then(cache => {
            cache.put(request, clone);
          });
          return response;
        })
        .catch(() => {
          return caches.match(request);
        })
    );
  } else {
    // Static assets - cache first
    event.respondWith(
      caches.match(request).then(response => {
        return response || fetch(request);
      })
    );
  }
});
```

### Offline Data Sync

```typescript
// hooks/useOfflineSync.ts
export function useOfflineSync() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [pendingActions, setPendingActions] = useState<any[]>([]);
  
  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true);
      syncPendingActions();
    };
    
    const handleOffline = () => {
      setIsOnline(false);
    };
    
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  
  const queueAction = async (
    action: () => Promise<any>,
    metadata: any
  ) => {
    if (isOnline) {
      return await action();
    } else {
      const item = { action, metadata, id: Date.now() };
      setPendingActions(prev => [...prev, item]);
      localStorage.setItem('pending-actions', JSON.stringify(pendingActions));
      return item.id;
    }
  };
  
  const syncPendingActions = async () => {
    const stored = localStorage.getItem('pending-actions');
    if (!stored) return;
    
    const actions = JSON.parse(stored);
    
    for (const item of actions) {
      try {
        await item.action();
        // Remove from pending
        setPendingActions(prev => 
          prev.filter(a => a.id !== item.id)
        );
      } catch (err) {
        console.error('Sync failed:', err);
      }
    }
  };
  
  return { isOnline, pendingActions, queueAction };
}
```

---

## Camera Integration

### Photo Capture

```typescript
// hooks/useCamera.ts
export function useCamera() {
  const videoRef = useRef<HTMLVideoElement>(null);
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const [isActive, setIsActive] = useState(false);
  
  const startCamera = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: 'user' },
        audio: false,
      });
      
      if (videoRef.current) {
        videoRef.current.srcObject = stream;
        setIsActive(true);
      }
    } catch (err) {
      console.error('Camera access denied:', err);
    }
  };
  
  const takePhoto = (): Blob | null => {
    if (!videoRef.current || !canvasRef.current) return null;
    
    const context = canvasRef.current.getContext('2d');
    if (!context) return null;
    
    canvasRef.current.width = videoRef.current.videoWidth;
    canvasRef.current.height = videoRef.current.videoHeight;
    
    context.drawImage(
      videoRef.current,
      0,
      0,
      canvasRef.current.width,
      canvasRef.current.height
    );
    
    return new Promise(resolve => {
      canvasRef.current?.toBlob(resolve, 'image/jpeg', 0.95);
    });
  };
  
  const stopCamera = () => {
    const stream = videoRef.current?.srcObject as MediaStream;
    stream?.getTracks().forEach(track => track.stop());
    setIsActive(false);
  };
  
  return { videoRef, canvasRef, isActive, startCamera, takePhoto, stopCamera };
}
```

### Progress Photo Upload

```typescript
function ProgressPhotoCapture() {
  const { videoRef, canvasRef, isActive, startCamera, takePhoto, stopCamera } = useCamera();
  const uploadMutation = useMutation(api.fitness.uploadProgressPhoto);
  
  const handleCapture = async () => {
    const blob = await takePhoto();
    if (!blob) return;
    
    // Upload
    try {
      const url = await uploadMutation({ blob });
      toast.success('Photo uploaded!');
    } catch (err) {
      toast.error('Upload failed');
    }
  };
  
  return (
    <div>
      {!isActive ? (
        <button onClick={startCamera}>Start Camera</button>
      ) : (
        <>
          <video ref={videoRef} autoPlay playsInline />
          <canvas ref={canvasRef} hidden />
          <button onClick={handleCapture}>Capture</button>
          <button onClick={stopCamera}>Stop</button>
        </>
      )}
    </div>
  );
}
```

---

## Mobile Performance

### Image Optimization

```typescript
// lib/imageOptimization.ts
export function getResponsiveImageSrc(
  imagePath: string,
  width: number
): string {
  // Use CDN with responsive image transformation
  return `https://cdn.example.com/image/${imagePath}?w=${width}&fit=cover`;
}

// Component
function OptimizedImage({
  src,
  alt,
}: {
  src: string;
  alt: string;
}) {
  return (
    <picture>
      <source
        media="(max-width: 640px)"
        srcSet={getResponsiveImageSrc(src, 640)}
      />
      <source
        media="(max-width: 1024px)"
        srcSet={getResponsiveImageSrc(src, 1024)}
      />
      <img
        src={getResponsiveImageSrc(src, 1920)}
        alt={alt}
        loading="lazy"
      />
    </picture>
  );
}
```

### Battery Optimization

```typescript
// hooks/useBatteryStatus.ts
export function useBatteryStatus() {
  const [battery, setBattery] = useState<BatteryStatus | null>(null);
  
  useEffect(() => {
    if (!('getBattery' in navigator)) return;
    
    (navigator as any).getBattery().then((bm: BatteryManager) => {
      setBattery({
        level: bm.level,
        charging: bm.charging,
        chargingTime: bm.chargingTime,
        dischargingTime: bm.dischargingTime,
      });
      
      bm.addEventListener('levelchange', () => {
        setBattery(prev => prev ? { ...prev, level: bm.level } : null);
      });
    });
  }, []);
  
  return battery;
}

// Usage - reduce features on low battery
function StudySession() {
  const battery = useBatteryStatus();
  const isLowBattery = battery && battery.level < 0.2;
  
  return (
    <div>
      {isLowBattery && (
        <Banner>Low battery - disabling animations</Banner>
      )}
    </div>
  );
}
```

---

## Device Compatibility

### iOS vs Android Differences

| Feature | iOS | Android |
|---------|-----|---------|
| Vibration | Limited (10-30ms) | Full support |
| Push Notifications | WKWebView support | Full APK support |
| Camera | User permission | User permission |
| Offline | Service Worker | Service Worker |
| Status Bar | Light/Dark modes | Configurable |
| Keyboard Safe Area | iPhone X+ notch | Variable |

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Mobile & Engineering Teams
