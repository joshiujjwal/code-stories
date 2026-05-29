# CodeStories — Task Breakdown

## How to Use This File

Workflow per task:
1. **Write tests FIRST** (red phase — tests must fail before you write implementation)
2. **Implement until tests pass** (green phase)
3. **Review diff manually** — read every changed line before committing
4. **Commit with a descriptive message** referencing the TODO item
5. **Update CLAUDE.md / AGENTS.md** if you discovered a non-obvious convention
6. **Check off the item** and move to the next

> 🔴 Never skip the red phase. If you can't write a failing test first, write the test file as a stub with `test.todo(...)` entries before coding.

---

## Phase 0: Foundation ⬜

> Gate: CI is green, smoke test passes, linter has zero warnings.

- [ ] Initialize Next.js 14 project with TypeScript (`npx create-next-app@latest`)
- [ ] Configure pnpm as package manager (preferred for monorepo-readiness)
- [ ] Set up Tailwind CSS + shadcn/ui base components
- [ ] Install and configure Vitest + React Testing Library
- [ ] Install and configure Playwright for E2E
- [ ] Write first smoke test: `renders root page without crashing`
- [ ] Set up ESLint + Prettier with project-specific rules
- [ ] Create `.env.example` with all required environment variable keys (no secrets)
- [ ] Set up GitHub Actions CI: lint → unit tests → build on every PR
- [ ] Set up Prisma + PostgreSQL schema (local Docker for dev)
- [ ] Review and tailor all AI config files (CLAUDE.md, AGENTS.md, copilot-instructions.md)

---

## Phase 1: Story Viewer — Core Reading Experience ⬜

> Gate: A user can open a story, read through panels with illustrated narrative, and reach the embedded problem.

- [ ] Define `Story` and `Panel` TypeScript types in `src/lib/types.ts`
- [ ] Write failing tests for `StoryPanel` component (renders title, image slot, narrative text)
- [ ] Implement `StoryPanel` component — comic-panel layout with caption area
- [ ] Write failing tests for `StoryReader` component (sequences panels, keyboard/click nav)
- [ ] Implement `StoryReader` — panel-by-panel progression with prev/next navigation
- [ ] Write failing test for `StoryIndex` page (lists all available story arcs by category)
- [ ] Implement `StoryIndex` page with search and category filter
- [ ] Author first complete story arc: **"The Array Kingdom"** (intro to arrays + two-pointer)
  - [ ] Write MDX file `src/stories/array-kingdom.mdx` with 6–8 panels
  - [ ] Add placeholder illustrations (SVG or placeholder images)
  - [ ] Embed the problem at the end of the story
- [ ] Manual testing: read through the full Array Kingdom story
- [ ] Playwright E2E: user navigates from index → story → final problem panel

---

## Phase 2: Code Sandbox & Problem Solving ⬜

> Gate: A user can read the embedded problem, write JS/Python code, run it, and see pass/fail feedback.

- [ ] Define `Problem`, `TestCase`, and `Submission` types
- [ ] Write failing tests for `CodeEditor` component (Monaco integration, onChange, language toggle)
- [ ] Implement `CodeEditor` wrapper around Monaco Editor
- [ ] Write failing tests for in-browser JS code runner (`src/lib/sandbox.ts`)
  - [ ] Handles syntax errors gracefully
  - [ ] Times out runaway loops (5s limit)
  - [ ] Returns structured `{ passed, output, error }` per test case
- [ ] Implement JS sandbox using Web Workers (`new Worker(...)` + structured clone)
- [ ] Write failing tests for `TestCaseRunner` component (displays per-test-case pass/fail)
- [ ] Implement `TestCaseRunner` with diff output for failed assertions
- [ ] Write problem definition for Array Kingdom problem with 5 test cases
- [ ] Wire `StoryReader` end panel → `CodeEditor` + `TestCaseRunner`
- [ ] E2E test: user writes a correct solution, submits, sees all tests pass
- [ ] E2E test: user writes a wrong solution, sees which test case failed and why

---

## Phase 3: User Progress & Story Library ⬜

> Gate: Progress persists across sessions. Users can see which stories they've completed.

- [ ] Design Prisma schema: `User`, `StoryProgress`, `Submission` models
- [ ] Write failing integration tests for progress API routes (`/api/progress`, `/api/submit`)
- [ ] Implement `POST /api/submit` — saves submission, evaluates server-side, returns result
- [ ] Implement `GET /api/progress` — returns user's story completion statuses
- [ ] Set up NextAuth.js (GitHub OAuth + email magic link)
- [ ] Write failing test for `useProgress` hook
- [ ] Implement `useProgress` hook (optimistic update + SWR)
- [ ] `StoryIndex` page shows completion badges (✅ / 🔒 locked / ⬜ unstarted)
- [ ] Unlock gating: Story 2 unlocks only after Story 1 is solved (per arc)
- [ ] User profile page: streak, solved count, favourite category

---

## Phase 4: Story Content Expansion ⬜

> Gate: At least 5 complete story arcs covering foundational DSA topics.

- [ ] **"The Linked List Express"** — linked lists, reversal, cycle detection
- [ ] **"Forest of Recursion"** — recursion, base cases, call stack visualized
- [ ] **"The Binary Search Detective"** — binary search, sorted array invariants
- [ ] **"Hash Map Heist"** — hash maps, collision, frequency counting
- [ ] **"Stack & Queue: The Time Machine"** — stack, queue, BFS vs DFS intro
- [ ] Each story: 6–8 MDX panels, 1 embedded problem, 5+ test cases
- [ ] Commission or generate panel illustrations (consistent art style guide in docs/)

---

## Phase 5: Polish & Harden ⬜

> Gate: Lighthouse ≥ 90, no accessibility violations, test coverage ≥ 80%.

- [ ] Audit and fix all accessibility issues (axe-core in tests)
- [ ] Add keyboard-first navigation for story panels
- [ ] Add dark mode (Tailwind dark: classes + system preference detection)
- [ ] Implement error boundaries on `StoryReader` and `CodeEditor`
- [ ] Add rate limiting to `/api/submit` (to prevent abuse)
- [ ] Load testing: simulate 100 concurrent sandbox evaluations
- [ ] Optimize image delivery (next/image with blur placeholders)
- [ ] Add OG image generation per story (for social sharing)
- [ ] SEO: per-story metadata, sitemap, robots.txt

---

## Phase 6: Ship ⬜

> Gate: Production deploy is live, monitoring is in place, README has accurate setup docs.

- [ ] Deploy to Vercel (preview + production environments)
- [ ] Set up Railway or Supabase for production PostgreSQL
- [ ] Configure environment secrets in Vercel dashboard
- [ ] Set up error tracking (Sentry)
- [ ] Set up uptime monitoring
- [ ] Write accurate `Getting Started` docs in README
- [ ] Announce on HN / dev.to / X with story screenshot

---

## Parking Lot 🅿️

> Ideas not yet scheduled — drop things here during sprints.

- Python code runner (Pyodide in Web Worker)
- AI-generated story hints when user is stuck
- Community problem submissions alongside stories
- Spaced repetition review system for solved problems
- Mobile-first comic layout with swipe gestures
- Leaderboard / competitive mode per story arc

---

## Lessons Learned 📝

> Update this section whenever you discover a non-obvious truth about this codebase or workflow.

_Nothing here yet — fill in as you build._
