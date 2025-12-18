# Error Handling & Logging Guide

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Error Types & Codes](#error-types--codes)
2. [Error Handling Patterns](#error-handling-patterns)
3. [Logging Levels](#logging-levels)
4. [User-Facing Messages](#user-facing-messages)
5. [Debug Logging](#debug-logging)
6. [Error Recovery](#error-recovery)
7. [Monitoring & Alerts](#monitoring--alerts)

---

## Error Types & Codes

### Error Code Reference

```typescript
enum ErrorCode {
  // Authentication (1000-1999)
  INVALID_OTP = 'INVALID_OTP',
  OTP_EXPIRED = 'OTP_EXPIRED',
  OTP_RATE_LIMITED = 'OTP_RATE_LIMITED',
  UNAUTHORIZED = 'UNAUTHORIZED',
  SESSION_EXPIRED = 'SESSION_EXPIRED',
  
  // Validation (2000-2999)
  VALIDATION_ERROR = 'VALIDATION_ERROR',
  MISSING_REQUIRED_FIELD = 'MISSING_REQUIRED_FIELD',
  INVALID_EMAIL = 'INVALID_EMAIL',
  INVALID_FORMAT = 'INVALID_FORMAT',
  
  // Data Operations (3000-3999)
  NOT_FOUND = 'NOT_FOUND',
  DUPLICATE_ENTRY = 'DUPLICATE_ENTRY',
  CONFLICT = 'CONFLICT',
  CONSTRAINT_VIOLATION = 'CONSTRAINT_VIOLATION',
  
  // Rate Limiting (4000-4999)
  RATE_LIMITED = 'RATE_LIMITED',
  TOO_MANY_REQUESTS = 'TOO_MANY_REQUESTS',
  QUOTA_EXCEEDED = 'QUOTA_EXCEEDED',
  
  // External Services (5000-5999)
  EXTERNAL_SERVICE_ERROR = 'EXTERNAL_SERVICE_ERROR',
  NETWORK_ERROR = 'NETWORK_ERROR',
  TIMEOUT = 'TIMEOUT',
  
  // Server Errors (6000-6999)
  INTERNAL_ERROR = 'INTERNAL_ERROR',
  DATABASE_ERROR = 'DATABASE_ERROR',
  PERMISSION_DENIED = 'PERMISSION_DENIED',
  
  // Unknown (9000-9999)
  UNKNOWN_ERROR = 'UNKNOWN_ERROR',
}
```

### Error Class Hierarchy

```typescript
class AppError extends Error {
  public readonly statusCode: number;
  public readonly isOperational: boolean;
  public readonly code: ErrorCode;
  public readonly timestamp: number;
  public readonly details?: Record<string, any>;
  
  constructor(
    code: ErrorCode,
    message: string,
    statusCode: number = 500,
    isOperational: boolean = true,
    details?: Record<string, any>
  ) {
    super(message);
    this.code = code;
    this.statusCode = statusCode;
    this.isOperational = isOperational;
    this.timestamp = Date.now();
    this.details = details;
    Object.setPrototypeOf(this, AppError.prototype);
  }
}

class ValidationError extends AppError {
  constructor(message: string, details?: Record<string, any>) {
    super(
      ErrorCode.VALIDATION_ERROR,
      message,
      400,
      true,
      details
    );
    Object.setPrototypeOf(this, ValidationError.prototype);
  }
}

class AuthenticationError extends AppError {
  constructor(message: string, code = ErrorCode.UNAUTHORIZED) {
    super(code, message, 401, true);
    Object.setPrototypeOf(this, AuthenticationError.prototype);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string) {
    super(
      ErrorCode.NOT_FOUND,
      `${resource} not found`,
      404,
      true
    );
    Object.setPrototypeOf(this, NotFoundError.prototype);
  }
}

class RateLimitError extends AppError {
  public readonly retryAfter: number;
  
  constructor(retryAfter: number = 60) {
    super(
      ErrorCode.RATE_LIMITED,
      'Too many requests. Please try again later.',
      429,
      true
    );
    this.retryAfter = retryAfter;
    Object.setPrototypeOf(this, RateLimitError.prototype);
  }
}
```

---

## Error Handling Patterns

### Convex Mutation Error Handling

```typescript
export const createNote = async (
  ctx: MutationCtx,
  args: { title: string; content: string }
) => {
  try {
    // Validate authentication
    const userId = await ctx.auth.getUserIdentity();
    if (!userId) {
      throw new AuthenticationError('Not authenticated');
    }
    
    // Validate input
    if (!args.title?.trim()) {
      throw new ValidationError('Title is required');
    }
    
    if (!args.content?.trim()) {
      throw new ValidationError('Content is required');
    }
    
    if (args.title.length > 500) {
      throw new ValidationError('Title must be less than 500 characters');
    }
    
    // Check for duplicates
    const existing = await ctx.db
      .query('notes')
      .filter(q =>
        q.and(
          q.eq(q.field('userId'), userId.subject),
          q.eq(q.field('title'), args.title)
        )
      )
      .first();
    
    if (existing) {
      throw new AppError(
        ErrorCode.DUPLICATE_ENTRY,
        'A note with this title already exists',
        409,
        true
      );
    }
    
    // Create note
    const noteId = await ctx.db.insert('notes', {
      userId: userId.subject,
      title: args.title.trim(),
      content: args.content.trim(),
      createdAt: Date.now(),
      updatedAt: Date.now(),
    });
    
    return await ctx.db.get(noteId);
  } catch (err) {
    // Log error
    logError(err, {
      operation: 'createNote',
      args: { title: args.title },
    });
    
    // Re-throw if operational error
    if (err instanceof AppError) {
      throw err;
    }
    
    // Wrap unexpected errors
    throw new AppError(
      ErrorCode.INTERNAL_ERROR,
      'Failed to create note',
      500,
      false
    );
  }
};
```

### Frontend Error Handling

```typescript
// hooks/useErrorHandler.ts
export function useErrorHandler() {
  const [error, setError] = useState<AppError | null>(null);
  
  const handleError = useCallback((err: unknown) => {
    let appError: AppError;
    
    if (err instanceof AppError) {
      appError = err;
    } else if (err instanceof Error) {
      appError = new AppError(
        ErrorCode.UNKNOWN_ERROR,
        err.message,
        500,
        false
      );
    } else {
      appError = new AppError(
        ErrorCode.UNKNOWN_ERROR,
        'An unexpected error occurred',
        500,
        false
      );
    }
    
    // Log the error
    logError(appError);
    
    // Set error state
    setError(appError);
    
    // Auto-clear after 5 seconds
    const timer = setTimeout(() => setError(null), 5000);
    return () => clearTimeout(timer);
  }, []);
  
  const clearError = useCallback(() => setError(null), []);
  
  return { error, handleError, clearError };
}

// Component usage
function MyComponent() {
  const { error, handleError, clearError } = useErrorHandler();
  const createNoteMutation = useMutation(api.study.createNote);
  
  const handleCreateNote = async (data: CreateNoteData) => {
    try {
      await createNoteMutation(data);
    } catch (err) {
      handleError(err);
    }
  };
  
  return (
    <>
      {error && (
        <ErrorAlert
          error={error}
          onDismiss={clearError}
        />
      )}
      <NoteForm onSubmit={handleCreateNote} />
    </>
  );
}
```

---

## Logging Levels

### Logging Configuration

```typescript
// lib/logger.ts
enum LogLevel {
  DEBUG = 0,
  INFO = 1,
  WARN = 2,
  ERROR = 3,
  FATAL = 4,
}

interface LogEntry {
  timestamp: number;
  level: LogLevel;
  message: string;
  context?: Record<string, any>;
  error?: {
    code: ErrorCode;
    message: string;
    stack?: string;
  };
}

class Logger {
  private static instance: Logger;
  private level: LogLevel = LogLevel.INFO;
  private isDevelopment = process.env.NODE_ENV === 'development';
  
  private constructor() {}
  
  static getInstance(): Logger {
    if (!Logger.instance) {
      Logger.instance = new Logger();
    }
    return Logger.instance;
  }
  
  setLevel(level: LogLevel) {
    this.level = level;
  }
  
  private shouldLog(level: LogLevel): boolean {
    return level >= this.level;
  }
  
  private formatMessage(
    level: LogLevel,
    message: string,
    context?: Record<string, any>
  ): string {
    const levelName = LogLevel[level];
    const timestamp = new Date().toISOString();
    const contextStr = context ? ` ${JSON.stringify(context)}` : '';
    return `[${timestamp}] [${levelName}] ${message}${contextStr}`;
  }
  
  debug(message: string, context?: Record<string, any>) {
    if (this.shouldLog(LogLevel.DEBUG)) {
      console.debug(this.formatMessage(LogLevel.DEBUG, message, context));
    }
  }
  
  info(message: string, context?: Record<string, any>) {
    if (this.shouldLog(LogLevel.INFO)) {
      console.info(this.formatMessage(LogLevel.INFO, message, context));
    }
  }
  
  warn(message: string, context?: Record<string, any>) {
    if (this.shouldLog(LogLevel.WARN)) {
      console.warn(this.formatMessage(LogLevel.WARN, message, context));
    }
  }
  
  error(message: string, context?: Record<string, any>, err?: Error) {
    if (this.shouldLog(LogLevel.ERROR)) {
      const entry: LogEntry = {
        timestamp: Date.now(),
        level: LogLevel.ERROR,
        message,
        context,
      };
      
      if (err instanceof AppError) {
        entry.error = {
          code: err.code,
          message: err.message,
          stack: this.isDevelopment ? err.stack : undefined,
        };
      } else if (err) {
        entry.error = {
          code: ErrorCode.UNKNOWN_ERROR,
          message: err.message,
          stack: this.isDevelopment ? err.stack : undefined,
        };
      }
      
      console.error(JSON.stringify(entry, null, 2));
      
      // Send to error tracking service
      this.sendToErrorTracking(entry);
    }
  }
  
  fatal(message: string, context?: Record<string, any>, err?: Error) {
    if (this.shouldLog(LogLevel.FATAL)) {
      console.error(`[FATAL] ${message}`, context, err);
      this.sendToErrorTracking({
        timestamp: Date.now(),
        level: LogLevel.FATAL,
        message,
        context,
        error: err ? {
          code: ErrorCode.INTERNAL_ERROR,
          message: err.message,
          stack: err.stack,
        } : undefined,
      });
    }
  }
  
  private sendToErrorTracking(entry: LogEntry) {
    // Send to Sentry or similar service
    if (process.env.REACT_APP_SENTRY_DSN) {
      // Implementation
    }
  }
}

export const logger = Logger.getInstance();
```

---

## User-Facing Messages

### Error Message Mapping

```typescript
// lib/errorMessages.ts
export const ERROR_MESSAGES: Record<ErrorCode, string> = {
  [ErrorCode.INVALID_OTP]: 'The code you entered is invalid. Please try again.',
  [ErrorCode.OTP_EXPIRED]: 'The code has expired. Please request a new one.',
  [ErrorCode.OTP_RATE_LIMITED]: 'Too many OTP requests. Please try again in {minutes} minutes.',
  [ErrorCode.UNAUTHORIZED]: 'You need to log in to continue.',
  [ErrorCode.SESSION_EXPIRED]: 'Your session has expired. Please log in again.',
  [ErrorCode.VALIDATION_ERROR]: 'Please check your input and try again.',
  [ErrorCode.MISSING_REQUIRED_FIELD]: 'Please fill in all required fields.',
  [ErrorCode.INVALID_EMAIL]: 'Please enter a valid email address.',
  [ErrorCode.NOT_FOUND]: 'The item you are looking for could not be found.',
  [ErrorCode.DUPLICATE_ENTRY]: 'This item already exists.',
  [ErrorCode.RATE_LIMITED]: 'Too many requests. Please wait a moment and try again.',
  [ErrorCode.NETWORK_ERROR]: 'Network error. Please check your connection.',
  [ErrorCode.TIMEOUT]: 'Request timed out. Please try again.',
  [ErrorCode.EXTERNAL_SERVICE_ERROR]: 'An external service is temporarily unavailable.',
  [ErrorCode.INTERNAL_ERROR]: 'Something went wrong. Our team has been notified.',
};

export function getUserMessage(error: AppError): string {
  const message = ERROR_MESSAGES[error.code];
  
  if (!message) {
    return ERROR_MESSAGES[ErrorCode.INTERNAL_ERROR];
  }
  
  // Replace placeholders
  if (error instanceof RateLimitError) {
    return message.replace('{minutes}', '5');
  }
  
  return message;
}
```

### Error Alert Component

```typescript
// components/ErrorAlert.tsx
interface ErrorAlertProps {
  error: AppError;
  onDismiss?: () => void;
  onRetry?: () => void;
}

export function ErrorAlert({
  error,
  onDismiss,
  onRetry,
}: ErrorAlertProps) {
  const message = getUserMessage(error);
  
  return (
    <div className="error-alert" role="alert">
      <div className="error-icon">
        <AlertCircle size={20} />
      </div>
      
      <div className="error-content">
        <p className="error-message">{message}</p>
        {error instanceof RateLimitError && (
          <p className="error-hint">
            Retry available in {error.retryAfter} seconds
          </p>
        )}
      </div>
      
      <div className="error-actions">
        {onRetry && <button onClick={onRetry}>Retry</button>}
        {onDismiss && <button onClick={onDismiss}>Dismiss</button>}
      </div>
    </div>
  );
}
```

---

## Debug Logging

### Debug Mode Setup

```typescript
// lib/debug.ts
class DebugLogger {
  private enabled = 
    localStorage.getItem('DEBUG_MODE') === 'true' ||
    process.env.NODE_ENV === 'development';
  
  toggle() {
    this.enabled = !this.enabled;
    localStorage.setItem('DEBUG_MODE', String(this.enabled));
  }
  
  log(namespace: string, message: string, data?: any) {
    if (!this.enabled) return;
    
    console.log(
      `%c${namespace}%c ${message}`,
      'background: #1e90ff; color: white; padding: 2px 6px; border-radius: 3px;',
      'color: inherit;',
      data
    );
  }
  
  time(label: string) {
    if (this.enabled) console.time(label);
  }
  
  timeEnd(label: string) {
    if (this.enabled) console.timeEnd(label);
  }
}

export const debug = new DebugLogger();

// Enable in browser console
Object.defineProperty(window, 'DEBUG', {
  get: () => debug,
  enumerable: true,
});
```

### Usage Examples

```typescript
// In components
function StudySession() {
  const cards = useQuery(api.study.getFlashcardsDueForReview, { deckId });
  
  useEffect(() => {
    debug.log('StudySession', 'Cards loaded', {
      count: cards?.length,
      deckId,
    });
  }, [cards]);
  
  const handleReview = async (quality: number) => {
    debug.time('review-submission');
    
    try {
      await reviewCard({ cardId, quality });
      debug.log('StudySession', 'Review submitted', { quality });
    } finally {
      debug.timeEnd('review-submission');
    }
  };
}
```

---

## Error Recovery

### Retry Mechanisms

```typescript
// lib/retry.ts
interface RetryOptions {
  maxRetries: number;
  initialDelayMs: number;
  maxDelayMs: number;
  backoffMultiplier: number;
  shouldRetry?: (error: unknown) => boolean;
}

export async function retryWithBackoff<T>(
  fn: () => Promise<T>,
  options: RetryOptions
): Promise<T> {
  let lastError: Error | undefined;
  let delayMs = options.initialDelayMs;
  
  for (let attempt = 0; attempt < options.maxRetries; attempt++) {
    try {
      return await fn();
    } catch (err) {
      lastError = err instanceof Error ? err : new Error(String(err));
      
      if (options.shouldRetry && !options.shouldRetry(err)) {
        throw err;
      }
      
      if (attempt < options.maxRetries - 1) {
        await new Promise(resolve => setTimeout(resolve, delayMs));
        delayMs = Math.min(
          delayMs * options.backoffMultiplier,
          options.maxDelayMs
        );
      }
    }
  }
  
  throw lastError || new Error('Max retries exceeded');
}

// Usage
const data = await retryWithBackoff(
  () => fetchFlashcards(deckId),
  {
    maxRetries: 3,
    initialDelayMs: 1000,
    maxDelayMs: 10000,
    backoffMultiplier: 2,
    shouldRetry: (err) => {
      // Retry on network errors but not validation errors
      return err instanceof NetworkError;
    },
  }
);
```

### Fallback Strategies

```typescript
// Component using fallbacks
function SafeComponent({ id }: { id: string }) {
  const [data, setData] = useState<Data | null>(null);
  const [error, setError] = useState<Error | null>(null);
  
  useEffect(() => {
    fetchDataWithFallback(id)
      .then(setData)
      .catch(setError);
  }, [id]);
  
  if (error) {
    return <ErrorFallback error={error} />;
  }
  
  return <DataDisplay data={data} />;
}

async function fetchDataWithFallback(id: string): Promise<Data> {
  try {
    return await fetchFromAPI(id);
  } catch (err) {
    console.warn('API fetch failed, trying cache:', err);
    
    try {
      return await fetchFromCache(id);
    } catch (cacheErr) {
      console.error('Cache fetch also failed:', cacheErr);
      throw new Error('Could not load data from API or cache');
    }
  }
}
```

---

## Monitoring & Alerts

### Sentry Integration

```typescript
// lib/sentry.ts
import * as Sentry from "@sentry/react";

export function initSentry() {
  Sentry.init({
    dsn: process.env.REACT_APP_SENTRY_DSN,
    environment: process.env.NODE_ENV,
    tracesSampleRate: 1.0,
    integrations: [
      new Sentry.Replay({
        maskAllText: true,
        blockAllMedia: true,
      }),
    ],
    replaysSessionSampleRate: 0.1,
    replaysOnErrorSampleRate: 1.0,
  });
}

// Report error
export function reportError(err: Error, context?: Record<string, any>) {
  Sentry.captureException(err, { extra: context });
}

// Set user context
export function setUserContext(userId: string, email: string) {
  Sentry.setUser({ id: userId, email });
}
```

### PostHog Analytics for Errors

```typescript
// lib/analytics.ts
import posthog from 'posthog-js';

export function trackError(errorCode: ErrorCode, context?: Record<string, any>) {
  posthog.capture('error_occurred', {
    error_code: errorCode,
    ...context,
  });
}

export function trackErrorRecovery(
  errorCode: ErrorCode,
  recoveryMethod: string
) {
  posthog.capture('error_recovered', {
    error_code: errorCode,
    recovery_method: recoveryMethod,
  });
}
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Engineering & DevOps Teams
