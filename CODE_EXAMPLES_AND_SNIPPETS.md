# Code Examples & Snippets

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Common Mutations](#common-mutations)
2. [Query Patterns](#query-patterns)
3. [Form Handling](#form-handling)
4. [Error Handling](#error-handling)
5. [Data Transformations](#data-transformations)
6. [Component Examples](#component-examples)
7. [API Patterns](#api-patterns)
8. [Real-World Examples](#real-world-examples)

---

## Common Mutations

### Create Note with Tags

```typescript
// Convex function
export const mutation = async (
  ctx: MutationCtx,
  args: {
    title: string;
    content: string;
    tags: string[];
    subjectId: string;
  }
) => {
  const userId = await ctx.auth.getUserIdentity();
  if (!userId) throw new Error('Not authenticated');
  
  // Validate input
  if (args.title.trim().length === 0) {
    throw new Error('Title cannot be empty');
  }
  
  if (args.content.trim().length === 0) {
    throw new Error('Content cannot be empty');
  }
  
  // Create note
  const noteId = await ctx.db.insert('notes', {
    userId: userId.subject,
    title: args.title.trim(),
    content: args.content.trim(),
    subjectId: args.subjectId,
    tags: args.tags.map(t => t.toLowerCase()),
    createdAt: Date.now(),
    updatedAt: Date.now(),
    isArchived: false,
  });
  
  // Return created note
  return await ctx.db.get(noteId);
};

// Frontend usage
export function useCreateNote() {
  const createNoteMutation = useMutation(api.study.createNote);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const createNote = useCallback(async (data: {
    title: string;
    content: string;
    tags: string[];
    subjectId: string;
  }) => {
    setIsLoading(true);
    setError(null);
    try {
      const note = await createNoteMutation(data);
      return note;
    } catch (err) {
      const message = err instanceof Error ? err.message : 'Failed to create note';
      setError(message);
      throw err;
    } finally {
      setIsLoading(false);
    }
  }, [createNoteMutation]);
  
  return { createNote, isLoading, error };
}
```

### Log Workout with Multiple Exercises

```typescript
// Convex function
export const logWorkout = async (
  ctx: MutationCtx,
  args: {
    date: number;
    duration: number;
    exercises: Array<{
      name: string;
      sets: number;
      reps: number;
      weight?: number;
      notes?: string;
    }>;
    notes?: string;
  }
) => {
  const userId = await ctx.auth.getUserIdentity();
  if (!userId) throw new Error('Not authenticated');
  
  const user = await ctx.db
    .query('users')
    .filter(q => q.eq(q.field('_id'), userId.subject))
    .first();
  
  if (!user) throw new Error('User not found');
  
  // Calculate total volume
  const totalVolume = args.exercises.reduce((sum, ex) => {
    if (!ex.weight) return sum;
    return sum + (ex.sets * ex.reps * ex.weight);
  }, 0);
  
  // Create workout
  const workoutId = await ctx.db.insert('workouts', {
    userId: userId.subject,
    date: args.date,
    duration: args.duration,
    exercises: args.exercises,
    totalVolume,
    notes: args.notes,
    createdAt: Date.now(),
  });
  
  // Update personal records if any exercise was a PR
  for (const exercise of args.exercises) {
    if (exercise.weight) {
      const existingPR = await ctx.db
        .query('personal_records')
        .filter(q => 
          q.and(
            q.eq(q.field('userId'), userId.subject),
            q.eq(q.field('exercise'), exercise.name)
          )
        )
        .first();
      
      if (!existingPR || existingPR.weight < exercise.weight) {
        if (existingPR) {
          await ctx.db.patch(existingPR._id, {
            weight: exercise.weight,
            date: args.date,
          });
        } else {
          await ctx.db.insert('personal_records', {
            userId: userId.subject,
            exercise: exercise.name,
            weight: exercise.weight,
            date: args.date,
          });
        }
      }
    }
  }
  
  return await ctx.db.get(workoutId);
};

// Frontend component
function WorkoutLogger() {
  const [exercises, setExercises] = useState<ExerciseLog[]>([
    { name: '', sets: 3, reps: 10, weight: 0 }
  ]);
  const logWorkoutMutation = useMutation(api.fitness.logWorkout);
  
  const handleAddExercise = () => {
    setExercises(prev => [...prev, { name: '', sets: 3, reps: 10, weight: 0 }]);
  };
  
  const handleUpdateExercise = (index: number, field: string, value: any) => {
    setExercises(prev => {
      const updated = [...prev];
      updated[index] = { ...updated[index], [field]: value };
      return updated;
    });
  };
  
  const handleSubmit = async () => {
    if (exercises.some(e => !e.name)) {
      alert('Please fill in all exercise names');
      return;
    }
    
    try {
      await logWorkoutMutation({
        date: Date.now(),
        duration: 60,
        exercises,
      });
      alert('Workout logged!');
      setExercises([{ name: '', sets: 3, reps: 10, weight: 0 }]);
    } catch (err) {
      alert('Failed to log workout');
    }
  };
  
  return (
    <div className="workout-logger">
      {exercises.map((exercise, idx) => (
        <div key={idx} className="exercise-row">
          <input
            type="text"
            placeholder="Exercise name"
            value={exercise.name}
            onChange={(e) => handleUpdateExercise(idx, 'name', e.target.value)}
          />
          <input
            type="number"
            placeholder="Sets"
            value={exercise.sets}
            onChange={(e) => handleUpdateExercise(idx, 'sets', parseInt(e.target.value))}
          />
          <input
            type="number"
            placeholder="Reps"
            value={exercise.reps}
            onChange={(e) => handleUpdateExercise(idx, 'reps', parseInt(e.target.value))}
          />
          <input
            type="number"
            placeholder="Weight (kg)"
            value={exercise.weight}
            onChange={(e) => handleUpdateExercise(idx, 'weight', parseFloat(e.target.value))}
          />
        </div>
      ))}
      <button onClick={handleAddExercise}>+ Add Exercise</button>
      <button onClick={handleSubmit}>Log Workout</button>
    </div>
  );
}
```

---

## Query Patterns

### Get Flashcards Due for Review with Filters

```typescript
// Convex function
export const getFlashcardsDueForReview = async (
  ctx: QueryCtx,
  args: {
    deckId: string;
    limit?: number;
    sortBy?: 'dueDate' | 'interval' | 'difficulty';
  }
) => {
  const userId = await ctx.auth.getUserIdentity();
  if (!userId) throw new Error('Not authenticated');
  
  // Get cards due for review (dueDate < now)
  const now = Date.now();
  const limit = args.limit ?? 50;
  
  let query = ctx.db
    .query('flashcards')
    .filter(q => 
      q.and(
        q.eq(q.field('userId'), userId.subject),
        q.eq(q.field('deckId'), args.deckId),
        q.lt(q.field('review.nextReviewDate'), now)
      )
    );
  
  // Apply sorting
  if (args.sortBy === 'dueDate') {
    query = query.order('asc', q => q.field('review.nextReviewDate'));
  } else if (args.sortBy === 'interval') {
    query = query.order('asc', q => q.field('review.interval'));
  } else if (args.sortBy === 'difficulty') {
    query = query.order('desc', q => q.field('review.difficulty'));
  }
  
  const cards = await query.take(limit);
  
  // Enrich with deck info
  const deck = await ctx.db.get(args.deckId);
  
  return {
    cards,
    deckName: deck?.name,
    totalDue: cards.length,
  };
};

// Frontend usage
function StudySession({ deckId }: { deckId: string }) {
  const studyData = useQuery(api.study.getFlashcardsDueForReview, {
    deckId,
    sortBy: 'dueDate',
  });
  const [index, setIndex] = useState(0);
  
  if (studyData === undefined) {
    return <div>Loading...</div>;
  }
  
  if (studyData.cards.length === 0) {
    return <div>All cards reviewed! Great work!</div>;
  }
  
  const currentCard = studyData.cards[index];
  
  return (
    <div className="study-session">
      <div className="progress">
        {index + 1} / {studyData.totalDue}
      </div>
      <Flashcard card={currentCard} />
      <button onClick={() => setIndex(prev => prev + 1)}>
        Next
      </button>
    </div>
  );
}
```

### Get User Statistics with Aggregation

```typescript
// Convex function
export const getUserStudyStats = async (
  ctx: QueryCtx,
  args: { userId: string; period?: 'week' | 'month' | 'all' }
) => {
  const period = args.period ?? 'month';
  const now = Date.now();
  const periodMs = {
    week: 7 * 24 * 60 * 60 * 1000,
    month: 30 * 24 * 60 * 60 * 1000,
    all: Infinity,
  }[period];
  
  // Get all notes created in period
  const notes = await ctx.db
    .query('notes')
    .filter(q => 
      q.and(
        q.eq(q.field('userId'), args.userId),
        q.gt(q.field('createdAt'), now - periodMs)
      )
    )
    .collect();
  
  // Get all flashcard reviews in period
  const reviews = await ctx.db
    .query('flashcard_reviews')
    .filter(q => 
      q.and(
        q.eq(q.field('userId'), args.userId),
        q.gt(q.field('reviewedAt'), now - periodMs)
      )
    )
    .collect();
  
  // Calculate statistics
  const totalNotes = notes.length;
  const totalReviews = reviews.length;
  const averageQuality = reviews.length > 0
    ? reviews.reduce((sum, r) => sum + r.quality, 0) / reviews.length
    : 0;
  
  const studySessions = await ctx.db
    .query('study_sessions')
    .filter(q => 
      q.and(
        q.eq(q.field('userId'), args.userId),
        q.gt(q.field('startedAt'), now - periodMs)
      )
    )
    .collect();
  
  const totalStudyTime = studySessions.reduce((sum, s) => 
    sum + (s.endedAt - s.startedAt), 0
  );
  
  return {
    period,
    totalNotes,
    totalReviews,
    averageQuality: Math.round(averageQuality * 100) / 100,
    studySessions: studySessions.length,
    totalStudyTimeMs: totalStudyTime,
    totalStudyTimeHours: Math.round(totalStudyTime / (1000 * 60 * 60) * 10) / 10,
  };
};
```

---

## Form Handling

### Complex Multi-Step Form

```typescript
interface FormStep {
  id: string;
  title: string;
  fields: Array<{
    name: string;
    label: string;
    type: 'text' | 'textarea' | 'select' | 'checkbox' | 'email';
    required?: boolean;
    options?: { label: string; value: string }[];
  }>;
}

function MultiStepForm({
  steps,
  onSubmit,
}: {
  steps: FormStep[];
  onSubmit: (data: Record<string, any>) => Promise<void>;
}) {
  const [currentStep, setCurrentStep] = useState(0);
  const [formData, setFormData] = useState<Record<string, any>>({});
  const [errors, setErrors] = useState<Record<string, string>>({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  
  const step = steps[currentStep];
  
  const validateStep = (stepId: string) => {
    const newErrors: Record<string, string> = {};
    const currentStepFields = steps.find(s => s.id === stepId)?.fields ?? [];
    
    for (const field of currentStepFields) {
      if (field.required && !formData[field.name]) {
        newErrors[field.name] = `${field.label} is required`;
      }
      if (field.type === 'email' && formData[field.name]) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailRegex.test(formData[field.name])) {
          newErrors[field.name] = 'Invalid email';
        }
      }
    }
    
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleNext = () => {
    if (validateStep(step.id)) {
      if (currentStep < steps.length - 1) {
        setCurrentStep(prev => prev + 1);
      }
    }
  };
  
  const handlePrevious = () => {
    setCurrentStep(prev => Math.max(0, prev - 1));
  };
  
  const handleChange = (field: string, value: any) => {
    setFormData(prev => ({ ...prev, [field]: value }));
    if (errors[field]) {
      setErrors(prev => {
        const newErrors = { ...prev };
        delete newErrors[field];
        return newErrors;
      });
    }
  };
  
  const handleSubmit = async () => {
    if (!validateStep(step.id)) return;
    
    setIsSubmitting(true);
    try {
      await onSubmit(formData);
      setFormData({});
      setCurrentStep(0);
    } catch (err) {
      console.error('Submit error:', err);
    } finally {
      setIsSubmitting(false);
    }
  };
  
  return (
    <div className="multi-step-form">
      <div className="steps-indicator">
        {steps.map((s, idx) => (
          <div
            key={s.id}
            className={`step ${idx === currentStep ? 'active' : ''} ${idx < currentStep ? 'completed' : ''}`}
          >
            {idx + 1}
          </div>
        ))}
      </div>
      
      <form className="form-fields">
        <h2>{step.title}</h2>
        {step.fields.map(field => (
          <div key={field.name} className="form-group">
            <label htmlFor={field.name}>{field.label}</label>
            {field.type === 'select' ? (
              <select
                id={field.name}
                value={formData[field.name] ?? ''}
                onChange={(e) => handleChange(field.name, e.target.value)}
              >
                <option value="">Select...</option>
                {field.options?.map(opt => (
                  <option key={opt.value} value={opt.value}>
                    {opt.label}
                  </option>
                ))}
              </select>
            ) : field.type === 'textarea' ? (
              <textarea
                id={field.name}
                value={formData[field.name] ?? ''}
                onChange={(e) => handleChange(field.name, e.target.value)}
              />
            ) : field.type === 'checkbox' ? (
              <input
                type="checkbox"
                id={field.name}
                checked={formData[field.name] ?? false}
                onChange={(e) => handleChange(field.name, e.target.checked)}
              />
            ) : (
              <input
                type={field.type}
                id={field.name}
                value={formData[field.name] ?? ''}
                onChange={(e) => handleChange(field.name, e.target.value)}
              />
            )}
            {errors[field.name] && (
              <span className="error">{errors[field.name]}</span>
            )}
          </div>
        ))}
      </form>
      
      <div className="form-actions">
        <button
          onClick={handlePrevious}
          disabled={currentStep === 0}
        >
          Previous
        </button>
        <span>{currentStep + 1} / {steps.length}</span>
        {currentStep === steps.length - 1 ? (
          <button
            onClick={handleSubmit}
            disabled={isSubmitting}
          >
            {isSubmitting ? 'Submitting...' : 'Submit'}
          </button>
        ) : (
          <button onClick={handleNext}>
            Next
          </button>
        )}
      </div>
    </div>
  );
}
```

---

## Error Handling

### Comprehensive Error Handler

```typescript
enum ErrorCode {
  VALIDATION_ERROR = 'VALIDATION_ERROR',
  NOT_FOUND = 'NOT_FOUND',
  UNAUTHORIZED = 'UNAUTHORIZED',
  CONFLICT = 'CONFLICT',
  RATE_LIMITED = 'RATE_LIMITED',
  INTERNAL_ERROR = 'INTERNAL_ERROR',
}

class AppError extends Error {
  constructor(
    public code: ErrorCode,
    public message: string,
    public statusCode: number = 500,
    public details?: Record<string, any>
  ) {
    super(message);
    this.name = 'AppError';
  }
}

function getErrorMessage(error: unknown): string {
  if (error instanceof AppError) {
    return error.message;
  }
  if (error instanceof Error) {
    return error.message;
  }
  return 'An unexpected error occurred';
}

// Usage in mutations
export const createNote = async (ctx: MutationCtx, args: CreateNoteArgs) => {
  try {
    const userId = await ctx.auth.getUserIdentity();
    if (!userId) {
      throw new AppError(
        ErrorCode.UNAUTHORIZED,
        'You must be logged in',
        401
      );
    }
    
    if (!args.title.trim()) {
      throw new AppError(
        ErrorCode.VALIDATION_ERROR,
        'Title cannot be empty',
        400
      );
    }
    
    // Create note...
  } catch (err) {
    if (err instanceof AppError) {
      throw err;
    }
    console.error('Unexpected error:', err);
    throw new AppError(
      ErrorCode.INTERNAL_ERROR,
      'Failed to create note',
      500
    );
  }
};

// Frontend error display
function useErrorHandler() {
  const [error, setError] = useState<{
    code: ErrorCode;
    message: string;
  } | null>(null);
  
  const handleError = (err: unknown) => {
    if (err instanceof AppError) {
      setError({
        code: err.code,
        message: err.message,
      });
    } else {
      setError({
        code: ErrorCode.INTERNAL_ERROR,
        message: getErrorMessage(err),
      });
    }
  };
  
  const getDisplayMessage = () => {
    if (!error) return null;
    
    switch (error.code) {
      case ErrorCode.VALIDATION_ERROR:
        return `Invalid input: ${error.message}`;
      case ErrorCode.UNAUTHORIZED:
        return 'Please log in to continue';
      case ErrorCode.NOT_FOUND:
        return 'The item you\'re looking for doesn\'t exist';
      case ErrorCode.RATE_LIMITED:
        return 'Too many requests. Please try again later';
      default:
        return 'Something went wrong. Please try again.';
    }
  };
  
  return { error, handleError, getDisplayMessage };
}
```

---

## Data Transformations

### Flatten Nested Data

```typescript
function flattenFlashcard(card: FlashcardWithReviews) {
  return {
    id: card._id,
    front: card.front,
    back: card.back,
    lastReviewDate: card.review?.lastReviewDate,
    nextReviewDate: card.review?.nextReviewDate,
    easinessFactor: card.review?.easinessFactor,
    interval: card.review?.interval,
    repetitions: card.review?.repetitions,
    quality: card.review?.quality,
  };
}

// Usage
const cards = flashcards.map(flattenFlashcard);
```

### Group and Aggregate

```typescript
function groupWorkoutsByDate(workouts: Workout[]) {
  return workouts.reduce((groups, workout) => {
    const date = new Date(workout.date).toLocaleDateString();
    if (!groups[date]) {
      groups[date] = [];
    }
    groups[date].push(workout);
    return groups;
  }, {} as Record<string, Workout[]>);
}

function calculateDailyStats(workouts: Workout[]) {
  const grouped = groupWorkoutsByDate(workouts);
  
  return Object.entries(grouped).map(([date, dayWorkouts]) => ({
    date,
    count: dayWorkouts.length,
    totalVolume: dayWorkouts.reduce((sum, w) => sum + w.totalVolume, 0),
    totalDuration: dayWorkouts.reduce((sum, w) => sum + w.duration, 0),
    averageIntensity: dayWorkouts.reduce((sum, w) => sum + w.intensity, 0) / dayWorkouts.length,
  }));
}
```

---

## Component Examples

### Reusable Card Component

```typescript
interface CardProps {
  title?: ReactNode;
  subtitle?: ReactNode;
  children: ReactNode;
  footer?: ReactNode;
  className?: string;
  isLoading?: boolean;
  error?: string | null;
  action?: ReactNode;
}

export function Card({
  title,
  subtitle,
  children,
  footer,
  className,
  isLoading,
  error,
  action,
}: CardProps) {
  return (
    <div className={`card ${className ?? ''}`}>
      {(title || subtitle || action) && (
        <div className="card-header">
          <div>
            {title && <h3 className="card-title">{title}</h3>}
            {subtitle && <p className="card-subtitle">{subtitle}</p>}
          </div>
          {action}
        </div>
      )}
      
      <div className="card-content">
        {isLoading && <div className="spinner">Loading...</div>}
        {error && <div className="error-message">{error}</div>}
        {!isLoading && !error && children}
      </div>
      
      {footer && <div className="card-footer">{footer}</div>}
    </div>
  );
}
```

---

## API Patterns

### Pagination

```typescript
interface PaginationParams {
  page: number;
  pageSize: number;
}

export const getPaginatedNotes = async (
  ctx: QueryCtx,
  args: PaginationParams & { userId: string }
) => {
  const { page, pageSize, userId } = args;
  const skip = (page - 1) * pageSize;
  
  const total = await ctx.db
    .query('notes')
    .filter(q => q.eq(q.field('userId'), userId))
    .count();
  
  const items = await ctx.db
    .query('notes')
    .filter(q => q.eq(q.field('userId'), userId))
    .order('desc', q => q.field('createdAt'))
    .skip(skip)
    .take(pageSize)
    .collect();
  
  return {
    items,
    pagination: {
      page,
      pageSize,
      total,
      pages: Math.ceil(total / pageSize),
      hasMore: page * pageSize < total,
    },
  };
};

// Frontend usage
function NotesList() {
  const [page, setPage] = useState(1);
  const { items, pagination } = useQuery(api.study.getPaginatedNotes, {
    page,
    pageSize: 20,
    userId: currentUserId,
  }) ?? { items: [], pagination: { page: 1, pages: 1, hasMore: false } };
  
  return (
    <>
      {items.map(note => <NoteCard key={note._id} note={note} />)}
      
      <div className="pagination">
        <button
          onClick={() => setPage(p => Math.max(1, p - 1))}
          disabled={page === 1}
        >
          Previous
        </button>
        <span>{page} / {pagination.pages}</span>
        <button
          onClick={() => setPage(p => p + 1)}
          disabled={!pagination.hasMore}
        >
          Next
        </button>
      </div>
    </>
  );
}
```

---

## Real-World Examples

### Complete Study Session Component

```typescript
export function StudySession({ deckId }: { deckId: string }) {
  const cards = useQuery(api.study.getFlashcardsDueForReview, { deckId });
  const reviewCardMutation = useMutation(api.study.reviewFlashcard);
  
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isFlipped, setIsFlipped] = useState(false);
  const [stats, setStats] = useState({
    reviewed: 0,
    easy: 0,
    good: 0,
    hard: 0,
    failed: 0,
  });
  
  const currentCard = cards?.[currentIndex];
  
  useEffect(() => {
    const handleKeyPress = (e: KeyboardEvent) => {
      if (e.key === ' ') {
        e.preventDefault();
        setIsFlipped(prev => !prev);
      }
      if (e.key >= '1' && e.key <= '5') {
        const quality = parseInt(e.key) as 1 | 2 | 3 | 4 | 5;
        handleReview(quality);
      }
      if (e.key === 'ArrowRight') {
        moveNext();
      }
      if (e.key === 'ArrowLeft') {
        movePrevious();
      }
    };
    
    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, [currentIndex, isFlipped]);
  
  const handleReview = async (quality: 1 | 2 | 3 | 4 | 5) => {
    if (!currentCard) return;
    
    try {
      await reviewCardMutation({
        cardId: currentCard._id,
        quality,
      });
      
      setStats(prev => ({
        ...prev,
        reviewed: prev.reviewed + 1,
        [quality === 1 ? 'failed' : quality === 2 ? 'hard' : quality === 3 ? 'good' : 'easy']: prev[quality === 1 ? 'failed' : quality === 2 ? 'hard' : quality === 3 ? 'good' : 'easy'] + 1,
      }));
      
      moveNext();
    } catch (err) {
      console.error('Review failed:', err);
    }
  };
  
  const moveNext = () => {
    if (currentIndex < (cards?.length ?? 0) - 1) {
      setCurrentIndex(prev => prev + 1);
      setIsFlipped(false);
    }
  };
  
  const movePrevious = () => {
    if (currentIndex > 0) {
      setCurrentIndex(prev => prev - 1);
      setIsFlipped(false);
    }
  };
  
  if (!cards) {
    return <div className="spinner">Loading cards...</div>;
  }
  
  if (cards.length === 0) {
    return (
      <div className="empty-state">
        <h2>All cards reviewed!</h2>
        <p>Great work! Come back tomorrow.</p>
      </div>
    );
  }
  
  return (
    <div className="study-session">
      <div className="session-header">
        <div className="progress-bar">
          <div
            className="progress-fill"
            style={{ width: `${((currentIndex + 1) / cards.length * 100)}%` }}
          />
        </div>
        <div className="progress-text">
          {currentIndex + 1} / {cards.length}
        </div>
      </div>
      
      {currentCard && (
        <div className="flashcard-container">
          <div
            className={`flashcard ${isFlipped ? 'flipped' : ''}`}
            onClick={() => setIsFlipped(!isFlipped)}
          >
            <div className="flashcard-inner">
              <div className="flashcard-front">
                <p>{currentCard.front}</p>
                <span className="hint">(Press space or click to reveal)</span>
              </div>
              <div className="flashcard-back">
                <p>{currentCard.back}</p>
              </div>
            </div>
          </div>
        </div>
      )}
      
      <div className="quality-buttons">
        <button onClick={() => handleReview(1)} className="btn-fail">1: Fail</button>
        <button onClick={() => handleReview(2)} className="btn-hard">2: Hard</button>
        <button onClick={() => handleReview(3)} className="btn-ok">3: OK</button>
        <button onClick={() => handleReview(4)} className="btn-good">4: Good</button>
        <button onClick={() => handleReview(5)} className="btn-easy">5: Easy</button>
      </div>
      
      <div className="session-stats">
        <div>Total: {stats.reviewed}</div>
        <div className="stat-good">Good: {stats.good + stats.easy}</div>
        <div className="stat-hard">Hard: {stats.hard}</div>
        <div className="stat-fail">Failed: {stats.failed}</div>
      </div>
    </div>
  );
}
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Engineering Team
