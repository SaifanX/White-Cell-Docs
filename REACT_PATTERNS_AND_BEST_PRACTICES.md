# React Patterns & Best Practices Guide

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Custom Hooks](#custom-hooks)
2. [Component Patterns](#component-patterns)
3. [State Management](#state-management)
4. [Performance Optimization](#performance-optimization)
5. [Error Handling](#error-handling)
6. [Code Organization](#code-organization)
7. [Testing Patterns](#testing-patterns)
8. [Anti-Patterns to Avoid](#anti-patterns)

---

## Custom Hooks

### useAuth Hook

**Purpose:** Centralized authentication state and methods

```typescript
// hooks/useAuth.ts
import { useQuery, useMutation } from 'convex/react';
import { api } from '@/convex/_generated/api';
import { useEffect, useState } from 'react';

export function useAuth() {
  const user = useQuery(api.users.getCurrentUser);
  const requestOtp = useMutation(api.auth.requestOTP);
  const verifyOtp = useMutation(api.auth.verifyOTP);
  const logout = useMutation(api.auth.logout);
  
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const handleRequestOtp = async (email: string) => {
    setIsLoading(true);
    setError(null);
    try {
      await requestOtp({ email });
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to send OTP');
      throw err;
    } finally {
      setIsLoading(false);
    }
  };
  
  const handleVerifyOtp = async (email: string, code: string) => {
    setIsLoading(true);
    setError(null);
    try {
      const result = await verifyOtp({ email, code });
      return result;
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Invalid OTP');
      throw err;
    } finally {
      setIsLoading(false);
    }
  };
  
  const handleLogout = async () => {
    setIsLoading(true);
    try {
      await logout();
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Logout failed');
    } finally {
      setIsLoading(false);
    }
  };
  
  return {
    user,
    isAuthenticated: !!user,
    isLoading,
    error,
    requestOtp: handleRequestOtp,
    verifyOtp: handleVerifyOtp,
    logout: handleLogout,
  };
}
```

**Usage:**
```typescript
function MyComponent() {
  const { user, isAuthenticated, requestOtp } = useAuth();
  
  if (!isAuthenticated) {
    return <LoginForm />;
  }
  
  return <Dashboard user={user} />;
}
```

### useFlashcards Hook

**Purpose:** Flashcard study session management

```typescript
export function useFlashcards(deckId: string) {
  const cards = useQuery(api.study.getFlashcardsDueForReview, { deckId });
  const reviewCard = useMutation(api.study.reviewFlashcard);
  
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isFlipped, setIsFlipped] = useState(false);
  const [reviewHistory, setReviewHistory] = useState<ReviewRecord[]>([]);
  
  const currentCard = cards?.[currentIndex];
  
  const handleReview = async (quality: 1 | 2 | 3 | 4 | 5) => {
    if (!currentCard) return;
    
    try {
      await reviewCard({
        cardId: currentCard._id,
        quality,
      });
      
      setReviewHistory(prev => [...prev, {
        cardId: currentCard._id,
        quality,
        timestamp: Date.now(),
      }]);
      
      // Move to next card
      if (currentIndex < (cards?.length ?? 0) - 1) {
        setCurrentIndex(prev => prev + 1);
        setIsFlipped(false);
      }
    } catch (err) {
      console.error('Review failed:', err);
    }
  };
  
  const progress = cards ? 
    ((currentIndex + 1) / cards.length * 100) : 0;
  
  return {
    currentCard,
    isFlipped,
    setIsFlipped,
    handleReview,
    progress,
    remaining: (cards?.length ?? 0) - currentIndex - 1,
    reviewHistory,
  };
}
```

### useTheme Hook

**Purpose:** Theme management with persistence

```typescript
export function useTheme() {
  const [theme, setTheme] = useState<Theme>(() => {
    // Load from localStorage
    const saved = localStorage.getItem('theme');
    if (saved) return saved as Theme;
    
    // Check system preference
    if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
      return 'dark';
    }
    return 'light';
  });
  
  useEffect(() => {
    // Apply theme to document
    const root = document.documentElement;
    root.classList.remove('light', 'dark');
    root.classList.add(theme);
    root.style.colorScheme = theme;
    
    // Save to localStorage
    localStorage.setItem('theme', theme);
  }, [theme]);
  
  const toggleTheme = useCallback(() => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  }, []);
  
  return { theme, toggleTheme, setTheme };
}
```

### useForm Hook

**Purpose:** Form state and validation

```typescript
export function useForm<T extends Record<string, any>>(
  initialValues: T,
  onSubmit: (values: T) => Promise<void>,
  validate?: (values: T) => Partial<Record<keyof T, string>>
) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const handleChange = useCallback((e: ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value, type } = e.target;
    setValues(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? (e.target as HTMLInputElement).checked : value,
    }));
  }, []);
  
  const handleBlur = useCallback((e: FocusEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    setTouched(prev => ({
      ...prev,
      [e.target.name]: true,
    }));
  }, []);
  
  const handleSubmit = useCallback(async (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    
    if (validate) {
      const validationErrors = validate(values);
      setErrors(validationErrors);
      
      if (Object.keys(validationErrors).length > 0) {
        return;
      }
    }
    
    setIsSubmitting(true);
    try {
      await onSubmit(values);
    } catch (err) {
      console.error('Submit error:', err);
    } finally {
      setIsSubmitting(false);
    }
  }, [values, validate, onSubmit]);
  
  const resetForm = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);
  
  return {
    values,
    errors,
    touched,
    isSubmitting,
    handleChange,
    handleBlur,
    handleSubmit,
    resetForm,
    setValues,
    setFieldValue: (field: keyof T, value: any) => {
      setValues(prev => ({ ...prev, [field]: value }));
    },
  };
}
```

### useApi Hook

**Purpose:** API call management with loading/error states

```typescript
export function useApi<T, Args extends any[] = []>(
  fn: (...args: Args) => Promise<T>,
  options?: { immediate?: boolean; onError?: (err: Error) => void }
) {
  const [data, setData] = useState<T | null>(null);
  const [isLoading, setIsLoading] = useState(options?.immediate ?? false);
  const [error, setError] = useState<Error | null>(null);
  
  const execute = useCallback(async (...args: Args) => {
    setIsLoading(true);
    setError(null);
    try {
      const result = await fn(...args);
      setData(result);
      return result;
    } catch (err) {
      const error = err instanceof Error ? err : new Error(String(err));
      setError(error);
      options?.onError?.(error);
      throw error;
    } finally {
      setIsLoading(false);
    }
  }, [fn, options]);
  
  useEffect(() => {
    if (options?.immediate) {
      execute();
    }
  }, [execute, options?.immediate]);
  
  const reset = useCallback(() => {
    setData(null);
    setError(null);
  }, []);
  
  return { data, isLoading, error, execute, reset };
}
```

---

## Component Patterns

### Compound Components

**Pattern:** Build flexible, composable components with shared state

```typescript
// Timer compound component
const Timer = ({ children }: { children: ReactNode }) => {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  
  useEffect(() => {
    if (!isRunning) return;
    const interval = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(interval);
  }, [isRunning]);
  
  return (
    <TimerContext.Provider value={{
      seconds,
      isRunning,
      start: () => setIsRunning(true),
      stop: () => setIsRunning(false),
      reset: () => { setSeconds(0); setIsRunning(false); },
    }}>
      {children}
    </TimerContext.Provider>
  );
};

Timer.Display = function TimerDisplay() {
  const { seconds } = useContext(TimerContext);
  const minutes = Math.floor(seconds / 60);
  const secs = seconds % 60;
  return <span>{minutes}:{String(secs).padStart(2, '0')}</span>;
};

Timer.StartButton = function TimerStartButton() {
  const { isRunning, start } = useContext(TimerContext);
  return (
    <button onClick={start} disabled={isRunning}>
      Start
    </button>
  );
};

Timer.StopButton = function TimerStopButton() {
  const { isRunning, stop } = useContext(TimerContext);
  return (
    <button onClick={stop} disabled={!isRunning}>
      Stop
    </button>
  );
};

// Usage
<Timer>
  <Timer.Display />
  <Timer.StartButton />
  <Timer.StopButton />
</Timer>
```

### Render Props

**Pattern:** Share component logic via render function prop

```typescript
interface RenderPropsArgs<T> {
  data: T | null;
  isLoading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
}

function DataFetcher<T>({
  fetch,
  children,
}: {
  fetch: () => Promise<T>;
  children: (args: RenderPropsArgs<T>) => ReactNode;
}) {
  const [data, setData] = useState<T | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  
  const refetch = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      const result = await fetch();
      setData(result);
    } catch (err) {
      setError(err instanceof Error ? err : new Error(String(err)));
    } finally {
      setIsLoading(false);
    }
  }, [fetch]);
  
  useEffect(() => {
    refetch();
  }, [refetch]);
  
  return children({ data, isLoading, error, refetch });
}

// Usage
<DataFetcher fetch={() => api.study.getNotes()}>
  {({ data, isLoading, error }) => (
    <>
      {isLoading && <Spinner />}
      {error && <Error message={error.message} />}
      {data && <NotesList notes={data} />}
    </>
  )}
</DataFetcher>
```

### Controlled vs Uncontrolled Components

**Controlled Component:**
```typescript
function SearchBox({ value, onChange }: {
  value: string;
  onChange: (value: string) => void;
}) {
  return (
    <input
      type="text"
      value={value}
      onChange={(e) => onChange(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

**Uncontrolled Component:**
```typescript
function SearchBox({ onSearch }: {
  onSearch: (value: string) => void;
}) {
  const inputRef = useRef<HTMLInputElement>(null);
  
  return (
    <input
      ref={inputRef}
      type="text"
      defaultValue=""
      onKeyDown={(e) => {
        if (e.key === 'Enter') {
          onSearch(inputRef.current?.value ?? '');
        }
      }}
    />
  );
}
```

---

## State Management

### Context API for Theme

```typescript
interface ThemeContextType {
  theme: 'light' | 'dark' | 'study' | 'fitness' | 'mind';
  setTheme: (theme: string) => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<ThemeContextType['theme']>('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useThemeContext() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useThemeContext must be used within ThemeProvider');
  }
  return context;
}
```

### Zustand for Global State (Optional)

```typescript
import create from 'zustand';

interface AppStore {
  sidebarOpen: boolean;
  notifications: Notification[];
  toggleSidebar: () => void;
  addNotification: (notification: Notification) => void;
  removeNotification: (id: string) => void;
}

export const useAppStore = create<AppStore>((set) => ({
  sidebarOpen: true,
  notifications: [],
  toggleSidebar: () => set(state => ({
    sidebarOpen: !state.sidebarOpen
  })),
  addNotification: (notification) => set(state => ({
    notifications: [...state.notifications, notification]
  })),
  removeNotification: (id) => set(state => ({
    notifications: state.notifications.filter(n => n.id !== id)
  })),
}));
```

---

## Performance Optimization

### Memo for Expensive Components

```typescript
const FlashcardDisplay = memo(function FlashcardDisplay({
  card,
  isFlipped,
}: {
  card: Flashcard;
  isFlipped: boolean;
}) {
  return (
    <div className="flashcard">
      <div className={isFlipped ? 'back' : 'front'}>
        {isFlipped ? card.back : card.front}
      </div>
    </div>
  );
}, (prevProps, nextProps) => {
  // Custom comparison
  return (
    prevProps.card._id === nextProps.card._id &&
    prevProps.isFlipped === nextProps.isFlipped
  );
});
```

### Code Splitting with React.lazy

```typescript
const PomodoroModule = lazy(() => import('@/pages/modules/pomodoro'));
const ChessModule = lazy(() => import('@/pages/modules/chess'));

export function ModuleRouter({ moduleId }: { moduleId: string }) {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      {moduleId === 'pomodoro' && <PomodoroModule />}
      {moduleId === 'chess' && <ChessModule />}
    </Suspense>
  );
}
```

### useMemo for Expensive Calculations

```typescript
function StatisticsPanel({ workouts }: { workouts: Workout[] }) {
  const stats = useMemo(() => {
    return {
      totalWorkouts: workouts.length,
      totalVolume: workouts.reduce((sum, w) => sum + w.volume, 0),
      averageIntensity: workouts.reduce((sum, w) => sum + w.intensity, 0) / workouts.length,
      personalRecords: workouts.filter(w => w.isPR),
    };
  }, [workouts]);
  
  return (
    <div>
      <div>Total Workouts: {stats.totalWorkouts}</div>
      <div>Total Volume: {stats.totalVolume}kg</div>
    </div>
  );
}
```

---

## Error Handling

### Error Boundary Component

```typescript
interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }
  
  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }
  
  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Send to error tracking service
  }
  
  render() {
    if (this.state.hasError) {
      return (
        this.props.fallback ?? (
          <div className="error-page">
            <h1>Something went wrong</h1>
            <p>{this.state.error?.message}</p>
            <button onClick={() => this.setState({ hasError: false })}>
              Try again
            </button>
          </div>
        )
      );
    }
    
    return this.props.children;
  }
}
```

### Async Error Handling Pattern

```typescript
async function handleSubmitForm(data: FormData) {
  try {
    const result = await submitForm(data);
    showSuccess('Form submitted successfully');
    return result;
  } catch (err) {
    if (err instanceof ValidationError) {
      showError('Please check your input');
    } else if (err instanceof NetworkError) {
      showError('Network error. Please try again.');
    } else {
      showError('An unexpected error occurred');
      console.error(err);
    }
    throw err;
  }
}
```

---

## Code Organization

### Component File Structure

```
components/
  Study/
    index.ts              # exports
    StudyModule.tsx      # main component
    StudyModule.module.css
    hooks/
      useStudySession.ts
      useFlashcards.ts
    components/
      FlashcardCard.tsx
      QuizModal.tsx
      StudyStats.tsx
    types/
      index.ts
```

### Custom Hook Organization

```
hooks/
  index.ts           # barrel export
  useAuth.ts
  useFlashcards.ts
  useForm.ts
  useTheme.ts
  useApi.ts
  utils/
    useDebounce.ts
    useLocalStorage.ts
```

---

## Testing Patterns

### Component Testing

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { FlashcardReview } from './FlashcardReview';

describe('FlashcardReview', () => {
  it('should flip card on space key', () => {
    render(<FlashcardReview card={mockCard} />);
    
    const card = screen.getByRole('article');
    fireEvent.keyDown(card, { key: ' ' });
    
    expect(screen.getByText(mockCard.back)).toBeInTheDocument();
  });
  
  it('should submit review', async () => {
    const onReview = jest.fn();
    render(<FlashcardReview card={mockCard} onReview={onReview} />);
    
    fireEvent.click(screen.getByRole('button', { name: /good/i }));
    
    expect(onReview).toHaveBeenCalledWith(4);
  });
});
```

### Hook Testing

```typescript
import { renderHook, act } from '@testing-library/react';
import { useFlashcards } from './useFlashcards';

describe('useFlashcards', () => {
  it('should flip card', () => {
    const { result } = renderHook(() => useFlashcards('deck-1'));
    
    act(() => {
      result.current.setIsFlipped(true);
    });
    
    expect(result.current.isFlipped).toBe(true);
  });
});
```

---

## Anti-Patterns to Avoid

### ❌ Prop Drilling

**Bad:**
```typescript
function Parent({ user }) {
  return <Child user={user} />;
}

function Child({ user }) {
  return <GrandChild user={user} />;
}

function GrandChild({ user }) {
  return <div>{user.name}</div>;
}
```

**Good:**
```typescript
function Parent() {
  return <Child />;
}

function Child() {
  return <GrandChild />;
}

function GrandChild() {
  const user = useContext(UserContext);
  return <div>{user.name}</div>;
}
```

### ❌ All State at Root

**Bad:** Store all state in one mega-component

**Good:** Keep state local, use Context for shared state, Convex for server state

### ❌ useEffect Dependencies

**Bad:**
```typescript
useEffect(() => {
  fetchData(userId);
}, []); // Missing userId dependency
```

**Good:**
```typescript
useEffect(() => {
  fetchData(userId);
}, [userId]); // Include all dependencies
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Engineering Team
