
[16/11, 10:19 am] Amit: **Project name**

OCM 3.0-Generative Al Omnichannel Content Creation & Review Platform

I

**Client**

[A leading global pharma/healthcare company. Confirm and word it as you prefer.]

**Duration**

[Fill in]

**Description**

Built an enterprise platform that generates, manages and routes regulated marketing content for brands, franchises, regions and customer-journey segments.

- A React/TypeScript frontend lets users plan content (content plans, brand planning) and review it.

- A FastAPI backend, backed by Firestore, Redis, PostgreSQL/pgvector and GCS, exposes the content, assets, segments, pipeline jobs and statistics APIs.

- A separate LangGraph content-factory service runs multi-node generation pipelines for text, images and key visuals, with region-specific pipelines (e.g. IND, RUS) and text validation.

-Generated content goes through MLR (Medical-Legal-Regulatory) review. The platform integrates with Mendix and Bynder through Mulesoft, with 53 asset transfer and callbacks for feedback and approval status.

- Content moves through a status lifecycle: Scheduled Generating Generated → Regenerating

Activate Windows

Go to Settings to activate Windowe

EM

+

→ Submitted for MLR → Approved or Rejected.
[16/11, 10:19 am] Amit: → Submitted for MLR → Approved or Rejected.

-Langfuse provides LLM observability.

**Technology Stack**

Python, FastAPI, LangGraph/LangChain, Pydantic, Firestore, Redis, PostgreSQL/pgVector, GCS/AWS S3, Docker, Jenkins CI/CD, Terraform ('infra'), Azure Entra OIDC, Langfuse, React, TypeScript, Vite, Tailwind, Pytest

**Responsibilities** (edit to match what you actually did)

- Designed and developed the layered backend (routers → services → repositories) with explicit Pydantic models and Firestore repositories. These support subcollections, collection-group queries and audit fields.

-Built LangGraph content-generation pipelines with region-specific nodes, a prompt library and text validation.


















Implement minimal RBAC on the backend APIs of this FastAPI app (monthly objective, content plan, content generation, and any other existing business endpoints).

Roles (only these two patterns, nothing else):
- {region}_{franchise}_read
- {region}_{franchise}_write
Examples: India_atarax_read, russia_allerway_write

Access rules:
- Read roles allow GET endpoints; write roles allow POST/PUT/PATCH/DELETE.
- A write role also implies read for the same region+franchise.
- Access is scoped to a region+franchise pair. Take region and franchise from the existing request path/query/body. Allow only if the user holds a matching role. Match case-insensitively.

Role source and sync:
- Firestore `users` collection is the source of truth. Each user document has a `roles` list.
- On login and on every token refresh, load the user's roles from their Firestore user document and refresh them in the auth context (token claims or session, whichever the app already uses). A role change in Firestore must take effect at the next refresh.
- Reuse the existing auth/token flow and Firestore client. Don't add a new auth system.

Implementation constraints:
- One small module (e.g. `rbac.py`) with a single FastAPI dependency, e.g. `require_access("read" | "write")`. It reads the roles from the auth context, extracts region+franchise, and returns 403 on no match (401 if unauthenticated).
- Apply it via `Depends(...)` on the routes. Don't refactor existing logic.
- No new collections, admin APIs, role hierarchies, or extra roles (no admin/superuser).
- Add focused tests: allowed read, allowed write, write blocked for a read-only role, wrong region, wrong franchise, no roles, and roles refreshed from Firestore on token refresh (mock Firestore).

Inspect the existing auth, Firestore user model and route structure first, then make the smallest possible diff. Summarize the changed files when done.
