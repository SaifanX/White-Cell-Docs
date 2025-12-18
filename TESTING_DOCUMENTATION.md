# Testing Documentation

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Test Structure](#test-structure)
2. [Unit Testing](#unit-testing)
3. [Integration Testing](#integration-testing)
4. [E2E Testing](#e2e-testing)
5. [Jest Configuration](#jest-configuration)
6. [Mock Strategies](#mock-strategies)
7. [Coverage Targets](#coverage-targets)
8. [Testing Checklist](#testing-checklist)

---

## Test Structure

### Folder Organization

```
src/
  __tests__/
    unit/
      hooks/
        useAuth.test.ts
        useFlashcards.test.ts
        useForm.test.ts
      utils/
        formatTime.test.ts
        calculateStats.test.ts
      lib/
        sm2Algorithm.test.ts
    integration/
      study/
        StudySession.integration.test.ts
      fitness/
        WorkoutLogging.integration.test.ts
    e2e/
      auth.e2e.test.ts
      study.e2e.test.ts
  components/
    __tests__/
      Flashcard.test.tsx
      StudySession.test.tsx
  hooks/
  lib/
  pages/
```

### Test File Naming

- Unit tests: `component.test.ts` or `hook.test.ts`
- Integration tests: `feature.integration.test.ts`
- E2E tests: `flow.e2e.test.ts`

---

## Unit Testing

### Testing Hooks

```typescript
// hooks/__tests__/useFlashcards.test.ts
import { renderHook, act } from '@testing-library/react';
import { useFlashcards } from '../useFlashcards';

describe('useFlashcards', () => {
  const mockCard = {
    _id: '1',
    front: 'What is 2+2?',
    back: '4',
    review: { nextReviewDate: 0 },
  };
  
  it('should initialize with first card', () => {
    const { result } = renderHook(() => useFlashcards('deck-1'), {
      wrapper: ConvexTestWrapper,
    });
    
    expect(result.current.currentCard).toBeDefined();
    expect(result.current.isFlipped).toBe(false);
  });
  
  it('should flip card on toggle', () => {
    const { result } = renderHook(() => useFlashcards('deck-1'), {
      wrapper: ConvexTestWrapper,
    });
    
    act(() => {
      result.current.setIsFlipped(true);
    });
    
    expect(result.current.isFlipped).toBe(true);
  });
  
  it('should update progress when reviewing', async () => {
    const { result } = renderHook(() => useFlashcards('deck-1'), {
      wrapper: ConvexTestWrapper,
    });
    
    const initialProgress = result.current.progress;
    
    await act(async () => {
      await result.current.handleReview(4);
    });
    
    expect(result.current.progress).toBeGreaterThan(initialProgress);
  });
  
  it('should track review history', async () => {
    const { result } = renderHook(() => useFlashcards('deck-1'), {
      wrapper: ConvexTestWrapper,
    });
    
    await act(async () => {
      await result.current.handleReview(5);
    });
    
    expect(result.current.reviewHistory).toHaveLength(1);
    expect(result.current.reviewHistory[0].quality).toBe(5);
  });
});
```

### Testing Custom Hooks with Async Operations

```typescript
// hooks/__tests__/useAuth.test.ts
import { renderHook, act, waitFor } from '@testing-library/react';
import { useAuth } from '../useAuth';
import * as authApi from '@/convex/auth';

jest.mock('@/convex/auth');

describe('useAuth', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  it('should request OTP', async () => {
    const { result } = renderHook(() => useAuth());
    const mockRequestOtp = authApi.requestOTP as jest.Mock;
    mockRequestOtp.mockResolvedValueOnce({ success: true });
    
    await act(async () => {
      await result.current.requestOtp('test@example.com');
    });
    
    expect(mockRequestOtp).toHaveBeenCalledWith({
      email: 'test@example.com',
    });
    expect(result.current.error).toBeNull();
  });
  
  it('should handle OTP request error', async () => {
    const { result } = renderHook(() => useAuth());
    const mockRequestOtp = authApi.requestOTP as jest.Mock;
    mockRequestOtp.mockRejectedValueOnce(new Error('Failed'));
    
    await act(async () => {
      try {
        await result.current.requestOtp('test@example.com');
      } catch (e) {
        // Expected
      }
    });
    
    expect(result.current.error).toBe('Failed');
  });
  
  it('should verify OTP', async () => {
    const { result } = renderHook(() => useAuth());
    const mockVerifyOtp = authApi.verifyOTP as jest.Mock;
    mockVerifyOtp.mockResolvedValueOnce({
      user: { _id: '1', email: 'test@example.com' },
      token: 'jwt-token',
    });
    
    const response = await act(async () => {
      return await result.current.verifyOtp('test@example.com', '123456');
    });
    
    expect(mockVerifyOtp).toHaveBeenCalledWith({
      email: 'test@example.com',
      code: '123456',
    });
  });
});
```

### Testing Components

```typescript
// components/__tests__/Flashcard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Flashcard } from '../Flashcard';

describe('Flashcard', () => {
  const mockCard = {
    _id: '1',
    front: 'Question?',
    back: 'Answer',
  };
  
  it('should render front side by default', () => {
    render(<Flashcard card={mockCard} isFlipped={false} />);
    expect(screen.getByText('Question?')).toBeInTheDocument();
  });
  
  it('should render back side when flipped', () => {
    render(<Flashcard card={mockCard} isFlipped={true} />);
    expect(screen.getByText('Answer')).toBeInTheDocument();
  });
  
  it('should call onFlip when clicked', () => {
    const onFlip = jest.fn();
    render(
      <Flashcard
        card={mockCard}
        isFlipped={false}
        onFlip={onFlip}
      />
    );
    
    fireEvent.click(screen.getByRole('article'));
    expect(onFlip).toHaveBeenCalled();
  });
  
  it('should handle keyboard space bar', () => {
    const onFlip = jest.fn();
    render(
      <Flashcard
        card={mockCard}
        isFlipped={false}
        onFlip={onFlip}
      />
    );
    
    fireEvent.keyDown(window, { key: ' ' });
    expect(onFlip).toHaveBeenCalled();
  });
});
```

### Testing Utility Functions

```typescript
// lib/__tests__/sm2Algorithm.test.ts
import { calculateSM2 } from '../sm2Algorithm';

describe('SM2 Algorithm', () => {
  it('should increase interval for good quality', () => {
    const result = calculateSM2({
      quality: 4, // Good
      previousInterval: 1,
      previousEasiness: 2.5,
      previousRepetitions: 1,
    });
    
    expect(result.nextInterval).toBeGreaterThan(1);
    expect(result.easiness).toBeGreaterThan(2.5);
    expect(result.repetitions).toBe(2);
  });
  
  it('should decrease interval for poor quality', () => {
    const result = calculateSM2({
      quality: 1, // Failed
      previousInterval: 10,
      previousEasiness: 2.5,
      previousRepetitions: 5,
    });
    
    expect(result.nextInterval).toBeLessThan(10);
    expect(result.repetitions).toBe(1); // Reset
  });
  
  it('should maintain minimum easiness factor', () => {
    const result = calculateSM2({
      quality: 1,
      previousInterval: 1,
      previousEasiness: 1.3, // Already low
      previousRepetitions: 1,
    });
    
    expect(result.easiness).toBeGreaterThanOrEqual(1.3);
  });
});
```

---

## Integration Testing

### Testing Feature Workflows

```typescript
// __tests__/integration/study/StudySession.integration.test.ts
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { StudySession } from '@/pages/study/StudySession';
import { ConvexTestProvider } from '@/test-utils/ConvexTestProvider';

describe('StudySession Integration', () => {
  it('should complete a full study session', async () => {
    render(
      <ConvexTestProvider>
        <StudySession deckId="test-deck" />
      </ConvexTestProvider>
    );
    
    // Wait for cards to load
    await waitFor(() => {
      expect(screen.getByText(/of/)).toBeInTheDocument();
    });
    
    // Get initial progress
    const initialText = screen.getByText(/1 \/ \d+/);
    
    // Flip card
    const card = screen.getByRole('article');
    fireEvent.click(card);
    
    // Verify back is shown
    await waitFor(() => {
      expect(screen.getByText(/Answer/)).toBeInTheDocument();
    });
    
    // Submit review
    const easyButton = screen.getByRole('button', { name: /Easy/i });
    fireEvent.click(easyButton);
    
    // Verify progress updated
    await waitFor(() => {
      const updatedText = screen.getByText(/2 \/ \d+/);
      expect(updatedText).toBeInTheDocument();
    });
  });
  
  it('should handle empty decks', async () => {
    render(
      <ConvexTestProvider emptyDeck>
        <StudySession deckId="empty-deck" />
      </ConvexTestProvider>
    );
    
    await waitFor(() => {
      expect(screen.getByText(/All cards reviewed/i)).toBeInTheDocument();
    });
  });
});
```

### Testing API Integration

```typescript
// __tests__/integration/fitness/WorkoutLogging.integration.test.ts
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { WorkoutLogger } from '@/pages/fitness/WorkoutLogger';
import { ConvexTestProvider } from '@/test-utils/ConvexTestProvider';

describe('Workout Logging Integration', () => {
  it('should log a complete workout', async () => {
    render(
      <ConvexTestProvider>
        <WorkoutLogger />
      </ConvexTestProvider>
    );
    
    const user = userEvent.setup();
    
    // Fill in exercise info
    const exerciseNameInput = screen.getByPlaceholderText(/Exercise name/i);
    await user.type(exerciseNameInput, 'Bench Press');
    
    const setsInput = screen.getByPlaceholderText(/Sets/);
    await user.clear(setsInput);
    await user.type(setsInput, '4');
    
    const repsInput = screen.getByPlaceholderText(/Reps/);
    await user.clear(repsInput);
    await user.type(repsInput, '8');
    
    const weightInput = screen.getByPlaceholderText(/Weight/);
    await user.type(weightInput, '100');
    
    // Submit
    const submitButton = screen.getByRole('button', { name: /Log Workout/i });
    fireEvent.click(submitButton);
    
    // Verify success
    await waitFor(() => {
      expect(screen.getByText(/Workout logged/i)).toBeInTheDocument();
    });
  });
});
```

---

## E2E Testing

### Setup Playwright Configuration

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './src/__tests__/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:5173',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI,
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],
});
```

### Authentication E2E Test

```typescript
// __tests__/e2e/auth.e2e.test.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication Flow', () => {
  test('should complete OTP login', async ({ page }) => {
    await page.goto('/');
    
    // Click login button
    await page.click('button:has-text("Login")');
    
    // Enter email
    await page.fill('input[type="email"]', 'test@example.com');
    await page.click('button:has-text("Send OTP")');
    
    // Verify OTP sent message
    expect(await page.locator('text=Check your email').isVisible()).toBeTruthy();
    
    // Mock OTP (in real scenario, get from email)
    const otp = '123456';
    
    // Enter OTP codes
    const inputs = await page.locator('input[data-testid="otp-input"]').all();
    for (let i = 0; i < inputs.length; i++) {
      await inputs[i].fill(otp[i]);
    }
    
    // Wait for redirect
    await page.waitForURL('/dashboard');
    
    // Verify logged in
    expect(await page.locator('text=Welcome').isVisible()).toBeTruthy();
  });
  
  test('should handle invalid OTP', async ({ page }) => {
    await page.goto('/login');
    
    await page.fill('input[type="email"]', 'test@example.com');
    await page.click('button:has-text("Send OTP")');
    
    // Enter wrong OTP
    const inputs = await page.locator('input[data-testid="otp-input"]').all();
    for (let i = 0; i < inputs.length; i++) {
      await inputs[i].fill('0');
    }
    
    // Submit
    await page.click('button:has-text("Verify")');
    
    // Verify error message
    expect(await page.locator('text=Invalid OTP').isVisible()).toBeTruthy();
  });
});
```

### Study Session E2E Test

```typescript
// __tests__/e2e/study.e2e.test.ts
import { test, expect } from '@playwright/test';

test.describe('Study Session E2E', () => {
  test.beforeEach(async ({ page }) => {
    // Login first
    await page.goto('/');
    await loginUser(page, 'test@example.com');
  });
  
  test('should complete study session with keyboard shortcuts', async ({ page }) => {
    await page.goto('/study/decks/test-deck');
    
    // Wait for first card to load
    await page.waitForSelector('[data-testid="flashcard"]');
    
    // Initial state - front side visible
    const front = await page.locator('text=Question').isVisible();
    expect(front).toBeTruthy();
    
    // Press space to flip
    await page.keyboard.press('Space');
    await page.waitForTimeout(500);
    
    // Back side visible
    const back = await page.locator('text=Answer').isVisible();
    expect(back).toBeTruthy();
    
    // Press 5 for easy
    await page.keyboard.press('5');
    await page.waitForTimeout(500);
    
    // Progress updated
    const progress = await page.locator('[data-testid="progress"]').innerText();
    expect(progress).toContain('2 /');
  });
  
  test('should show completion message', async ({ page }) => {
    await page.goto('/study/decks/test-deck-empty');
    
    await page.waitForSelector('[data-testid="completion-message"]');
    expect(await page.locator('text=All cards reviewed').isVisible()).toBeTruthy();
  });
});
```

---

## Jest Configuration

### Jest Config File

```typescript
// jest.config.js
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  roots: ['<rootDir>/src'],
  testMatch: ['**/__tests__/**/*.test.(ts|tsx)'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  setupFilesAfterEnv: ['<rootDir>/src/test-setup.ts'],
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/main.tsx',
    '!src/**/*.stories.tsx',
  ],
  coveragePathIgnorePatterns: [
    '/node_modules/',
    '/dist/',
  ],
  globals: {
    'ts-jest': {
      tsconfig: {
        jsx: 'react-jsx',
      },
    },
  },
};
```

### Test Setup File

```typescript
// src/test-setup.ts
import '@testing-library/jest-dom';

// Mock window.matchMedia
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});

