# AGENTS.md

**Setup & Scripts**
- Run `bun install` to install dependencies (project uses Bun, not npm).
- Development server: `bun run dev` (starts Next.js on http://localhost:3000).
- Production build: `bun run build`.
- Start production server: `bun run start`.
- Type‑checking only: `bun run typecheck` (runs `tsc --noEmit`).

**Project Characteristics**
- Minimal Kanban issue tracker built with **Next.js 15 (App Router)** and **React 19**.
- No test framework, linter, or formatter configured.
- All data lives in an **in‑memory singleton store** (`lib/store.ts`). Data resets on server restart or hot‑reload.

**API Endpoints** (App Router route handlers under `app/api/`)
- `GET /api/issues` – list all issues sorted by order.
- `POST /api/issues` – create issue (`{title, description?, status?}`).
- `GET /api/issues/:id` – fetch single issue.
- `PATCH /api/issues/:id` – partial update (`title?, description?, status?, order?`). Changing `status` without `order` auto‑computes next order.
- `DELETE /api/issues/:id` – delete issue.
- `PUT /api/columns/:status/reorder` – reorder issues in a column (`{orderedIds: string[]}`).

**TypeScript Path Alias**
- `@/*` resolves to the project root (`./*`). Use when importing modules.

**Frontend Entry Points**
- `app/page.tsx` – main page.
- `components/Board.tsx` – loads issues via API, handles drag‑and‑drop state.
- `components/Column.tsx` – droppable column.
- `components/IssueCard.tsx` – sortable issue card.

**Drag‑and‑Drop Behavior**
- Implemented with `@dnd-kit` libraries.
- `Board.tsx` updates issue status optimistically on drag‑over and persists order on drag‑end via the reorder API.
