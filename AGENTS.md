# AGENTS.md — Code Stories

OpenAI Codex / agentic agent instructions. Read this before writing any code.

---

## Setup

```bash
npm install
cp .env.example .env.local    # fill in Supabase credentials
npm run dev                   # verify dev server starts at localhost:3000
npm test                      # verify baseline tests pass
```

Required env vars (see `.env.example`):
- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY` (server-only — never exposed to browser)

---

## Testing

**Write tests before implementing.** Red → Green → Refactor.

```bash
npm test              # watch mode — keep running while developing
npm run test:run      # single run — use for CI verification
npm run test:e2e      # Playwright e2e (requires dev server on localhost:3000)
```

- Unit tests: `tests/unit/` — Vitest + React Testing Library
- Integration tests: `tests/integration/` — require Supabase dev credentials
- E2E tests: `tests/e2e/` — Playwright targeting `localhost:3000`
- Every PR must paste test output confirming new/modified tests are green

---

## Code Style

**TypeScript**
- Strict mode: no `any`, no implicit `any`
- Prefer `interface` for object shapes, `type` for unions/aliases
- Validate all API inputs with `zod` before touching the database

**React / Next.js**
- App Router only — no `pages/` directory
- Server Components by default; `"use client"` only when DOM APIs or hooks are needed
- Data fetching: `async` server components and `lib/` functions — not `useEffect`

**Tailwind CSS**
- Mobile-first: base = mobile, `md:` / `lg:` = larger
- No inline `style={{}}` except for dynamic CSS custom properties
- Class order: layout → spacing → typography → color → state

**Naming**
- Components: PascalCase (`StoryCard.tsx`)
- Hooks: `useCamelCase` (`useStoryFeed.ts`)
- Lib functions: camelCase (`getStoryById`)
- Constants: SCREAMING_SNAKE_CASE (`MAX_FEED_PAGE_SIZE`)
- Types/interfaces: PascalCase (`Story`, `UserProgress`)

---

## PR Requirements

Every PR must include:
1. **What changed** — 2–3 sentence description
2. **Tests added/modified** — list files and what they cover
3. **Test output** — paste of `npm run test:run` showing green
4. **Manual testing evidence** — what you tested manually or Playwright test name
5. **Screenshot** — for any UI change

**Rules:**
- One concern per PR — no mixed refactors
- Do not remove existing tests unless the tested feature is deleted
- `npm run lint && npm run typecheck && npm run build` must all pass before review

---

## Security Rules

- Never commit `.env.local` or real credentials
- `SUPABASE_SERVICE_ROLE_KEY` only in server-side code (API routes, server components)
- All admin API routes must verify `admin` role via Supabase JWT claims
- All Supabase tables must have RLS enabled
- Validate all user inputs with `zod`

---

## Out of Scope

- Do not refactor working code unless asked
- Do not upgrade dependencies without instruction
- Do not add analytics/tracking without explicit instruction
- Do not run Supabase migrations directly — write SQL files in `supabase/migrations/`, human runs them