// Mock IntersectionObserver
global.IntersectionObserver = class IntersectionObserver {
  constructor() {}
  disconnect() {}
  observe() {}
  takeRecords() {
    return [];
  }
  unobserve() {}
};
```

---

## Mock Strategies

### Mocking Convex Queries and Mutations

```typescript
// test-utils/ConvexTestProvider.tsx
import { ReactNode } from 'react';
import { ConvexProvider } from 'convex/react';
import { ConvexClient } from 'convex/browser';

const mockClient = new ConvexClient(
  process.env.REACT_APP_CONVEX_URL || ''
);

export function ConvexTestProvider({
  children,
}: {
  children: ReactNode;
}) {
  return (
    <ConvexProvider client={mockClient}>
      {children}
    </ConvexProvider>
  );
}
```

### Mocking API Calls

```typescript
// __tests__/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.post('/api/study/create-note', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({
      _id: '1',
      ...body,
      createdAt: Date.now(),
    });
  }),
  
  http.get('/api/flashcards/:deckId', () => {
    return HttpResponse.json({
      cards: [
        { _id: '1', front: 'Q1', back: 'A1' },
        { _id: '2', front: 'Q2', back: 'A2' },
      ],
    });
  }),
];
```

### MSW Setup

```typescript
// __tests__/mocks/server.ts
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);

