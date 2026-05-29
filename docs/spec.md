# CodeStories — Feature Specification

**Version**: 0.1 (pre-implementation)  
**Last updated**: 2025  
**Status**: Draft

---

## 1. Overview

### Problem Statement

Learning data structures and algorithms (DSA) is a high-stakes, high-anxiety experience for most developers. LeetCode-style platforms present decontextualised problems with no narrative, no memory hooks, and no reason to care. The result: people grind problems mechanically without building durable intuition.

### Solution

CodeStories wraps each DSA concept in a short illustrated comic narrative. The user reads a story arc (6–8 panels), absorbs the concept through metaphor and character, then immediately applies it by solving the embedded problem. Progress through arcs is gated on solving each problem — reading alone isn't enough.

### Success Metrics

- User can read a story arc and solve the attached problem in < 30 minutes for beginner topics
- Retention: 70% of users who solve story 1 return to solve story 2
- Story arc completion rate ≥ 60% (vs. ~10% for raw LeetCode problem completion)

---

## 2. Functional Requirements

### 2.1 Story Reading Experience

- [ ] User can browse a library of story arcs organised by DSA category
- [ ] Each story arc contains 6–8 sequential panels
- [ ] Each panel has: illustration area, caption/narration text, optional character dialogue
- [ ] User navigates panels with prev/next buttons and left/right arrow keys
- [ ] Progress within a story is preserved if the user closes and re-opens
- [ ] Final panel transitions directly into the embedded problem

### 2.2 Code Sandbox

- [ ] User can write JavaScript (and later Python) solutions in an embedded Monaco Editor
- [ ] Code runs in an isolated Web Worker sandbox (no network access, no DOM)
- [ ] Execution times out after 5 seconds (returns `TLE` status)
- [ ] Each submission is validated against N ≥ 5 test cases
- [ ] Per-test output: `passed | failed | error | TLE` with expected vs. actual diff
- [ ] User can run code as many times as they like before "submitting"
- [ ] "Submit" locks in the result and records it in the user's history

### 2.3 User Progress

- [ ] Users sign in with GitHub OAuth or magic-link email
- [ ] Completion of a story arc (= solving the problem) unlocks the next arc in the sequence
- [ ] User profile shows: arcs completed, current streak, submission history
- [ ] Stories are tagged with difficulty: Beginner / Intermediate / Advanced
- [ ] Completed arcs show a visual badge; locked arcs show a lock icon

### 2.4 Story Content (MDX)

- [ ] Story panels are authored in MDX format
- [ ] MDX supports: text, images, code snippets (syntax-highlighted), callouts
- [ ] Each story file exports: `metadata` (title, category, difficulty, problemId) and panel array

---

## 3. Non-Functional Requirements

- [ ] First Contentful Paint < 1.5s on 3G (story images lazy-loaded)
- [ ] Code sandbox evaluation < 2s for typical O(n²) solutions on n ≤ 10,000
- [ ] Accessible: WCAG 2.1 AA compliance (keyboard navigation, screen reader support)
- [ ] Mobile-responsive layout (320px minimum width)
- [ ] Zero third-party code execution in sandbox (Web Worker with restricted context)

---

## 4. Data Model

### Story

```typescript
type Story = {
  id: string;               // e.g. "array-kingdom"
  title: string;
  description: string;      // 1-2 sentence teaser
  category: DSACategory;    // ARRAYS | LINKED_LISTS | TREES | GRAPHS | DP | ...
  difficulty: Difficulty;   // BEGINNER | INTERMEDIATE | ADVANCED
  panels: Panel[];
  problemId: string;        // references a Problem
  prerequisites: string[];  // story IDs that must be completed first
};

type Panel = {
  id: string;
  illustrationUrl: string;
  narration: string;        // caption text below the image
  dialogue?: Dialogue[];    // speech bubbles
};

type Dialogue = {
  character: string;
  text: string;
  position: "left" | "right";
};
```

### Problem

