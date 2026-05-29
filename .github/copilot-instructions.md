# GitHub Copilot Instructions — CodeStories

## Project Context

**CodeStories** teaches data structures & algorithms through illustrated comic narratives. Users read a story arc (6–8 panels), then solve an embedded coding problem. Solving unlocks the next arc.

**Stack**: Next.js 14 App Router · TypeScript (strict) · Tailwind CSS · shadcn/ui · MDX · Prisma · PostgreSQL · Vitest · Playwright

---

## Coding Conventions

### TypeScript
- Strict mode is ON — no `any`, no `@ts-ignore` without an explanation comment
- Prefer `unknown` over `any` for untyped input; narrow with Zod or type guards
- All exported functions have explicit return types
- Use `z.infer<typeof Schema>` for types derived from Zod schemas

### React / Next.js
- Default to **Server Components** — add `"use client"` only for interactivity or browser APIs
- Named exports for components, default exports for Next.js pages/layouts
- Co-locate component-specific hooks with the component file until they're reused
- Use `next/image` for all images — never raw `<img>` tags

### Styling
- Tailwind utility classes only — no custom CSS files except for comic-panel layout in `src/styles/`
- Use `cn()` (from `src/lib/utils.ts`) to merge conditional Tailwind classes
- Dark mode via `dark:` Tailwind classes — no JS theme toggling

### Naming
- Components: `PascalCase` (e.g., `StoryPanel`, `CodeEditor`)
- Hooks: `useCamelCase` (e.g., `useProgress`, `useCodeRunner`)
- Constants: `SCREAMING_SNAKE_CASE` (e.g., `MAX_EXECUTION_TIME_MS`)
- DB models: `PascalCase` matching Prisma schema

---

## Testing Conventions

- **Red first**: always write a failing test before implementation
- Unit tests in `tests/unit/` — mirror `src/` directory structure
- E2E tests in `tests/e2e/` using Playwright with `page.getByRole()` queries
- Prefer semantic queries: `getByRole` > `getByLabelText` > `getByText` > `getByTestId`
- Use `it("does X when Y")` format — no numbered test names
- Integration tests use a separate `DATABASE_URL_TEST` database

---

## Domain Knowledge

### Story Format
Each story is an MDX file in `src/stories/` exporting:
- `metadata`: `{ title, category, difficulty, problemId, prerequisites }`
- default export: `Panel[]` array

### Code Sandbox
User code runs in a **Web Worker** (`src/lib/sandbox.ts`). Never execute user code in the main thread or server-side. Sandbox enforces a 5-second timeout.

### Progress Unlock Logic
A story is "completed" only when the user submits a passing solution to its embedded problem. Reading the story alone does NOT unlock the next arc. This logic lives in `POST /api/submit`.

---

## Boundaries — What Copilot Should NOT Do

- Do NOT refactor code outside the file currently being edited unless explicitly asked
- Do NOT remove existing passing tests
- Do NOT add new npm dependencies without a comment explaining why
- Do NOT run user code outside the Web Worker sandbox
- Do NOT generate placeholder `any` types — use `unknown` and add a TODO if unsure
- Do NOT suggest skipping the red phase of TDD
- Do NOT use `console.log` for debugging in committed code — use structured logging or remove before commit
