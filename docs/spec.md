# Code Stories — Feature Specification

**Version:** 0.1
**Status:** Draft

---

## 1. Overview

### Problem Statement

Learning DSA for interviews is brutal. Leetcode is text-heavy, abstract, and isolating. Most people learn algorithms by reading walls of text or watching hour-long lecture videos they never finish.

**Code Stories** flips the format: each Leetcode problem or DSA concept gets a 60–120 second visual story — narrated, animated, step-by-step. The feed is vertical and swipeable (like Reels), so you learn on the go without commitment.

### Goals

- Make DSA concepts stick through narrative and visual storytelling
- Zero-friction learning: open app → swipe → learn
- Track progress so learners see real improvement over time
- Enable discovery by topic, difficulty, and problem type

### Non-Goals (v0.1)

- User-generated content / creator mode
- Embedded code execution / sandboxes
- Real-time multiplayer or collaboration
- Native mobile apps

---

## 2. Functional Requirements

### Feed
- [ ] Vertical scroll-snap feed (one story per viewport, `scroll-snap-type: y mandatory`)
- [ ] Each card: thumbnail, title, difficulty badge, duration, tag chips
- [ ] Infinite scroll — load next 10 as user nears bottom
- [ ] Feed preserves scroll position on back navigation
- [ ] Empty state shown when no stories match filters

### Story Player
- [ ] Full-screen video playback on card tap/click
- [ ] HTML5 `<video>` with custom overlay controls: play/pause, scrub, mute, volume
- [ ] Progress bar showing watch position
- [ ] Overlay: problem title, difficulty, tags, Leetcode problem link
- [ ] Auto-advance to next story on completion (3-second countdown with cancel)
- [ ] Keyboard shortcuts: Space=play/pause, →=next, ←=prev, M=mute

### Auth
- [ ] Email + password sign up and sign in via Supabase Auth
- [ ] Persistent JWT session with auto-refresh
- [ ] Sign out
- [ ] Protected routes redirect to `/login` when unauthenticated

### User Progress
- [ ] Story marked "watched" when user reaches 80% of video duration
- [ ] Watch history displayed on `/profile`
- [ ] Completion % per topic tag shown on profile
- [ ] Bookmarking: save stories to personal watchlist

### Discovery
- [ ] `/explore` page with filter + search
- [ ] Filter by tag (Arrays, Trees, Graphs, DP, Sorting, etc.)
- [ ] Filter by difficulty (Easy, Medium, Hard)
- [ ] Full-text search on story title and problem name
- [ ] Filters are combinable and URL-param-persisted (shareable)

### Admin
- [ ] `/admin` route (requires `admin` role via Supabase JWT claim)
- [ ] Create story: title, description, video upload, difficulty, Leetcode URL, tags
- [ ] Edit and delete stories
- [ ] Story status: Draft | Published

---

## 3. Non-Functional Requirements

- [ ] Initial feed load < 1.5s on 3G (Lighthouse network throttle)
- [ ] Lighthouse Performance ≥ 85, Accessibility ≥ 90
- [ ] Mobile-first: feed and player fully usable on 375px viewport
- [ ] No Supabase credentials exposed client-side
- [ ] All Supabase tables have RLS policies — no public write access
- [ ] Admin routes return HTTP 403 for non-admin users

---

## 4. Data Model

### `stories`
| Column | Type | Notes |
|---|---|---|
| `id` | uuid PK | auto |
| `title` | text | e.g. "Two Sum: The HashMap Trick" |
| `description` | text | 1–2 sentence teaser |
| `video_url` | text | Supabase Storage URL |
| `thumbnail_url` | text | Supabase Storage URL |
| `difficulty` | enum(`easy`,`medium`,`hard`) | |
| `leetcode_url` | text | nullable |
| `duration_seconds` | int | |
| `status` | enum(`draft`,`published`) | default `draft` |
| `created_at` | timestamptz | |
| `updated_at` | timestamptz | |

### `tags`
| Column | Type | Notes |
|---|---|---|
| `id` | uuid PK | |
| `name` | text UNIQUE | e.g. `arrays`, `dynamic-programming` |
| `color` | text | hex for badge |

### `story_tags`
| Column | Type | Notes |
|---|---|---|
| `story_id` | uuid FK | |
| `tag_id` | uuid FK | |

### `user_progress`
| Column | Type | Notes |
|---|---|---|
| `id` | uuid PK | |
| `user_id` | uuid FK → auth.users | |
| `story_id` | uuid FK → stories | |
| `watched` | bool | default false |
| `watch_percent` | int | 0–100 |
| `bookmarked` | bool | default false |
| `last_watched_at` | timestamptz | |

---

## 5. API / Interface Design

```typescript
// src/lib/stories.ts
getStories(opts?: { tag?: string; difficulty?: string; search?: string; page?: number }): Promise<Story[]>
getStoryById(id: string): Promise<Story | null>
getStoriesByTag(tagName: string): Promise<Story[]>

// src/lib/progress.ts
getUserProgress(userId: string, storyId: string): Promise<UserProgress | null>
markStoryWatched(userId: string, storyId: string, percent: number): Promise<void>
toggleBookmark(userId: string, storyId: string): Promise<boolean>
getUserWatchHistory(userId: string): Promise<UserProgress[]>
```

**Admin API Routes:**
```
POST   /api/stories              — create story (admin only)
PATCH  /api/stories/[id]         — update story (admin only)
DELETE /api/stories/[id]         — delete story (admin only)
POST   /api/stories/[id]/publish — publish draft (admin only)
```

---

## 6. Test Plan

### Unit Tests (Vitest + RTL)
- `StoryCard`: renders title, badge, tags; handles missing thumbnail
- `StoryPlayer`: play/pause toggle, progress bar updates, mute toggle
- `StoryFeed`: renders list, empty state, loads more on scroll
- `useStoryFeed`: fetches, paginates, tracks current index
- `useAuth`: returns user, handles sign-out, loading state
- Validation functions: required fields, URL format, tag count

### Integration Tests (Vitest + Supabase dev)
- `getStories()` returns only published stories
- `getStoryById()` returns null for non-existent ID
- `markStoryWatched()` upserts `user_progress`
- `toggleBookmark()` correctly flips state
- Admin API route: 403 for non-admin, 201 for valid admin request

### E2E Tests (Playwright)
- Feed loads and shows at least one story card
- Clicking a card opens story player
- Video plays, progress bar advances
- Reaching 80% marks story watched (verify on profile)
- Tag filter: click "Arrays" → only Arrays stories shown
- Search: type "two sum" → correct story appears
- Sign up → session persists on page reload

### Edge Cases
- Empty feed (no published stories)
- Video URL returns 404
- Supabase unavailable (network error)
- User watches same story multiple times (upsert, no duplicates)
- Story with no tags assigned
- Very long story title (overflow/truncation)

---

## 7. Open Questions

- **Video hosting:** Supabase Storage vs. Cloudflare R2 or Mux for CDN performance at scale?
- **Video generation:** Manual uploads first → later explore Remotion for programmatic animation?
- **Rate limiting:** Simple Next.js middleware vs. Upstash Redis?
- **PWA:** Should v0.1 be installable as a PWA from day one?
- **Analytics:** Which events to track? (story started, completed, searched, filtered)
