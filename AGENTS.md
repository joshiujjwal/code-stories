# AGENTS.md — CodeStories

Setup and workflow instructions for autonomous coding agents (OpenAI Codex, GitHub Copilot Workspace, etc.).

---

## Setup

```bash
# 1. Install dependencies
pnpm install

# 2. Set up environment
cp .env.example .env.local
# Fill in DATABASE_URL, NEXTAUTH_SECRET, GITHUB_CLIENT_ID, GITHUB_CLIENT_SECRET

# 3. Run database migrations
pnpm db:migrate

# 4. Verify setup — these must all pass before doing any work
pnpm typecheck
pnpm lint
pnpm test
```

If any of the verification steps fail, **stop and fix them before proceeding**. Never start a task on a broken baseline.

---

## Tech Stack Quick Reference

| Layer | Tool | Notes |
|-------|------|-------|
| Framework | Next.js 14 App Router | Use server components by default; opt into `"use client"` only when needed |
| Language | TypeScript (strict) | No `any`. Use `unknown` and narrow with guards or Zod |
| Styling | Tailwind CSS + shadcn/ui | Utility-first; no custom CSS files unless doing comic-panel layout |
| Content | MDX | Story panels live in `src/stories/*.mdx` |
| DB ORM | Prisma | Schema at `prisma/schema.prisma` |
| Auth | NextAuth.js v5 | Config in `src/lib/auth.ts` |
| State | SWR + React hooks | No Redux; server state via SWR, UI state via `useState` / `useReducer` |
| Testing | Vitest + RTL (unit), Playwright (E2E) | See Testing section below |

---

## Code Style

### TypeScript

```typescript
// ✅ Explicit return types on all exported functions
export function getStory(id: string): Story | null { ... }

// ✅ Zod for runtime validation of external data
const StorySchema = z.object({ id: z.string(), title: z.string() });
type Story = z.infer<typeof StorySchema>;

// ❌ Never use `any`
// ❌ Never use non-null assertion (!) without a comment
```

### React / Next.js

```tsx
// ✅ Server components by default
// app/stories/page.tsx — no "use client" directive needed
export default async function StoriesPage() { ... }

// ✅ Client component only when you need browser APIs or event handlers
"use client";
export function StoryReader({ panels }: { panels: Panel[] }) { ... }

// ✅ Named exports for components, default exports for pages
export function StoryPanel({ panel }: { panel: Panel }) { ... }
```

### Naming

| Thing | Convention | Example |
|-------|-----------|---------|
| Components | PascalCase | `StoryPanel.tsx` |
| Hooks | camelCase with `use` prefix | `useProgress.ts` |
| Utilities | camelCase | `formatDuration.ts` |
| Types/interfaces | PascalCase | `type StoryProgress` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_EXECUTION_TIME_MS` |
| Route handlers | `route.ts` inside app dir | `app/api/submit/route.ts` |

---

## Testing Instructions

### Red/Green TDD — Non-Negotiable

1. **Write the test first.** It must fail (`red`).
2. Confirm it fails for the right reason (not a syntax error).
3. Write the minimum implementation to make it pass (`green`).
4. Refactor if needed, keeping tests green.

### Running Tests

```bash
pnpm test              # Run all Vitest unit + integration tests
pnpm test:watch        # Watch mode during development
pnpm test:e2e          # Playwright E2E (requires running dev server)
pnpm test -- --coverage  # With coverage report
```

### Test File Conventions

- Unit tests: `tests/unit/<mirror-of-src-path>.test.tsx`
- Integration tests: `tests/integration/<route-or-feature>.test.ts`
- E2E tests: `tests/e2e/<user-journey>.spec.ts`
- Use `describe` blocks matching the component or function name
- Use `it("does X when Y")` style — not `test("test1")`

### What to Test

| Type | Test this |
|------|-----------|
| `StoryPanel` | renders narration, image, dialogue; handles missing optional props |
| `StoryReader` | panel navigation, keyboard events, boundary conditions |
| `sandbox.ts` | correct solution passes, wrong solution fails, infinite loop → TLE, syntax error → ERROR |
| API routes | happy path, auth-required routes return 401, invalid input returns 400 |
| Progress unlock | story unlocks only after problem solved, not after reading |

---

## Pull Request Instructions

Every PR must include:

1. **A failing test** that existed before your implementation (prove the red phase happened)
2. **Evidence of green**: paste `pnpm test` output showing all tests pass
3. **For UI changes**: a screenshot or short screen recording
4. **Description**: one paragraph explaining *what* changed and *why*
5. **No unrelated changes**: diff must be minimal and focused

### PR Title Format

```
<type>(<scope>): <short description>

feat(story-reader): add keyboard navigation for panels
fix(sandbox): handle async functions that return undefined
test(code-editor): add missing edge case for empty input
docs(spec): update data model with Submission.runtime field
```

### What Agents Must NOT Do in PRs

- Do not remove or skip existing passing tests
- Do not add `eslint-disable` comments without explaining why
- Do not introduce new dependencies without updating `docs/spec.md`
- Do not refactor code outside the scope of the task
- Do not commit `.env.local`, `*.pem`, or any credential file
