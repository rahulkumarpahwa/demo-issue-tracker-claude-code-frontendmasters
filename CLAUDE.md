# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A minimal kanban-style issue tracker built with Next.js (App Router). Issues are stored in-memory using a singleton store and reset on server restart.

## Commands

- `bun run dev` — Start the Next.js development server on http://localhost:3000.
- `bun run build` — Build for production.
- `bun run start` — Start the production server.
- `bun run typecheck` — Run TypeScript type checking (`tsc --noEmit`).

## Tech Stack

- Next.js 15.2 with App Router
- React 19
- TypeScript (strict mode)
- `@dnd-kit/core` and `@dnd-kit/sortable` for drag-and-drop
- No test framework or linter is configured.

## Architecture

### In-Memory Data Store

`lib/store.ts` exports a singleton `IssueStore` backed by a `Map<string, Issue>`. The instance is persisted across Next.js dev hot-reloads by attaching it to `globalThis.__issueStore` (only in non-production). The store seeds three sample issues on construction.

### API Routes (Route Handlers)

All API endpoints are implemented as Next.js App Router route handlers under `app/api/`:

- `GET /api/issues` — Returns all issues sorted by `order`.
- `POST /api/issues` — Creates an issue. Requires `title`. Accepts optional `description` and `status` (defaults to `"backlog"`).
- `GET /api/issues/:id` — Returns a single issue.
- `PATCH /api/issues/:id` — Partial update of `title`, `description`, `status`, or `order`. Changing `status` without providing `order` auto-computes the next `order` in the target column.
- `DELETE /api/issues/:id` — Deletes an issue, returns 204 on success.
- `PUT /api/columns/:status/reorder` — Reorders and optionally reassigns issues within a column. Body: `{ orderedIds: string[] }`.

### Frontend

- `components/Board.tsx` is the main client component. It loads issues via `useEffect`, maintains local state (`useState`), and implements an optimistic UI for drag-and-drop.
- `components/Column.tsx` renders a droppable column using `useDroppable`.
- `components/IssueCard.tsx` renders a sortable card using `useSortable`. It includes a `<select>` for status changes. The select has `onPointerDown={(e) => e.stopPropagation()}` to prevent drag interference.
- `lib/types.ts` defines `Issue`, `Status`, and the `STATUSES` array (`backlog` | `todo` | `in_progress` | `done`).

### Drag-and-Drop Behavior

- `Board.tsx` uses `DndContext` with `closestCorners` collision detection.
- `handleDragOver` updates an issue's status optimistically in local state when dragged over a different column.
- `handleDragEnd` computes the final column order, updates local state, and calls `PUT /api/columns/:status/reorder` to persist the order.
