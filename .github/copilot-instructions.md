# GitHub Copilot Instructions — Code Stories

## Stack

- **Framework:** Next.js 14 with App Router
- **Language:** TypeScript (strict mode)
- **Styling:** Tailwind CSS (mobile-first)
- **Database / Auth:** Supabase (Postgres + Auth + Storage + RLS)
- **State:** Zustand (client-side feed and player state)
- **Animation:** Framer Motion
- **Testing:** Vitest + React Testing Library (unit/integration), Playwright (e2e)

---

## Coding Conventions

### Components
- React Server Components by default — `"use client"` only for hooks, events, or browser APIs
- Props typed with interface: `interface StoryCardProps { ... }`
- Always handle loading, error, and empty states
- Prefer small, focused components (< 150 lines)

### Hooks
- `src/hooks/` — one hook per file, named `use[Feature].ts`
- Return typed objects: `{ data, isLoading, error }` not arrays
- Handle all async states explicitly

### API Routes
- `app/api/.../route.ts` — Next.js Route Handlers
- Validate request bodies with `zod`
- Return `NextResponse.json({ error }, { status })` for all error cases
- Admin check: `session.user.app_metadata.role === 'admin'`

### Supabase
- Import client from `@/lib/supabase` — never instantiate directly in components
- Always destructure and check `error` before using `data`
- RLS must be enabled on all tables — no exceptions

---

## Testing Conventions

- Test files: `tests/unit/components/StoryCard.test.tsx` mirrors `src/components/`
- Use `describe` blocks for grouping
- `data-testid` attributes for Playwright selectors
- Mock Supabase in unit tests with `vi.mock('@/lib/supabase')`
- Test behavior, not implementation details

---

## Boundaries

- **Do not** refactor working code unless explicitly asked
- **Do not** remove or skip existing tests
- **Do not** use `any` type — use `unknown` and narrow
- **Do not** add `// @ts-ignore` without an explanation comment
- **Do not** use `useEffect` for initial data loading (use server components)
- **Do not** expose `SUPABASE_SERVICE_ROLE_KEY` in any `NEXT_PUBLIC_` env var
- **Do not** write CSS outside Tailwind classes (no `style={{}}` except CSS variables)
- **Do not** create new pages without a corresponding test file
