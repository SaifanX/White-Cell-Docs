# Integration Guides

**Version:** 1.0
**Last Updated:** December 18, 2025

---

## Table of Contents

1. [Lichess Integration](#lichess-integration)
2. [Spotify Integration](#spotify-integration)
3. [GitHub Integration](#github-integration)
4. [Notion Integration](#notion-integration)
5. [Email/SMS Providers](#emailsms-providers)
6. [Weather API](#weather-api)
7. [Troubleshooting](#troubleshooting)

---

## Lichess Integration

### Setup Instructions

**1. Register Application**
- Go to https://lichess.org/account/oauth/app
- Create new application
- Set redirect URI: `https://yourapp.com/auth/lichess/callback`
- Save Client ID and Secret

**2. Environment Variables**

```env
VITE_LICHESS_CLIENT_ID=your_client_id
VITE_LICHESS_REDIRECT_URI=https://yourapp.com/auth/lichess/callback
CONVEX_LICHESS_CLIENT_ID=your_client_id
CONVEX_LICHESS_REDIRECT_URI=https://yourapp.com/auth/lichess/callback
CONVEX_LICHESS_SECRET=your_client_secret
```

### OAuth Flow

```typescript
// Frontend: Initiate login
export function LichessConnect() {
  const handleConnect = () => {
    const clientId = import.meta.env.VITE_LICHESS_CLIENT_ID;
    const redirectUri = import.meta.env.VITE_LICHESS_REDIRECT_URI;
    
    const authUrl = new URL('https://lichess.org/oauth');
    authUrl.searchParams.append('client_id', clientId);
    authUrl.searchParams.append('redirect_uri', redirectUri);
    authUrl.searchParams.append('response_type', 'code');
    authUrl.searchParams.append('scope', 'puzzle:read');
    
    window.location.href = authUrl.toString();
  };
  
  return (
    <button onClick={handleConnect}>
      Connect Lichess Account
    </button>
  );
}

// Backend: Handle callback
export const handleLichessCallback = async (
  ctx: ActionCtx,
  args: { code: string }
) => {
  const clientId = process.env.CONVEX_LICHESS_CLIENT_ID;
  const clientSecret = process.env.CONVEX_LICHESS_SECRET;
  const redirectUri = process.env.CONVEX_LICHESS_REDIRECT_URI;
  
  // Exchange code for access token
  const tokenResponse = await fetch('https://lichess.org/api/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      grant_type: 'authorization_code',
      code: args.code,
      client_id: clientId,
      client_secret: clientSecret,
      redirect_uri: redirectUri,
    }),
  });
  
  const { access_token } = await tokenResponse.json();
  
  // Get user profile
  const userResponse = await fetch('https://lichess.org/api/account', {
    headers: { Authorization: `Bearer ${access_token}` },
  });
  
  const lichessUser = await userResponse.json();
  
  // Store in database
  return {
    lichessId: lichessUser.id,
    lichessUsername: lichessUser.username,
    accessToken: access_token,
  };
};
```

### Puzzle API Integration

```typescript
// Fetch puzzles
export const fetchLichessChessPuzzles = async (
  ctx: ActionCtx,
  args: {
    userId: string;
    count: number;
  }
) => {
  const user = await ctx.runQuery(api.users.getUserById, {
    userId: args.userId,
  });
  
  if (!user?.lichess?.accessToken) {
    throw new Error('Lichess not connected');
  }
  
  const puzzles = [];
  
  // Fetch puzzles
  const response = await fetch(
    `https://lichess.org/api/puzzle/activity?max=${args.count}`,
    {
      headers: {
        Authorization: `Bearer ${user.lichess.accessToken}`,
      },
    }
  );
  
  const data = await response.json();
  
  for (const puzzle of data) {
    const puzzleData = await ctx.db.insert('chess_puzzles', {
      userId: args.userId,
      lichessId: puzzle.puzzle.id,
      rating: puzzle.puzzle.rating,
      attempts: puzzle.puzzle.attempts,
      success: puzzle.puzzle.success,
      fen: puzzle.puzzle.fen,
      moves: puzzle.puzzle.moves,
      fetchedAt: Date.now(),
    });
    
    puzzles.push(puzzleData);
  }
  
  return puzzles;
};

// Component
function LichessChessTab() {
  const puzzles = useQuery(api.mind.getChessPuzzles);
  const fetchMoreMutation = useMutation(api.mind.fetchLichessChessPuzzles);
  
  const handleFetchMore = async () => {
    await fetchMoreMutation({ count: 5 });
  };
  
  return (
    <div>
      {puzzles?.map(puzzle => (
        <PuzzleCard key={puzzle._id} puzzle={puzzle} />
      ))}
      <button onClick={handleFetchMore}>Fetch More Puzzles</button>
    </div>
  );
}
```

---

## Spotify Integration

### Setup Instructions

**1. Create Spotify App**
- Go to https://developer.spotify.com/dashboard
- Create new application
- Accept terms and create app
- Copy Client ID and Client Secret
- Add redirect URI: `https://yourapp.com/auth/spotify/callback`

**2. Environment Variables**

```env
VITE_SPOTIFY_CLIENT_ID=your_client_id
VITE_SPOTIFY_REDIRECT_URI=https://yourapp.com/auth/spotify/callback
CONVEX_SPOTIFY_CLIENT_ID=your_client_id
CONVEX_SPOTIFY_REDIRECT_URI=https://yourapp.com/auth/spotify/callback
CONVEX_SPOTIFY_SECRET=your_client_secret
```

### OAuth Flow

```typescript
// Frontend: Initiate Spotify login
export function SpotifyConnect() {
  const handleConnect = () => {
    const clientId = import.meta.env.VITE_SPOTIFY_CLIENT_ID;
    const redirectUri = import.meta.env.VITE_SPOTIFY_REDIRECT_URI;
    
    const scope = [
      'user-read-private',
      'user-read-email',
      'user-read-currently-playing',
      'user-top-read',
      'playlist-read-private',
    ].join('%20');
    
    const authUrl = new URL('https://accounts.spotify.com/authorize');
    authUrl.searchParams.append('client_id', clientId);
    authUrl.searchParams.append('response_type', 'code');
    authUrl.searchParams.append('redirect_uri', redirectUri);
    authUrl.searchParams.append('scope', scope);
    
    window.location.href = authUrl.toString();
  };
  
  return (
    <button onClick={handleConnect}>
      Connect Spotify
    </button>
  );
}

// Backend: Handle Spotify callback
export const handleSpotifyCallback = async (
  ctx: ActionCtx,
  args: { code: string }
) => {
  const clientId = process.env.CONVEX_SPOTIFY_CLIENT_ID;
  const clientSecret = process.env.CONVEX_SPOTIFY_SECRET;
  const redirectUri = process.env.CONVEX_SPOTIFY_REDIRECT_URI;
  
  // Get access token
  const tokenResponse = await fetch('https://accounts.spotify.com/api/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      'Authorization': `Basic ${btoa(`${clientId}:${clientSecret}`)}`,
    },
    body: new URLSearchParams({
      grant_type: 'authorization_code',
      code: args.code,
      redirect_uri: redirectUri,
    }),
  });
  
  const tokens = await tokenResponse.json();
  
  // Get user profile
  const profileResponse = await fetch('https://api.spotify.com/v1/me', {
    headers: { Authorization: `Bearer ${tokens.access_token}` },
  });
  
  const profile = await profileResponse.json();
  
  return {
    spotifyId: profile.id,
    spotifyEmail: profile.email,
    accessToken: tokens.access_token,
    refreshToken: tokens.refresh_token,
  };
};
```

### Music Playback Integration

```typescript
// Get currently playing track
export const getCurrentlyPlaying = async (
  ctx: ActionCtx,
  args: { userId: string }
) => {
  const user = await ctx.runQuery(api.users.getUserById, {
    userId: args.userId,
  });
  
  if (!user?.spotify?.accessToken) {
    return null;
  }
  
  const response = await fetch(
    'https://api.spotify.com/v1/me/player/currently-playing',
    {
      headers: {
        Authorization: `Bearer ${user.spotify.accessToken}`,
      },
    }
  );
  
  if (response.status === 204) return null; // No track playing
  
  const data = await response.json();
  
  return {
    trackId: data.item.id,
    trackName: data.item.name,
    artists: data.item.artists.map(a => a.name),
    imageUrl: data.item.album.images[0]?.url,
    isPlaying: data.is_playing,
    progressMs: data.progress_ms,
    durationMs: data.item.duration_ms,
  };
};
```

---

## GitHub Integration

### Setup Instructions

**1. Create OAuth App**
- Go to GitHub Settings → Developer settings → OAuth Apps
- Create new OAuth App
- Set Authorization callback URL: `https://yourapp.com/auth/github/callback`
- Copy Client ID and Client Secret

**2. Environment Variables**

```env
VITE_GITHUB_CLIENT_ID=your_client_id
VITE_GITHUB_REDIRECT_URI=https://yourapp.com/auth/github/callback
CONVEX_GITHUB_CLIENT_ID=your_client_id
CONVEX_GITHUB_REDIRECT_URI=https://yourapp.com/auth/github/callback
CONVEX_GITHUB_SECRET=your_client_secret
```

### User Profile Integration

```typescript
export const handleGithubCallback = async (
  ctx: ActionCtx,
  args: { code: string }
) => {
  const clientId = process.env.CONVEX_GITHUB_CLIENT_ID;
  const clientSecret = process.env.CONVEX_GITHUB_SECRET;
  
  // Get access token
  const tokenResponse = await fetch('https://github.com/login/oauth/access_token', {
    method: 'POST',
    headers: { 'Accept': 'application/json' },
    body: JSON.stringify({
      client_id: clientId,
      client_secret: clientSecret,
      code: args.code,
    }),
  });
  
  const { access_token } = await tokenResponse.json();
  
  // Get user profile
  const userResponse = await fetch('https://api.github.com/user', {
    headers: { Authorization: `Bearer ${access_token}` },
  });
  
  const profile = await userResponse.json();
  
  return {
    githubId: profile.id,
    githubUsername: profile.login,
    githubUrl: profile.html_url,
    avatar: profile.avatar_url,
    bio: profile.bio,
    accessToken: access_token,
  };
};
```

---

## Notion Integration

### Setup Instructions

**1. Create Integration**
- Go to https://www.notion.so/my-integrations
- Create new integration
- Copy Internal Integration Token
- Share database with integration

**2. Environment Variables**

```env
CONVEX_NOTION_API_KEY=your_integration_token
VITE_NOTION_DATABASE_ID=your_database_id
```

### Sync Notes to Notion

```typescript
// Export notes to Notion
export const syncNotesToNotion = async (
  ctx: ActionCtx,
  args: {
    userId: string;
    deckId: string;
  }
) => {
  const notionKey = process.env.CONVEX_NOTION_API_KEY;
  const databaseId = process.env.VITE_NOTION_DATABASE_ID;
  
  // Get notes
  const notes = await ctx.runQuery(api.study.getNotes, {
    userId: args.userId,
    deckId: args.deckId,
  });
  
  // Create pages in Notion
  const createdPages = [];
  
  for (const note of notes) {
    const response = await fetch(
      'https://api.notion.com/v1/pages',
      {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${notionKey}`,
          'Content-Type': 'application/json',
          'Notion-Version': '2022-06-28',
        },
        body: JSON.stringify({
          parent: { database_id: databaseId },
          properties: {
            Title: {
              title: [{ text: { content: note.title } }],
            },
            Content: {
              rich_text: [{ text: { content: note.content } }],
            },
            Tags: {
              multi_select: note.tags.map(t => ({ name: t })),
            },
            'Created At': {
              date: { start: new Date(note.createdAt).toISOString() },
            },
          },
        }),
      }
    );
    
    const page = await response.json();
    createdPages.push(page);
  }
  
  return createdPages;
};

