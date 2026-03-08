# ArchGuard Frontend Development Plan

## Objective
Build a production-ready Next.js + shadcn frontend for ArchGuard that supports auth, project management, architecture visibility, and compliance reporting against the existing FastAPI backend.

## Context Gathered
- Product and roadmap sources:
  - `PRD.md`
  - `project-design-document.txt`
  - `api-spec-and-data-models.md`
  - `progress.txt`
- Backend API implementation:
  - `backend/app/api/v1/endpoints/auth.py`
  - `backend/app/api/v1/endpoints/projects.py`
  - `backend/app/api/v1/endpoints/graph.py`
  - `backend/app/api/v1/endpoints/compliance.py`
  - `backend/app/api/v1/endpoints/webhooks.py`
- Data contracts and roles:
  - `backend/app/schemas/auth.py`
  - `backend/app/schemas/project.py`
  - `backend/app/schemas/compliance.py`
  - `backend/app/models/*.py`

## Implemented Baseline (This Pass)
- Next.js app scaffolded in `frontend/web` with TypeScript, App Router, Tailwind.
- shadcn initialized and core UI components added.
- Implemented app shell and routing:
  - Public routes: `/`, `/login`
  - Protected routes: `/dashboard`, `/projects`, `/projects/[projectId]`, `/compliance`
- Auth and session flow:
  - Login/register/logout wiring to backend auth routes.
  - Token + user persisted in localStorage.
- Typed API service layer:
  - Envelope parsing and typed DTOs for projects, versions, rules, reports, violations.
- Visual direction:
  - Custom typography (`Space Grotesk`, `IBM Plex Mono`)
  - Non-default warm/cyan palette and gradient atmosphere.
  - Meaningful entrance/stagger motion classes.

## Architecture for FE
- Framework: Next.js App Router (React 19).
- State model:
  - Global auth state via context provider.
  - Page-level server interactions with local component state.
- API strategy:
  - Unified request utility in `src/lib/api-client.ts`.
  - Domain service functions in `src/lib/api-service.ts`.
  - Typed models in `src/lib/types.ts`.
- UI system:
  - shadcn components + CSS tokenized theme in `src/app/globals.css`.

## Phase Plan

### Phase 1 (Complete)
- Scaffold app and design system.
- Auth flow and protected shell.
- Core read/create pages for projects and compliance visibility.

### Phase 2 (Complete)
- Project workspace completeness:
  - ✓ Create architecture versions with status lifecycle management (draft → under_review → approved → active → deprecated)
  - ✓ CRUD operations for architecture rules (create, update, delete with severity levels)
  - ✓ Full API service layer for version management, rules, components, relationships
- Graph management UI:
  - ✓ Add/edit/delete intended components with layer levels and types
  - ✓ Create/delete relationships between components (ALLOWED, FORBIDDEN, REQUIRES, LAYER_ABOVE)
  - ✓ Tabbed interface for Components, Relationships, and Rules
  - ⏳ Mapping UI for intended-to-actual components (deferred - requires static analysis first)

### Phase 3 (Next)
- Compliance operations UX:
  - Trigger static analysis and compliance check from UI.
  - Poll/report progress states (pending/running).
  - Better violation filtering and severity drilldowns.

### Phase 4
- Visualization:
  - Graph explorer (React Flow) for intended vs actual graphs.
  - Overlay violations and path tracing.

### Phase 5
- Production hardening:
  - Runtime validation for backend response drifts.
  - Error boundaries and retry policies.
  - E2E tests for auth and critical workflows.
  - Accessibility pass and performance tuning.

## API Mapping Matrix
- Auth
  - `POST /auth/login`
  - `POST /auth/register`
  - `POST /auth/logout`
  - `GET /auth/me`
- Projects & architecture
  - `GET /projects`
  - `POST /projects`
  - `GET /projects/{project_id}`
  - `GET /projects/{project_id}/architecture`
  - `GET /architecture/{version_id}/rules`
- Compliance
  - `GET /projects/{project_id}/compliance/reports`
  - `GET /projects/{project_id}/compliance/reports/{report_id}/violations`
  - `GET /projects/{project_id}/compliance/health`

## Known Integration Risks (Backend Contract)
- `compliance.py` endpoint implementation appears to have runtime mismatches with service signatures for analysis/actual-graph paths.
- Some model field references in compliance handlers may not match ORM attribute names exactly.
- UI should defensively handle 4xx/5xx responses on those endpoints until backend contracts are aligned.

## Testing Plan
- Unit tests
  - API client error parsing and envelope handling.
  - Auth provider state transitions.
- Integration tests
  - Login/register flow.
  - Project create and list.
  - Compliance report and violation rendering.
- E2E (Playwright)
  - Full happy path: auth -> create project -> view project -> view compliance.

## Delivery Milestones
1. M1: Baseline app shell + auth + projects list/create + compliance viewer.
2. M2: Architecture version/rule management and graph CRUD UX.
3. M3: Compliance trigger workflows and robust reporting interactions.
4. M4: Graph visualization and production hardening.

## Runbook
- Start backend services from repo root:
  - `docker compose up -d`
- Start frontend:
  - `cd frontend/web`
  - `npm install`
  - `npm run dev`
- Ensure `.env.local` sets:
  - `NEXT_PUBLIC_API_BASE_URL=http://localhost:8000/api/v1`
