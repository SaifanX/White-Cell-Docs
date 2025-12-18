# Gamification System Documentation

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [XP System](#xp-system)
2. [Streak Mechanics](#streak-mechanics)
3. [Badges & Achievements](#badges--achievements)
4. [Leaderboards](#leaderboards)
5. [Reward Tiers](#reward-tiers)
6. [Psychological Principles](#psychological-principles)
7. [Engagement Metrics](#engagement-metrics)

---

## XP System

### XP Earning Rules

**Study Module**

| Action | XP | Conditions |
|--------|-----|-----------|
| Create note | 5 | +5 per 100 words |
| Create flashcard | 10 | +5 if complex |
| Review flashcard (Quality 1) | 5 | Failed (learning) |
| Review flashcard (Quality 2) | 10 | Hard (learning) |
| Review flashcard (Quality 3) | 15 | OK (consolidation) |
| Review flashcard (Quality 4) | 20 | Good (mastery) |
| Review flashcard (Quality 5) | 25 | Easy (expert) |
| Complete quiz | 25 | +10 if 80%+ |
| Create deck | 50 | First-time only |
| Achieve 100% deck mastery | 200 | One-time achievement |
| 30-day study streak | 500 | Streak bonus |

**Fitness Module**

| Action | XP | Conditions |
|--------|-----|-----------|
| Log workout | 25 | +5 per exercise |
| Personal record | 100 | All time or monthly |
| 7-day consistency | 75 | 4+ workouts/week |
| 30-day consistency | 300 | 4+ workouts/week |
| Log meal | 10 | Track nutrition |
| Progress photo | 50 | Visual documentation |
| 100 workouts | 250 | Lifetime milestone |
| Reach fitness goal | 500 | Goal achievement |

**Mind Module**

| Action | XP | Conditions |
|--------|-----|-----------|
| Complete chess puzzle | 10 | +5 if difficulty 1500+ |
| Speedcube solve | 5 | +10 if PB |
| Read wisdom | 2 | Auto-daily |
| Complete debate | 25 | All arguments |
| 7-day mind streak | 50 | Variety required |
| Master activity | 200 | 100+ completions |

**Discipline Module**

| Action | XP | Conditions |
|--------|-----|-----------|
| Complete task | 10 | +5 if priority |
| 7-day streak | 75 | Same habit |
| 30-day streak | 300 | Habit formation |
| 60-day streak | 750 | Mastery |
| 365-day streak | 5000 | Annual achievement |
| Recovery from streak loss | 25 | Restart building |
| Create habit stack | 50 | Multiple habits |

**Creator Module**

| Action | XP | Conditions |
|--------|-----|-----------|
| Publish content | 50 | All metadata complete |
| 1,000 views | 100 | Content milestone |
| 100 followers | 150 | Audience milestone |
| 1,000 followers | 1000 | Major milestone |
| High engagement | 25 | 5%+ engagement rate |
| Collaboration | 75 | Joint content |
| Monetization start | 200 | Revenue generation |

### XP Progression Curve

```
Level 1: 0-500 XP (Beginner)
Level 2: 500-1,500 XP (Apprentice)
Level 3: 1,500-5,000 XP (Expert)
Level 4: 5,000-10,000 XP (Master)
Level 5: 10,000-20,000 XP (Grandmaster)
Level 6: 20,000+ XP (Legend)

Each level requires:
- Level 1→2: 500 XP
- Level 2→3: 1,000 XP
- Level 3→4: 3,500 XP
- Level 4→5: 5,000 XP
- Level 5→6: 10,000 XP+

Cumulative growth ensures long-term engagement.
```

### XP Display & Tracking

```typescript
// components/XPDisplay.tsx
interface XPProps {
  current: number;
  nextLevel: number;
  level: number;
}

export function XPDisplay({ current, nextLevel, level }: XPProps) {
  const progress = (current / nextLevel) * 100;
  
  return (
    <div className="xp-display">
      <div className="xp-header">
        <span className="level">Level {level}</span>
        <span className="xp-text">{current} / {nextLevel} XP</span>
      </div>
      <div className="xp-bar">
        <div className="xp-fill" style={{ width: `${progress}%` }} />
      </div>
      <div className="xp-sublevel">
        {Math.round(progress)}% to next level
      </div>
    </div>
  );
}
```

---

## Streak Mechanics

### Streak Calculation

**Simple Streak:**
```typescript
interface StreakData {
  currentStreak: number;
  longestStreak: number;
  lastActivityDate: number;
  isActive: boolean; // Has activity today
  daysUntilLoss: number; // 1-2 days grace period
}

function calculateStreak(lastActivityDate: number): StreakData {
  const today = new Date().toDateString();
  const lastDate = new Date(lastActivityDate).toDateString();
  
  const daysSinceActivity = Math.floor(
    (Date.now() - lastActivityDate) / (1000 * 60 * 60 * 24)
  );
  
  const isActive = daysSinceActivity === 0;
  const isAlmostLost = daysSinceActivity === 1;
  
  return {
    currentStreak: isAlmostLost ? current - 1 : current,
    longestStreak: Math.max(current, longest),
    lastActivityDate,
    isActive,
    daysUntilLoss: isActive ? 1 : (1 - daysSinceActivity),
  };
}
```

### Streak Types

| Streak Type | Reset Condition | Grace Period | Reward |
|----------|------------------|---------------|---------|
| Daily Tasks | Miss 1 day | No | 25 XP/day |
| Habit Stack | Miss 1 day | 2 days | 50 XP/day |
| Study Sessions | Miss 3 days | No | 50 XP/session |
| Workouts | Miss 2 days | 1 day | 75 XP/session |

### Streak Warning System

```typescript
// hooks/useStreakWarning.ts
export function useStreakWarning() {
  const habits = useQuery(api.discipline.getActiveHabits);
  const [warnings, setWarnings] = useState<StreakWarning[]>([]);
  
  useEffect(() => {
    if (!habits) return;
    
    const newWarnings = habits
      .filter(h => h.streak.daysUntilLoss <= 1)
      .map(h => ({
        habitId: h._id,
        habitName: h.name,
        daysUntilLoss: h.streak.daysUntilLoss,
        currentStreak: h.streak.currentStreak,
        severity: h.streak.daysUntilLoss === 0 ? 'critical' : 'warning',
      }));
    
    setWarnings(newWarnings);
  }, [habits]);
  
  return warnings;
}

// Component
function StreakWarnings() {
  const warnings = useStreakWarning();
  
  return (
    <>
      {warnings.map(w => (
        <Alert
          key={w.habitId}
          variant={w.severity === 'critical' ? 'error' : 'warning'}
        >
          {w.severity === 'critical' ? (
            <>⚠️ Last day for {w.habitName}!</>
          ) : (
            <>📍 {w.habitName} due tomorrow</>
          )}
          {w.currentStreak > 0 && (
            <span> 🔥 {w.currentStreak} day streak</span>
          )}
        </Alert>
      ))}
    </>
  );
}
```

---

## Badges & Achievements

### Badge Categories

**Progression Badges (Auto-Unlock)**

| Badge | Requirement | Rarity |
|-------|------------|--------|
| 🏅 First Step | Create 1 flashcard | Common |
| 📚 Book Lover | Create 50 flashcards | Common |
| 🧠 Scholar | Master 1 deck | Uncommon |
| 🎓 PhD Candidate | Master 5 decks | Rare |
| 💪 Gym Rat | Log 10 workouts | Common |
| 💯 Powerlifter | Hit 5 PRs | Uncommon |
| 🔥 Unstoppable | 30-day streak | Rare |
| ⚡ Legend | Level 5+ | Epic |

**Skill Badges (Conditional)**

| Badge | Requirement | Skill |
|-------|------------|-------|
| ♟️ Chess Master | 100 puzzles, 80%+ solve | Strategy |
| ⏱️ Speedcube Elite | Sub-30 second solve | Dexterity |
| 🗣️ Orator | 20+ debates won | Communication |
| 📝 Author | 50,000 word notes | Knowledge |
| 🎬 Content King | 500K total views | Creation |

**Challenge Badges (Monthly)**

| Badge | Requirement | Reward |
|-------|------------|--------|
| 🎯 Consistent | 4+ weeks of daily | 250 XP |
| 🚀 Momentum | 30%+ XP over goal | 200 XP |
| 🌟 Rising Star | Top 10% growth | 150 XP |

### Badge Implementation

```typescript
// Convex mutation
export const awardBadge = async (
  ctx: MutationCtx,
  args: {
    userId: string;
    badgeId: string;
  }
) => {
  const userId = await ctx.auth.getUserIdentity();
  if (!userId) throw new Error('Not authenticated');
  
  // Check if already awarded
  const existing = await ctx.db
    .query('badges')
    .filter(q =>
      q.and(
        q.eq(q.field('userId'), userId.subject),
        q.eq(q.field('badgeId'), args.badgeId)
      )
    )
    .first();
  
  if (existing) return existing;
  
  // Award badge
  const badgeId = await ctx.db.insert('badges', {
    userId: userId.subject,
    badgeId: args.badgeId,
    awardedAt: Date.now(),
    isNew: true,
  });
  
  // Send notification
  await ctx.runAction(api.notifications.sendBadgeEarned, {
    userId: userId.subject,
    badgeId: args.badgeId,
  });
  
  return await ctx.db.get(badgeId);
};
```

---

## Leaderboards

### Leaderboard Types

**Global XP Leaderboard**
```
Rank | User          | XP      | Level | Streak
1    | StudyMaster   | 125,000 | 6     | 365 days
2    | FitnessKing   | 98,500  | 5     | 120 days
3    | MindGuru      | 87,200  | 5     | 95 days
```

**Module-Specific**
- Study: Cards mastered
- Fitness: Total volume lifted
- Mind: Puzzles solved
- Discipline: Days without relapse
- Creator: Total followers

**Time-Based**
- Weekly: Most XP earned this week
- Monthly: Most activity streaks
- All-time: Highest level

### Leaderboard Implementation

```typescript
export const getLeaderboard = async (
  ctx: QueryCtx,
  args: {
    type: 'global' | 'study' | 'fitness' | 'mind' | 'discipline' | 'creator';
    period: 'week' | 'month' | 'all';
    limit?: number;
  }
) => {
  const limit = args.limit ?? 100;
  
  let query = ctx.db.query('users').order('desc', q =>
    q.field('stats.totalXP')
  );
  
  // Filter by period
  if (args.period !== 'all') {
    const days = args.period === 'week' ? 7 : 30;
    const since = Date.now() - days * 24 * 60 * 60 * 1000;
    query = query.filter(q =>
      q.gt(q.field('stats.lastActivityDate'), since)
    );
  }
  
  // Filter by module
  if (args.type !== 'global') {
    query = query.filter(q =>
      q.gt(q.field(`stats.${args.type}.totalXP`), 0)
    );
  }
  
  const users = await query.take(limit);
  
  return users.map((user, index) => ({
    rank: index + 1,
    userId: user._id,
    username: user.username,
    avatar: user.avatar,
    totalXP: user.stats.totalXP,
    level: user.stats.level,
    streak: user.stats.currentStreak,
  }));
};
```

---

## Reward Tiers

### Milestone Rewards

```
Every 1,000 XP:
├─ 1,000 XP: 📱 Unlock new theme
├─ 2,000 XP: 🎁 Cosmetic reward
├─ 5,000 XP: ⭐ Premium feature (temporary)
├─ 10,000 XP: 🏆 Permanent feature unlock
└─ 20,000 XP: 👑 Exclusive badge
```

### Tier Progression

| Tier | XP Range | Benefits | Unlocks |
|------|---------|----------|---------|
| Bronze | 0-2,500 | Basic features | Themes |
| Silver | 2,500-7,500 | +1 study deck | Advanced notes |
| Gold | 7,500-20,000 | Premium themes | Custom schedules |
| Platinum | 20,000+ | All features | Beta access |

---

## Psychological Principles

### 1. Variable Rewards

```typescript
// Random reward multipliers
function getRewardMultiplier(): number {
  const random = Math.random();
  if (random < 0.05) return 3; // 5% chance for 3x
  if (random < 0.15) return 2; // 10% chance for 2x
  return 1; // 85% chance for 1x
}

// Applied to XP
const bonusXP = baseXP * getRewardMultiplier();
```

### 2. Progress Visualization

```typescript
// Show immediate progress
function handleCardReview(quality: number) {
  showXPAnimation(quality * 5, {
    position: { x, y },
    duration: 1000,
    color: getQualityColor(quality),
  });
}
```

### 3. Loss Aversion

```typescript
// Streak warning before loss
if (daysUntilStreak === 0) {
  sendUrgentNotification(
    '⚠️ Your streak ends TODAY!',
    { sound: true, vibration: true }
  );
}
```

### 4. Social Proof

```typescript
// Show leaderboard position
<Leaderboard position={userRank} total={totalUsers} />

// Share achievements
<ShareButton 
  text={`I just got ${badge.name}! 🏆`}
/>
```

### 5. Commitment & Consistency

```typescript
// Public goals
<PublicGoal 
  goal="Study 500 cards"
  followers={followers}
  progress={progress}
/>
```

---

## Engagement Metrics

### Metrics to Track

```typescript
interface EngagementMetrics {
  // Daily
  dailyActiveUsers: number;
  averageSessionDuration: number;
  sessionsPerUser: number;
  
  // XP
  averageXPPerDay: number;
  xpEarningDistribution: Record<string, number>;
  
  // Streaks
  activeStreaks: number;
  averageStreakLength: number;
  streakRetention: number; // % keeping streaks
  
  // Badges
  badgesUnlockedPerUser: number;
  commonBadges: Badge[];
  rareBadges: Badge[];
  
  // Leaderboard
  leaderboardEngagement: number; // % checking
  competitiveIndex: number;
  
  // Churn
  churnRate: number; // Lost streaks
  returnRate: number; // Day 1 to Day 7
}
```

### Engagement Dashboard

```typescript
// Query
export const getEngagementStats = async (ctx: QueryCtx, args: {
  period: 'day' | 'week' | 'month';
}) => {
  const since = getPeriodStart(args.period);
  
  const [activeUsers, xpData, streakData, badgeData] = await Promise.all([
    getActiveUsers(since),
    getXPStats(since),
    getStreakStats(since),
    getBadgeStats(since),
  ]);
  
  return {
    activeUsers: activeUsers.length,
    totalXPGenerated: xpData.total,
    averageXPPerUser: xpData.total / activeUsers.length,
    activeStreaks: streakData.count,
    averageStreakLength: streakData.average,
    badgesAwarded: badgeData.count,
    topBadges: badgeData.top5,
    retention: calculateRetention(since),
  };
};
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Product & Analytics Teams