// In setup file
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

---

## Coverage Targets

### Coverage Goals

| Type | Target | Current |
|------|--------|---------|
| Statements | 80% | - |
| Branches | 75% | - |
| Functions | 80% | - |
| Lines | 80% | - |

### Critical Paths (100% Coverage Required)

- Authentication flows
- Data mutations (create, update, delete)
- Payment processing (if any)
- Error handling
- Security-related code

### Coverage Report

```bash
# Generate coverage report
npm run test:coverage

# View HTML report
open coverage/index.html
```

---

## Testing Checklist

### Before Push to Main

- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] No console errors in tests
- [ ] Coverage meets targets (80%+)
- [ ] E2E tests pass on main paths
- [ ] No skipped tests (remove `skip` or `only`)
- [ ] Code changes have tests
- [ ] Mocks are cleaned up

### PR Testing Requirements

- [ ] New features have unit tests
- [ ] New components have snapshot tests
- [ ] Edge cases covered
- [ ] Error scenarios tested
- [ ] Performance tests for heavy operations
- [ ] Accessibility tests (keyboard nav, screen reader)
- [ ] Mobile responsive testing

### Deployment Testing

- [ ] All tests pass
- [ ] Coverage report reviewed
- [ ] Manual testing in staging
- [ ] Cross-browser testing (Chrome, Firefox, Safari)
- [ ] Mobile device testing
- [ ] Accessibility audit
- [ ] Performance audit

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** QA & Engineering Teams
