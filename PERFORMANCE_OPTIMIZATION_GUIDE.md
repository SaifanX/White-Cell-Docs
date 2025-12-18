# Performance Optimization Guide

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Bundle Size Optimization](#bundle-size-optimization)
2. [Query Optimization](#query-optimization)
3. [Component Rendering](#component-rendering)
4. [Image Optimization](#image-optimization)
5. [Caching Strategies](#caching-strategies)
6. [Monitoring & Analysis](#monitoring--analysis)

---

## Bundle Size Optimization

### Current Metrics Target

| Metric | Target | Current |
|--------|--------|---------|
| Main bundle | <100KB | Monitor |
| Total JS | <250KB | Monitor |
| CSS | <30KB | Monitor |
| Initial load | <2s | Monitor |
| Time to Interactive | <3s | Monitor |

### Code Splitting Strategy

```typescript
// pages/index.tsx - Main router
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Study = lazy(() => import('./pages/modules/Study'));
const Fitness = lazy(() => import('./pages/modules/Fitness'));
const Mind = lazy(() => import('./pages/modules/Mind'));
const Discipline = lazy(() => import('./pages/modules/Discipline'));
const Creator = lazy(() => import('./pages/modules/Creator'));

export function Router() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/study/*" element={<Study />} />
        <Route path="/fitness/*" element={<Fitness />} />
        <Route path="/mind/*" element={<Mind />} />
        <Route path="/discipline/*" element={<Discipline />} />
        <Route path="/creator/*" element={<Creator />} />
      </Routes>
    </Suspense>
  );
}
```

### Dependency Analysis

```bash
# Analyze bundle size
npm run build:analyze

# Expected output:
# React: 42KB (gzipped)
# Convex: 35KB (gzipped)
# UI Components: 28KB (gzipped)
# Utilities: 15KB (gzipped)
# Other: 20KB (gzipped)
```

### Optimization Checklist

- [ ] Remove unused dependencies
- [ ] Tree-shake all exports
- [ ] Use dynamic imports for large features
- [ ] Compress images before shipping
- [ ] Enable gzip compression on server
- [ ] Monitor with bundle analyzer

---

## Query Optimization

### Pagination Pattern

```typescript
// Bad: Load all notes
export const getAllNotes = async (ctx: QueryCtx) => {
  return await ctx.db.query('notes').collect(); // Could be 10,000+!
};

// Good: Paginated
export const getPaginatedNotes = async (
  ctx: QueryCtx,
  args: { page: number; pageSize: number }
) => {
  const skip = (args.page - 1) * args.pageSize;
  
  const notes = await ctx.db
    .query('notes')
    .order('desc', q => q.field('createdAt'))
    .skip(skip)
    .take(args.pageSize)
    .collect();
  
  const total = await ctx.db
    .query('notes')
    .count();
  
  return { notes, total, pageCount: Math.ceil(total / args.pageSize) };
};
```

### Selective Field Queries

```typescript
// Bad: Load all fields
const card = await ctx.db.get(cardId);
// Returns: {
//   _id, _creationTime,
//   front, back, deckId,
//   review: { nextReviewDate, ... },
//   metadata: { ... },
//   ...
// }

// Good: Request only needed fields
export const getFlashcardForReview = async (
  ctx: QueryCtx,
  args: { cardId: string }
) => {
  const card = await ctx.db.get(args.cardId);
  return {
    _id: card._id,
    front: card.front,
    back: card.back,
    review: {
      nextReviewDate: card.review.nextReviewDate,
      quality: card.review.quality,
    },
  };
};
```

### Index Strategy

```typescript
// Define indexes in convex.json
{
  "indexes": [
    {
      "tableName": "flashcards",
      "fields": ["userId", "deckId", "review.nextReviewDate"]
    },
    {
      "tableName": "notes",
      "fields": ["userId", "createdAt"]
    },
    {
      "tableName": "workouts",
      "fields": ["userId", "date"]
    },
    {
      "tableName": "xp_logs",
      "fields": ["userId", "timestamp"]
    }
  ]
}
```

### Query Caching

```typescript
// Frontend cache with SWR
import useSWR from 'swr';

function StudyStats() {
  const { data, error } = useSWR(
    'study-stats',
    () => client.query(api.study.getUserStudyStats),
    {
      revalidateOnFocus: false,
      revalidateOnReconnect: true,
      dedupingInterval: 60000, // Cache 1 minute
    }
  );
  
  return <StatsDisplay stats={data} />;
}
```

---

## Component Rendering

### Memoization

```typescript
// Prevent unnecessary re-renders
const FlashcardCard = memo(
  function FlashcardCard({ card, onReview }: Props) {
    return (
      <div className="card">
        <h3>{card.front}</h3>
        <button onClick={() => onReview(card._id)}>Review</button>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Custom comparison
    return (
      prevProps.card._id === nextProps.card._id &&
      prevProps.card.front === nextProps.card.front
    );
  }
);
```

### useMemo for Expensive Calculations

```typescript
// Calculate stats only when dependencies change
const StudyStats = ({ cards }: { cards: Flashcard[] }) => {
  const stats = useMemo(() => {
    return {
      total: cards.length,
      dueToday: cards.filter(c => c.review.nextReviewDate < Date.now()).length,
      averageInterval: cards.reduce((sum, c) => sum + c.review.interval, 0) / cards.length,
      totalReviews: cards.reduce((sum, c) => sum + c.review.repetitions, 0),
    };
  }, [cards]);
  
  return <div>{stats.total} cards</div>;
};
```

### Virtual Scrolling for Large Lists

```typescript
import { FixedSizeList } from 'react-window';

function NotesList({ notes }: { notes: Note[] }) {
  return (
    <FixedSizeList
      height={600}
      itemCount={notes.length}
      itemSize={100}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>
          <NoteItem note={notes[index]} />
        </div>
      )}
    </FixedSizeList>
  );
}
```

### useCallback for Function Stability

```typescript
function FlashcardReview({ card }: { card: Flashcard }) {
  const [reviewData, setReviewData] = useState<ReviewData[]>([]);
  
  // Stable function reference prevents child re-renders
  const handleReview = useCallback(async (quality: number) => {
    try {
      await reviewCard({ cardId: card._id, quality });
      setReviewData(prev => [...prev, { quality, timestamp: Date.now() }]);
    } catch (err) {
      console.error(err);
    }
  }, [card._id]);
  
  return <QualityButtons onRate={handleReview} />;
}
```

---

## Image Optimization

### Responsive Images

```typescript
// Serve appropriately sized images
function OptimizedImage({ src, alt }: { src: string; alt: string }) {
  return (
    <picture>
      <source
        media="(max-width: 640px)"
        srcSet={`${src}?w=640&q=80`}
      />
      <source
        media="(max-width: 1024px)"
        srcSet={`${src}?w=1024&q=80`}
      />
      <img
        src={`${src}?w=1920&q=80`}
        alt={alt}
        loading="lazy"
        decoding="async"
      />
    </picture>
  );
}
```

### Image Compression

```bash
# Compress images before upload
npm install sharp

// Build script
const sharp = require('sharp');

sharp('progress-photo.jpg')
  .resize(1920, 1080, { fit: 'cover' })
  .webp({ quality: 80 })
  .toFile('progress-photo.webp');
```

### Lazy Loading

```typescript
// Lazy load images below the fold
<img
  src="profile-photo.jpg"
  alt="User profile"
  loading="lazy"
  decoding="async"
/>

// With Intersection Observer for custom behavior
const useImageLazyLoad = (ref: RefObject<HTMLImageElement>) => {
  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting && ref.current) {
        ref.current.src = ref.current.dataset.src!;
        observer.unobserve(ref.current);
      }
    });
    
    if (ref.current) observer.observe(ref.current);
    return () => observer.disconnect();
  }, [ref]);
};
```

---

## Caching Strategies

### HTTP Caching Headers

```typescript
// In your server (e.g., Vercel deployment)
vercel.json:
{
  "headers": [
    {
      "source": "/assets/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }
      ]
    },
    {
      "source": "/",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=3600, s-maxage=3600" }
      ]
    }
  ]
}
```

### Service Worker Cache

```typescript
// public/sw.js - Cache network responses
const CACHE_NAME = 'app-v1';

self.addEventListener('fetch', event => {
  const { request } = event;
  
  // API requests - Network first
  if (request.url.includes('/api')) {
    event.respondWith(
      fetch(request)
        .then(response => {
          const clone = response.clone();
          caches.open(CACHE_NAME).then(cache => {
            cache.put(request, clone);
          });
          return response;
        })
        .catch(() => caches.match(request))
    );
  }
  
  // Static assets - Cache first
  else {
    event.respondWith(
      caches.match(request).then(response =>
        response || fetch(request)
      )
    );
  }
});
```

### React Query Cache

```typescript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 minutes
      cacheTime: 1000 * 60 * 10, // 10 minutes
      refetchOnWindowFocus: false,
      retry: 1,
    },
  },
});
```

---

## Monitoring & Analysis

### Web Vitals Tracking

```typescript
// lib/metrics.ts
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

export function reportWebVitals() {
  getCLS(metric => {
    console.log('CLS:', metric.value);
    sendMetric('CLS', metric.value);
  });
  
  getFID(metric => {
    console.log('FID:', metric.value);
    sendMetric('FID', metric.value);
  });
  
  getFCP(metric => {
    console.log('FCP:', metric.value);
    sendMetric('FCP', metric.value);
  });
  
  getLCP(metric => {
    console.log('LCP:', metric.value);
    sendMetric('LCP', metric.value);
  });
  
  getTTFB(metric => {
    console.log('TTFB:', metric.value);
    sendMetric('TTFB', metric.value);
  });
}

function sendMetric(name: string, value: number) {
  // Send to analytics
  if (navigator.sendBeacon) {
    navigator.sendBeacon('/api/metrics', JSON.stringify({
      name,
      value,
      timestamp: Date.now(),
    }));
  }
}
```

### Performance Monitoring

```typescript
// Track function execution time
function withTiming<T extends any[], R>(
  fn: (...args: T) => Promise<R>,
  label: string
) {
  return async (...args: T): Promise<R> => {
    const start = performance.now();
    try {
      return await fn(...args);
    } finally {
      const end = performance.now();
      console.log(`${label} took ${end - start}ms`);
      sendMetric(label, end - start);
    }
  };
}

// Usage
const optimizedQuery = withTiming(
  api.study.getFlashcardsDueForReview,
  'getFlashcardsDueForReview'
);
```

### Lighthouse Targets

| Metric | Target |
|--------|--------|
| Performance | 90+ |
| Accessibility | 95+ |
| Best Practices | 90+ |
| SEO | 95+ |

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Performance & Engineering Teams
