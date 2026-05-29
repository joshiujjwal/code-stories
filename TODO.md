# code-stories — Task Breakdown

## How to Use This File

One task at a time, in phase order. Evidence gates block phase transitions.

**Workflow per task:**
1. Write failing tests FIRST (red phase) — commit them
2. Implement until tests pass (green phase) — commit
3. Review the diff manually — no surprises
4. Commit with a descriptive message
5. If you learned something non-obvious, append it to CLAUDE.md → Lessons Learned

**Phase gate rule:** Do NOT start the next phase until ALL items in the current phase are checked AND tests pass CI.

---

## Phase 0: Foundation ⬜

- [ ] Init Next.js 14 project with TypeScript: `npx create-next-app@latest . --typescript --tailwind --app --src-dir`
- [ ] Configure ESLint + Prettier
- [ ] Set up Vitest + React Testing Library
- [ ] Set up Playwright: `npx playwright install`
- [ ] Write first smoke test: app renders without crashing (red → green)
- [ ] Configure path aliases: `@/` → `src/` in `tsconfig.json`
- [ ] Add `.env.example` with all required keys (no values committed)
- [ ] Set up GitHub Actions CI: lint + unit tests + build on push / PRs
- [ ] Configure Supabase dev project, get credentials into `.env.local`
- [ ] Review AI config files (CLAUDE.md, AGENTS.md, copilot-instructions.md)

**Evidence gate:** CI green. Smoke test passes. `npm run build` succeeds.

---

## Phase 1: Data Model + API Foundation ⬜

- [ ] Define TypeScript types: `Story`, `Problem`, `Tag`, `UserProgress` in `src/types/`
- [ ] Write failing integration tests for Supabase schema assumptions
- [ ] Create Supabase tables: `stories`, `problems`, `tags`, `story_tags`, `user_progress`
- [ ] Enable RLS on all tables; write policies
- [ ] Write Supabase client singleton in `src/lib/supabase.ts` (browser + server variants)
- [ ] Write failing tests for `getStories()`, `getStoryById()`, `getStoriesByTag()`
- [ ] Implement data-fetching functions in `src/lib/stories.ts`
- [ ] Write failing tests for `getUserProgress()`, `markStoryWatched()`, `toggleBookmark()`
- [ ] Implement progress tracking in `src/lib/progress.ts`
- [ ] Seed database with 3 sample stories (Two Sum, Binary Search, FizzBuzz)

**Evidence gate:** All integration tests pass against dev Supabase. Can query seeded stories.

---

## Phase 2: Video Feed (Core UI) ⬜

- [ ] Write failing tests for `StoryCard` (renders title, difficulty badge, thumbnail, tags)
- [ ] Implement `StoryCard` component with Tailwind
- [ ] Write failing tests for `StoryFeed` (renders list, handles empty state, scroll snap)
- [ ] Implement `StoryFeed` with vertical scroll-snap (CSS `scroll-snap-type: y mandatory`)
- [ ] Write failing tests for `useStoryFeed` hook (fetches, paginates, tracks current index)
- [ ] Implement `useStoryFeed` with Zustand
- [ ] Build `/` (home feed) page — server-fetches initial stories, passes to client feed
- [ ] Write failing tests for keyboard navigation (arrow keys, scroll)
- [ ] Implement keyboard + touch/swipe navigation
- [ ] Add loading skeletons for feed cards

**Evidence gate:** Feed renders 3 seeded stories. Scroll snap works on mobile viewport in Playwright.

---

## Phase 3: Story Player ⬜

- [ ] Write failing tests for `StoryPlayer` (renders video, play/pause, progress bar updates)
- [ ] Implement `StoryPlayer` with HTML5 `<video>` and custom controls
- [ ] Write failing tests for `StoryOverlay` (shows title, difficulty, tags, Leetcode link)
- [ ] Implement `StoryOverlay` positioned over video
- [ ] Build `/story/[id]` dynamic route
- [ ] Write failing test for auto-advance on video completion (fires after 3s countdown)
- [ ] Implement auto-advance in `useStoryFeed`
- [ ] Write failing test for progress persistence (80% watched → stored in Supabase)
- [ ] Implement `markStoryWatched` call on video progress update
- [ ] Add mute/unmute toggle; default to muted autoplay (browser policy)

