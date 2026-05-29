# CodeStories 📖⚡

> **Learn and solve coding/algorithm problems through narrative comic stories**

![Status](https://img.shields.io/badge/status-🚧%20Early%20Development-orange)
![Stack](https://img.shields.io/badge/stack-Next.js%20%7C%20TypeScript%20%7C%20Tailwind-blue)

Each data structure or algorithm concept is taught through an engaging illustrated story arc — making DSA memorable, not miserable. Think of it as LeetCode meets a graphic novel.

---

## ✨ Concept

| Old Way | CodeStories Way |
|---|---|
| "Implement BFS on a graph" | A courier navigating a city block-by-block, visiting every street before going deeper |
| "Reverse a linked list" | A train switching directions at a mountain pass |
| "Binary search" | A detective cutting a suspect list in half each clue |

---

## 🛠 Tech Stack

- **Framework**: Next.js 14 (App Router) + TypeScript
- **Styling**: Tailwind CSS + shadcn/ui
- **Story Content**: MDX (rich story + code interleaved)
- **Code Runner**: Monaco Editor + in-browser JS execution sandbox
- **Database**: PostgreSQL via Prisma (user progress, submissions)
- **Auth**: NextAuth.js
- **Testing**: Vitest + Playwright (E2E)
- **Deploy**: Vercel (frontend) + Railway/Supabase (DB)

---

## 🚀 Getting Started

```bash
git clone https://github.com/YOUR_USERNAME/code-stories.git
cd code-stories

# Install dependencies
npm install        # TODO: confirm package manager (npm/pnpm)

# Set up environment
cp .env.example .env.local
# Edit .env.local with your DB URL and auth secrets

# Run database migrations
npx prisma migrate dev

# Start dev server
npm run dev        # TODO: runs on http://localhost:3000
```

### Running Tests

```bash
npm run test          # Unit tests (Vitest)
npm run test:e2e      # End-to-end tests (Playwright)
npm run test:watch    # Watch mode
```

---

## 📁 Project Structure

```
code-stories/
├── src/
│   ├── components/        # Reusable UI components (StoryPanel, CodeEditor, etc.)
│   ├── stories/           # MDX story content files (one per DSA concept)
│   ├── problems/          # Problem definitions, test cases, solutions
│   ├── lib/               # Shared utilities (db client, auth helpers, sandboxer)
│   ├── hooks/             # React hooks (useProgress, useStory, useCodeRunner)
│   └── styles/            # Global styles, comic/panel CSS
├── tests/
│   ├── unit/              # Component and utility unit tests
│   ├── integration/       # API route and DB interaction tests
│   └── e2e/               # Full user-journey Playwright tests
├── docs/
│   ├── spec.md            # Feature specification
│   └── adr/               # Architecture Decision Records
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/      # Path-specific Copilot instructions
│   └── skills/
├── README.md
├── TODO.md
├── CLAUDE.md
└── AGENTS.md
```

---

## 🤝 Contributing

- **Write tests first** — no PR without a failing test that your implementation fixes
- **Evidence in PRs** — screenshots, test output, or a short demo gif
- **One concept per PR** — each story/problem pair ships independently
- **Red → Green → Refactor** — strictly in that order
- **No unreviewed code ships** — all PRs require at least one review

---

## 📄 License

MIT
