# Issue Tracker

A minimal kanban-style issue tracker built with [Next.js](https://nextjs.org/) (App Router). Issues are stored in memory and reset on server restart. Drag-and-drop cards across backlog, todo, in progress, and done columns, with a built-in Claude Code plugin for enforcing typecheck-before-commit and auto-wrapping up issues.

## Tech Stack

- **Framework:** Next.js 15 (App Router)
- **UI Library:** React 19
- **Language:** TypeScript (strict mode)
- **Drag and Drop:** `@dnd-kit/core` + `@dnd-kit/sortable`
- **Package Manager:** [Bun](https://bun.sh/)
- **Plugin System:** Claude Code plugin with hooks + skills

## Features

- Create new issues via the "Add a new issue..." input
- Drag-and-drop issues between columns (backlog, todo, in progress, done)
- Change issue status via the dropdown on each card
- Reorder issues within a column by dragging
- All data persisted in-memory during server runtime
- **Claude Code Plugin (optional):** blocks `git commit` on typecheck errors and automates the wrap-up ritual

## Project Structure

```
├── app/
│   ├── api/
│   │   ├── columns/[status]/reorder/route.ts   — PUT: reorder issues in a column
│   │   └── issues/
│   │       ├── route.ts                        — GET (list) + POST (create)
│   │       └── [id]/route.ts                   — GET, PATCH, DELETE single issue
│   ├── globals.css                             — global styles (kanban board layout)
│   ├── layout.tsx                              — root layout
│   └── page.tsx                                — entry page ("Issues" heading + Board)
├── components/
│   ├── Board.tsx                               — main DndContext, drag-over/drag-end handlers, issue form
│   ├── Column.tsx                              — droppable column (useDroppable)
│   └── IssueCard.tsx                           — sortable card (useSortable) + status selector
├── lib/
│   ├── store.ts                                — in-memory singleton IssueStore (Map-backed)
│   └── types.ts                                — Issue, Status, STATUSES definitions
├── plugin/                                     — Claude Code plugin (see below)
├── AGENTS.md                                   — detailed agent guide (setup, API, drag-drop behavior)
├── EXERCISE.md                                 — "Definition of Done" plugin exercise brief
└── SOLUTION.md                                 — annotated solution for the exercise
```

## Data Model

```typescript
type Status = "backlog" | "todo" | "in_progress" | "done";

interface Issue {
  id: string;
  title: string;
  description: string;
  status: Status;
  order: number;       // sort order within a column
  createdAt: string;   // ISO timestamp
}
```

Issues are stored in an in-memory `Map<string, Issue>` inside a singleton `IssueStore` (`lib/store.ts`). The instance is pinned to `globalThis.__issueStore` during development to survive hot-reloads. Three seed issues are created on first instantiation.

## Setup

```bash
bun install
bun run dev
```

Open http://localhost:3000.

## API

- `GET /api/issues` — List all issues (sorted by order).
- `POST /api/issues` — Create an issue. Body: `{ title: string, description?: string, status?: Status }`.
- `GET /api/issues/:id` — Fetch a single issue.
- `PATCH /api/issues/:id` — Partial update. Body: `{ title?, description?, status?, order? }`. Setting `status` without `order` auto-computes the next order.
- `DELETE /api/issues/:id` — Delete an issue. Returns `204 No Content`.
- `PUT /api/columns/:status/reorder` — Reorder issues in a column. Body: `{ orderedIds: string[] }`.

## Claude Code Plugin

The `plugin/` directory contains a [Claude Code](https://claude.ai/code) plugin that enforces team workflow rules. It ships with the project so any developer who runs `claude` inside this repo gets the same guardrails.

### PreToolUse Hook — Typecheck Gate

**Location:** `plugin/hooks/hooks.json` → `plugin/scripts/typecheck-gate.sh`

- Fires before **every** Bash tool call.
- Only acts on `git commit` commands — all other commands pass through silently.
- Runs `bun run typecheck`; if it fails, the commit is **blocked** and the compiler errors are surfaced to the agent.
- This is *enforced* at the harness level (outside the model's control), not a prompt-based suggestion.

### Skill — Wrap Up an Issue

**Location:** `plugin/skills/wrap-up/SKILL.md`

- Invoked automatically when the user says something like *"wrap up the board-layout issue"*, *"I'm done with #3"*, or any similar phrasing.
- Performs the team's full wrap-up ritual in one motion:
  1. Resolves the issue (finds it by id or title match).
  2. Writes a one-sentence summary from the recent diff.
  3. Applies all three updates in a single `PATCH` — sets `status: "done"`, clears `assignee`, appends the summary to `description`.

### Demo

1. Break a type in `lib/types.ts`.
2. Ask the agent to commit → the hook **blocks** the commit and shows the compiler errors.
3. Fix the type.
4. Say *"I'm done with the board layout issue"* → the skill fires, the card moves to **Done** on the board, assignee clears, and a summary is recorded.