```typescript
type Problem = {
  id: string;
  title: string;
  storyId: string;
  prompt: string;           // Markdown problem description
  starterCode: Record<Language, string>;
  testCases: TestCase[];
  solutionHints: string[];
  officialSolution: Record<Language, string>;
};

type TestCase = {
  id: string;
  input: unknown[];
  expected: unknown;
  label?: string;           // e.g. "empty array", "all duplicates"
  isHidden?: boolean;       // hidden cases only run on submit, not on Run
};
```

### User Progress (DB — Prisma)

```prisma
model User {
  id          String   @id @default(cuid())
  email       String   @unique
  githubId    String?  @unique
  createdAt   DateTime @default(now())
  submissions Submission[]
  progress    StoryProgress[]
}

model StoryProgress {
  id        String   @id @default(cuid())
  userId    String
  storyId   String
  status    ProgressStatus  // STARTED | COMPLETED
  startedAt DateTime @default(now())
  completedAt DateTime?
  user      User @relation(fields: [userId], references: [id])
  @@unique([userId, storyId])
}

model Submission {
  id         String   @id @default(cuid())
  userId     String
  problemId  String
  language   String
  code       String
  status     SubmitStatus  // PASSED | FAILED | TLE | ERROR
  runtime    Int?          // ms
  submittedAt DateTime @default(now())
  user        User @relation(fields: [userId], references: [id])
}
```

---

## 5. API Interface

| Method | Route | Auth | Description |
|--------|-------|------|-------------|
| `GET` | `/api/stories` | optional | List all stories (with user progress if authed) |
| `GET` | `/api/stories/:id` | optional | Single story metadata + panels |
| `GET` | `/api/problems/:id` | optional | Problem prompt, starter code, visible test cases |
| `POST` | `/api/run` | optional | Run code against visible tests (ephemeral, not saved) |
| `POST` | `/api/submit` | required | Submit solution, save result, unlock next story |
| `GET` | `/api/progress` | required | Current user's story progress |
| `GET` | `/api/profile` | required | User profile + stats |

---

## 6. Test Plan

### Unit Tests (Vitest)

- `StoryPanel` renders narration text and image slot
- `StoryPanel` renders dialogue bubbles when provided
- `StoryReader` advances panel on next(), retreats on prev()
- `StoryReader` does not go below panel 0 or above panel.length-1
- `CodeEditor` calls `onChange` with updated code value
- `sandbox.run(code, testCases)` → passes for correct solution
- `sandbox.run(code, testCases)` → returns `TLE` for infinite loop
- `sandbox.run(code, testCases)` → returns `ERROR` for syntax errors
- `useProgress` returns correct status for each story

### Integration Tests

- `POST /api/submit` with valid passing code → returns `PASSED`, creates Submission record
- `POST /api/submit` with failing code → returns `FAILED`, does NOT mark story complete
- `POST /api/submit` when unauthenticated → returns 401
- Story unlock: completing story A with prerequisite unlocks story B

### E2E Tests (Playwright)

- User opens story index, clicks "Array Kingdom", reads all 8 panels
- User reaches problem panel, writes correct solution, runs code, sees all tests pass
- User submits, sees "Story Complete!" banner, sees next story unlocked
- User revisits story index — "Array Kingdom" shows ✅ badge

### Edge Cases

- Code that throws a synchronous exception: caught and reported as `ERROR`
- Code that produces the right answer but exceeds 5s: reported as `TLE`
- Problem with no visible test cases (all hidden): "Run" shows "Submit to see results"
- Story with no prerequisites: visible and accessible to unauthenticated users

---

## 7. Open Questions

- [ ] **Illustration sourcing**: commission artists, generate with Stable Diffusion, or use SVG characters? Consistent style across all stories is critical.
- [ ] **Python support**: Pyodide adds ~10MB WASM download. Worth it at launch or later?
- [ ] **Offline support**: should stories be readable offline (PWA)? Problem submission still needs network.
- [ ] **Story ordering**: strict linear sequence per category, or a dependency graph the user can explore freely?
- [ ] **Monetisation**: freemium (first 3 stories free, rest paywalled) or fully free with a Patreon?
- [ ] **Community problems**: allow users to submit problem + story pairs for review?
