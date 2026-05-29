# code-stories

> 🎬 Short-form video stories that teach DSA and Leetcode problems — like TikTok for algorithms.

![Status](https://img.shields.io/badge/status-🚧%20Early%20Development-orange)
![Stack](https://img.shields.io/badge/stack-Next.js%20%2B%20TypeScript-blue)

---

## What It Is

Code Stories turns dry algorithm problems into **visual, narrative-driven short videos**. Each story walks through a Leetcode problem or DSA concept as an animated explainer — voiceover, step-by-step visualization, and a swipeable card-style feed. Think Reels, but you leave knowing how to solve Two Sum.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Animation | Framer Motion |
| Video | HTML5 video + Supabase Storage |
| Database | Supabase (Postgres + Auth + Storage) |
| State | Zustand |
| Testing | Vitest + React Testing Library + Playwright |
| Deployment | Vercel |

---

## Getting Started

```bash
# Clone
git clone git@github.com:joshiujjwal/code-stories.git
cd code-stories

# Install dependencies
npm install

# Copy env vars
cp .env.example .env.local
# Fill in Supabase URL + keys

# Run dev server
npm run dev

# Run tests
npm test

# Run e2e tests
npm run test:e2e
```

---

## Project Structure

```
code-stories/
├── src/
│   ├── app/              # Next.js App Router pages & layouts
│   ├── components/       # Reusable UI components
│   │   ├── feed/         # StoryCard, StoryFeed, ScrollSnapContainer
│   │   ├── player/       # StoryPlayer, StoryOverlay, ProgressBar
│   │   └── ui/           # Button, Badge, TagChip, Skeleton
│   ├── lib/              # Supabase client, stories, progress, auth
│   ├── hooks/            # useStoryFeed, useAuth, useProgress
│   └── types/            # Shared TypeScript interfaces
├── tests/
│   ├── unit/             # Vitest unit tests (mirrors src/)
│   ├── integration/      # API + DB integration tests
│   └── e2e/              # Playwright end-to-end tests
├── docs/
│   ├── spec.md           # Feature specification
│   └── adr/              # Architecture Decision Records
├── .github/
│   └── copilot-instructions.md
├── CLAUDE.md             # AI agent context (Anthropic)
├── AGENTS.md             # Agent instructions (OpenAI)
└── TODO.md               # Evidence-gated task breakdown
```

---

## Contributing

1. Read `TODO.md` — find the next unchecked item in the current phase
2. **Write tests first** (red phase) before any implementation
3. Implement until tests pass (green phase)
4. Review your own diff — no unreviewed code ships
5. PRs must include: what changed, test output, manual testing evidence
6. Keep PRs small and focused — one feature or fix per PR
7. Update `CLAUDE.md` or `AGENTS.md` if you learn something non-obvious

---

## License

Private. All rights reserved.
