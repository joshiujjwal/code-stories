# CLAUDE.md — Code Stories

Context for AI coding agents. Keep under 200 lines. Append to Lessons Learned when you discover something non-obvious.

---

## Project in One Sentence

Short-form vertical video feed (TikTok/Reels-style) for learning Leetcode and DSA — each story is a 60–120s narrated visual walkthrough of an algorithm problem.

---

## Commands

```bash
# Development
npm run dev           # Start Next.js dev server at localhost:3000

# Testing
npm test              # Vitest unit + integration (watch mode)
npm run test:run      # Vitest single run (CI)
npm run test:e2e      # Playwright e2e (requires dev server on :3000)
npm run test:e2e:ui   # Playwright with interactive UI

# Quality
npm run lint          # ESLint
npm run format        # Prettier --write
npm run typecheck     # tsc --noEmit

# Build
npm run build         # Next.js production build
npm run start         # Start production server locally
```

> **Before writing any code, run `npm test` to confirm the baseline is green.**

---

## Directory Map

```
src/
  app/                # Next.js App Router
    (feed)/           # Route group: home feed
    story/[id]/       # Story player page
    explore/          # Discovery + search + filters
    profile/          # Watch history, bookmarks (auth required)
    admin/            # Story CRUD (admin role required)
    login/ signup/    # Auth pages
    api/              # Route handlers (stories CRUD, auth callbacks)
  components/
    feed/             # StoryCard, StoryFeed, ScrollSnapContainer
    player/           # StoryPlayer, StoryOverlay, ProgressBar, Controls
    ui/               # Button, Badge, TagChip, Skeleton, DifficultyBadge
    layout/           # Header, BottomNav, AuthGuard
  lib/
    supabase.ts       # Client singleton — browser + server variants
    stories.ts        # getStories, getStoryById, getStoriesByTag
    progress.ts       # markStoryWatched, toggleBookmark, getUserWatchHistory
    auth.ts           # signIn, signUp, signOut helpers
  hooks/
    useStoryFeed.ts   # Fetches + paginates stories, tracks current index
    useAuth.ts        # Returns { user, loading, signOut }
    useProgress.ts    # Per-story watch state and bookmark toggle
  types/
    index.ts          # Story, Problem, Tag, UserProgress, Difficulty
tests/
  unit/               # mirrors src/components and src/hooks
  integration/        # real dev Supabase, run with credentials in env
  e2e/                # Playwright, target localhost:3000
```

---

## Key Conventions

### Supabase
- Use `createServerClient` (from `@supabase/ssr`) in Server Components and API routes
- Use `createBrowserClient` only in Client Components / hooks
- `SUPABASE_SERVICE_ROLE_KEY` — server only, never in `NEXT_PUBLIC_` env vars
- All tables must have RLS enabled — no exceptions

### TypeScript
- Strict mode on — no `any`, no `@ts-ignore` without explanation comment
- Generate DB types: `npx supabase gen types typescript --local > src/types/supabase.ts`
- Use `zod` for runtime validation on all API route inputs

### Components
- Default to React Server Components; add `"use client"` only for DOM APIs or hooks
- `StoryPlayer` and `StoryFeed` are client components (video API, scroll events)
- No `useEffect` for initial data loading — use server component async functions

### Styling
- Tailwind only — no inline `style={{}}` except CSS variables for dynamic values
- Mobile-first — base styles for mobile, `md:` / `lg:` for larger screens
- Scroll snap: `scroll-snap-type: y mandatory` on feed container, `scroll-snap-align: start` on cards

---

## Non-Obvious Gotchas

- **Supabase SSR:** Use `@supabase/ssr` package, NOT deprecated `@supabase/auth-helpers-nextjs`
- **Video autoplay:** Browsers block autoplay with audio. Default: muted autoplay. User unmutes manually.
- **Scroll snap on iOS Safari:** Parent must use `overflow-y: scroll` (not `auto`) for snap to work
- **Supabase admin role:** Check `auth.jwt() ->> 'role' = 'admin'` — set via custom JWT claim, not a column
- **`generateMetadata` async params:** In Next.js 15, `params` is a Promise — `await params` before destructuring

---

## Workflow

1. **Read TODO.md** — find the current phase, pick the next unchecked task
2. **Run `npm test`** — confirm baseline is green before touching anything
3. **Write a failing test** (red) — commit with `test: ...` prefix
4. **Implement until green** — commit with `feat:` or `fix:`
5. **Run `npm run lint && npm run typecheck`** — fix before committing
6. **Update this file** with anything non-obvious you discovered
7. **Check off the task** in TODO.md

---

## Lessons Learned

_Append: `YYYY-MM-DD — what you discovered`_

- (none yet)