// Component
function SyncNotionButton({ deckId }: { deckId: string }) {
  const syncMutation = useMutation(api.study.syncNotesToNotion);
  const [syncing, setSyncing] = useState(false);
  
  const handleSync = async () => {
    setSyncing(true);
    try {
      await syncMutation({ deckId });
      toast.success('Notes synced to Notion!');
    } catch (err) {
      toast.error('Sync failed');
    } finally {
      setSyncing(false);
    }
  };
  
  return (
    <button onClick={handleSync} disabled={syncing}>
      {syncing ? 'Syncing...' : 'Sync to Notion'}
    </button>
  );
}
```

---

## Email/SMS Providers

### Resend (Email OTP)

```typescript
// Environment
CONVEX_RESEND_API_KEY=your_api_key

// Send OTP
export const sendOtpViaResend = async (
  ctx: ActionCtx,
  args: { email: string; code: string }
) => {
  const apiKey = process.env.CONVEX_RESEND_API_KEY;
  
  const response = await fetch('https://api.resend.com/emails', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${apiKey}`,
    },
    body: JSON.stringify({
      from: 'White Cell Protocol <noreply@whitecellandprotocol.com>',
      to: args.email,
      subject: 'Your verification code',
      html: `
        <div>
          <h2>Welcome to White Cell Protocol</h2>
          <p>Your verification code is:</p>
          <h1 style="letter-spacing: 8px;">${args.code}</h1>
          <p>This code expires in 10 minutes.</p>
        </div>
      `,
    }),
  });
  
  return await response.json();
};
```

### Twilio (SMS OTP)

```typescript
// Environment
CONVEX_TWILIO_ACCOUNT_SID=your_account_sid
CONVEX_TWILIO_AUTH_TOKEN=your_auth_token
CONVEX_TWILIO_PHONE_NUMBER=your_phone_number

// Send SMS OTP
export const sendOtpViaTwilio = async (
  ctx: ActionCtx,
  args: { phone: string; code: string }
) => {
  const accountSid = process.env.CONVEX_TWILIO_ACCOUNT_SID;
  const authToken = process.env.CONVEX_TWILIO_AUTH_TOKEN;
  const fromNumber = process.env.CONVEX_TWILIO_PHONE_NUMBER;
  
  const response = await fetch(
    `https://api.twilio.com/2010-04-01/Accounts/${accountSid}/Messages.json`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Basic ${btoa(`${accountSid}:${authToken}`)}`,
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: new URLSearchParams({
        From: fromNumber,
        To: args.phone,
        Body: `Your White Cell Protocol verification code is: ${args.code}`,
      }),
    }
  );
  
  return await response.json();
};
```

---

## Weather API

### Setup Instructions

**Environment:**
```env
CONVEX_WEATHER_API_KEY=your_api_key
```

### Get Weather for Workout

```typescript
export const getWeatherForWorkout = async (
  ctx: ActionCtx,
  args: { latitude: number; longitude: number }
) => {
  const apiKey = process.env.CONVEX_WEATHER_API_KEY;
  
  const response = await fetch(
    `https://api.openweathermap.org/data/2.5/weather?lat=${args.latitude}&lon=${args.longitude}&appid=${apiKey}&units=metric`
  );
  
  const data = await response.json();
  
  return {
    temperature: data.main.temp,
    humidity: data.main.humidity,
    condition: data.weather[0].main,
    windSpeed: data.wind.speed,
    feelsLike: data.main.feels_like,
  };
};
```

---

## Troubleshooting

### OAuth Issues

| Issue | Solution |
|-------|----------|
| Redirect URI mismatch | Check exact match with registered URI (include protocol, domain, path) |
| Invalid client secret | Verify secret in environment, not in public code |
| CORS errors | Use backend actions, not direct frontend calls |
| Token expired | Implement refresh token logic |

### Rate Limiting

```typescript
// Add rate limiting to API calls
import { RateLimiter } from 'limiter';

const limiter = new RateLimiter({
  tokensPerInterval: 10,
  interval: 'minute',
});

export const fetchWithRateLimit = async (url: string) => {
  await limiter.removeTokens(1);
  return fetch(url);
};
```

### Error Handling

```typescript
// Handle API errors gracefully
export async function safeApiCall<T>(
  fn: () => Promise<T>,
  fallback: T
): Promise<T> {
  try {
    return await fn();
  } catch (err) {
    console.error('API call failed:', err);
    return fallback;
  }
}
```

---

**Last Updated:** December 18, 2025
**Next Review:** March 2026
**Maintained By:** Integration & Backend Teams
