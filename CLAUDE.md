# CLAUDE.md — CodeStories

Context file for AI coding assistants. Keep this under 200 lines.
Update it whenever you discover something non-obvious.

---

## Project Summary

**CodeStories** — Learn DSA through illustrated comic story arcs.
Each story arc teaches one concept (arrays, linked lists, trees, etc.) and ends with an embedded coding problem. Solving the problem unlocks the next arc.

Tech: **Next.js 14 (App Router) · TypeScript · Tailwind CSS · Prisma · PostgreSQL · Vitest · Playwright**

---

## Commands

```bash
# Install dependencies
pnpm install

# Dev server (http://localhost:3000)
pnpm dev

# Run all unit tests (Vitest)
pnpm test

# Run tests in watch mode
pnpm test:watch

# Run E2E tests (Playwright — requires dev server running)
pnpm test:e2e

# Type check only (no emit)
pnpm typecheck

# Lint
pnpm lint

# Format
pnpm format

# Prisma: generate client after schema changes
pnpm db:generate

# Prisma: run migrations (dev)
pnpm db:migrate

# Prisma: open Prisma Studio
pnpm db:studio
```

> ⚠️ Commands are placeholders until `package.json` is bootstrapped. Update this section immediately after running `create-next-app`.

---

## Directory Map

```
src/
  components/     UI components — StoryPanel, StoryReader, CodeEditor, TestCaseRunner
  stories/        MDX content files — one per story arc (e.g. array-kingdom.mdx)
  problems/       Problem definitions (TS objects) + test cases
  lib/
    types.ts      Shared TypeScript types (Story, Panel, Problem, TestCase, ...)
    sandbox.ts    Web Worker-based code execution sandbox
    db.ts         Prisma client singleton
    auth.ts       NextAuth config
  hooks/          Custom React hooks (useProgress, useStory, useCodeRunner)
  styles/         Global CSS, comic-panel layout classes
tests/
  unit/           Vitest unit tests — mirror src/ structure
  integration/    API route tests with test DB
  e2e/            Playwright journeys
docs/
  spec.md         Feature specification — read this before building anything new
  adr/            Architecture Decision Records
```

---

## Workflow

Before writing any code:
1. `pnpm test` — confirm existing tests pass (never break existing green tests)
2. Read `TODO.md` — find the next unchecked item in the current phase
3. Read `docs/spec.md` section relevant to what you're building
4. **Write failing tests first** (red phase) — confirm they fail for the right reason
5. Implement until tests pass (green phase)
6. Manually review the diff — read every changed line
7. Commit with a message like: `feat(story-reader): implement panel navigation`
8. If you learned something non-obvious → update `CLAUDE.md` or `AGENTS.md`

---

## Non-Obvious Conventions

### Story Content (MDX)
- Each `.mdx` file in `src/stories/` must export a `metadata` object and a default array of `Panel` objects
- Panel illustrations live in `public/illustrations/<story-id>/panel-<n>.svg`
- Never inline base64 images — always reference `/illustrations/...` paths

### Code Sandbox (`src/lib/sandbox.ts`)
- Code runs in a **Web Worker** with `importScripts` blocked — no external network
- The worker communicates via `postMessage` with a structured `SandboxRequest` / `SandboxResponse` type
- Timeout is enforced by the host thread (`setTimeout` + `worker.terminate()`)
- Never run user code in the main thread or in a server-side API route

### Database
- Prisma client is a singleton exported from `src/lib/db.ts` — do not `new PrismaClient()` elsewhere
- Migrations go in `prisma/migrations/` — never edit generated migration files
- Use `prisma.$transaction([...])` for any multi-table writes (e.g., submit + unlock)

### Testing
- Unit tests in `tests/unit/` mirror `src/` structure: `src/components/StoryPanel.tsx` → `tests/unit/components/StoryPanel.test.tsx`
- Integration tests spin up a test DB via `DATABASE_URL_TEST` env var
- E2E tests require the dev server to be running (`pnpm dev`) — or use `webServer` config in `playwright.config.ts`
- Never use `screen.getByTestId` unless no semantic query exists — prefer `getByRole`, `getByLabelText`

### TypeScript
- Strict mode is on — no `any`, no `@ts-ignore` without a comment explaining why
- All API responses have explicit return types; use `z.infer<typeof Schema>` for validated inputs (Zod)

---

## Environment Variables

See `.env.example` for the full list. Key ones:

| Variable | Purpose |
|----------|---------|
| `DATABASE_URL` | PostgreSQL connection string |
| `DATABASE_URL_TEST` | Separate DB for integration tests |
| `NEXTAUTH_SECRET` | NextAuth signing secret |
| `GITHUB_CLIENT_ID` | GitHub OAuth app client ID |
| `GITHUB_CLIENT_SECRET` | GitHub OAuth app secret |

---

## What NOT to Do

- Do not run user code outside the Web Worker sandbox
- Do not store full solution code in plain text client-side (only after submission)
- Do not refactor unrelated code while implementing a feature
- Do not remove or skip existing passing tests
- Do not commit `.env.local` or any file containing secrets