**Evidence gate:** Full playback works. Progress saves. Auto-advance fires. Playwright e2e passes.

---

## Phase 4: Auth + User Profiles ⬜

- [ ] Write failing tests for Supabase Auth flows (sign up, sign in, sign out)
- [ ] Implement auth helpers in `src/lib/auth.ts`
- [ ] Build `/login` and `/signup` pages
- [ ] Write failing tests for `useAuth` hook (returns user, loading, sign-out)
- [ ] Implement `useAuth` with Supabase session listener
- [ ] Add middleware auth guard for protected routes (`/profile`, `/admin`)
- [ ] Write failing tests for `/profile` page (shows watch history, completion %)
- [ ] Implement `/profile` page
- [ ] Write failing tests for bookmark toggle
- [ ] Implement bookmark feature (toggle, list in profile)

**Evidence gate:** Sign up → watch story → profile shows watched story. All auth tests green.

---

## Phase 5: Content Management (Admin) ⬜

- [ ] Write failing tests for `POST /api/stories` (creates story, returns 403 for non-admin)
- [ ] Implement admin API route with Supabase role check
- [ ] Build `/admin/stories/new` form (title, description, video upload, difficulty, tags, Leetcode URL)
- [ ] Write failing tests for story input validation (required fields, URL format, tag limits)
- [ ] Implement zod validation shared between client and API
- [ ] Write failing tests for video upload to Supabase Storage
- [ ] Implement video upload (client-side → signed URL → store reference)
- [ ] Write failing tests for story edit and delete
- [ ] Implement edit/delete in admin UI and API routes
- [ ] Add admin dashboard listing all stories with status badges

**Evidence gate:** Admin can CRUD stories. Videos upload and play. Non-admin gets 403.

---

## Phase 6: Discovery + Search ⬜

- [ ] Write failing tests for tag filter (click tag → feed shows only matching stories)
- [ ] Implement tag filter UI (pill chips, active state, URL param persistence)
- [ ] Write failing tests for difficulty filter (Easy / Medium / Hard)
- [ ] Implement difficulty filter
- [ ] Write failing tests for full-text search (title + problem name)
- [ ] Implement search using Supabase `ilike` or `pg_trgm`
- [ ] Write failing tests for `/explore` page (filters combinable, URL param synced)
- [ ] Implement `/explore` page
- [ ] Add "Related Stories" section at end of player (same tag or difficulty)

**Evidence gate:** Search and combined filters work. Playwright e2e test covers full filter flow.

---

## Phase 7: Polish & Harden ⬜

- [ ] Add error boundaries to feed and player routes
- [ ] Write failing tests for error states (network fail, video 404, empty feed)
- [ ] Implement graceful error UI for all edge cases
- [ ] Audit Lighthouse: target Performance ≥ 85, Accessibility ≥ 90
- [ ] Add Open Graph meta tags for story pages (shareable link preview)
- [ ] Add `generateMetadata` in dynamic routes for SEO
- [ ] Write Playwright performance test: feed initial load < 1.5s on 3G throttle
- [ ] Implement lazy loading + intersection observer for off-screen video elements
- [ ] Add rate limiting to admin API routes
- [ ] Security audit: verify RLS policies on all tables, no public write access

**Evidence gate:** Lighthouse ≥ 85/90. All error paths tested. Security review signed off.

---

## Phase 8: Ship ⬜

- [ ] Configure Vercel project (link repo, set env vars)
- [ ] Set up preview deployments for every PR
- [ ] Deploy to production URL
- [ ] Manual smoke test on production: sign up → watch story → check profile
- [ ] Write `docs/launch.md` post-launch checklist
- [ ] Tag `v0.1.0` release in GitHub

**Evidence gate:** Production URL live. Smoke test passes. v0.1.0 tagged.

---

## Parking Lot ��️

- Remotion pipeline: programmatically generate animated algorithm walkthroughs
- "Solve It" mode: embedded code sandbox after watching a story
- Community comments per story
- Learning paths: curated story sequences (Arrays → Trees → Graphs → DP)
- Daily streak tracking (watch N stories/day)
- Story creator mode (user-submitted stories)
- Spaced repetition reminders
- React Native mobile app with shared lib logic
- Story transcript + closed captions (accessibility)

---

## Lessons Learned 📝

_Append: `YYYY-MM-DD — what you discovered`_

- (none yet)
