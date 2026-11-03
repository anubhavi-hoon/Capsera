# CAPSERA — Technical Decision Records (`DECISIONS.md`)

This document tracks architectural and technical decisions that refine or operationalize [PRD v1.0](./PRD.md). Per Section 19 of the PRD, shared technical choices and PRD baseline amendments require review and approval by all four team members.

---

## Decision Summary

| ID | Title | Status | Owner / Proposer |
|---|---|---|---|
| ADR-001 | Standardize SQL access on `mysql2/promise` with parameterized queries | **SUPERSEDED by ADR-002** | Member 4 (Foundation Setup) |
| ADR-002 | Migrate RDBMS from MySQL to PostgreSQL (`pg` connection pool + `$1..$n` parameterized queries) per Faculty Requirement | **PROPOSED (Pending Formal Team Approval)** | Member 4 (Foundation Setup) |
| ADR-003 | Establish Single Source of Truth for PostgreSQL Schema (`docs/DATA_MODEL.md` + `database/migrations/`) & Proposed PRD Section 12 "Database Schema Guidelines" Subsection | **PROPOSED (Pending Formal Team Approval)** | Member 4 (Foundation Setup) |
| OPEN-001 | Frontend HTTP Client (`fetch` vs `axios`) | **UNRESOLVED (Pending Team Decision)** | All Members |
| OPEN-002 | Authentication Transport & Session Strategy (HttpOnly Cookie vs Bearer Token) | **UNRESOLVED (Pending Member 1 & Team Decision)** | Member 1 |
| OPEN-003 | Request Validation Library / Shared Helper Pattern | **UNRESOLVED (Pending Team Decision)** | All Members |
| OPEN-004 | Certificate Status Representation (`issued_at`/`revoked_at` vs `status` enum) | **UNRESOLVED (Pending Member 4 & Team Decision)** | Member 4 |
| OPEN-005 | Duplicate Enrollment Handling (`409 Conflict` vs Idempotent Return) | **UNRESOLVED (Pending Member 2 & Team Decision)** | Member 2 |
| OPEN-006 | Test Runner Selection (Backend & Frontend) | **UNRESOLVED (Pending Team Decision)** | All Members |

---

## ADR-002: Database Migration from MySQL 8+ to PostgreSQL (`pg` Pool & Parameterized Queries)

- **Status:** PROPOSED — Required by Faculty; Pending Formal Approval from Members 1, 2, 3, and 4
- **Supersedes:** ADR-001 (`mysql2/promise`)
- **Date:** 2026-11-03

### 1. Requirement & Sections Being Changed (PRD Section 19 Change Control)
Our course faculty requires the project to use **PostgreSQL** instead of **MySQL**. Because [docs/PRD.md](./PRD.md) v1.0 is the finalized baseline, it has **not** been silently rewritten. Instead, the exact PRD v1.0 sections requiring formal amendment upon team sign-off are flagged below.

### 2. Reason and Expected Value
- Satisfies the mandatory academic/faculty evaluation requirement for PostgreSQL.
- Preserves all CAPSERA relational integrity guarantees (ACID transactions, foreign keys, unique constraints, composite indexes, and parameterized SQL queries) without introducing an unapproved ORM.

### 3. Scope, Time, and Data/API Compatibility Impact
- **Scope/Time Impact:** Minimal — the switch occurs at the foundation stage before any application business modules or SQL migrations have been implemented.
- **Database Name & API Prefix:** Unchanged (`capsera_db` and `/api/v1`).
- **Naming Conventions:** Unchanged (plural `snake_case` SQL tables and `snake_case` columns; `camelCase` JSON API fields).
- **Driver & Parameter Syntax Impact:**
  - Replace `mysql2` with `pg` (`node-postgres`) using `pg.Pool` in [server/src/config/db.js](../server/src/config/db.js).
  - Default database port changes from `3306` to `5432` (default user `postgres`).
  - Parameterized SQL queries use PostgreSQL positional placeholders (`$1, $2, ...`) via `executeQuery(text, params)` / `pool.query(text, params)` instead of `?`.
  - Transactions use `const client = await pool.connect()` with `BEGIN`, `COMMIT`, `ROLLBACK`, and `client.release()` via `withTransaction(work)`.

---

### 4. Exact Sections of CAPSERA PRD v1.0 Flagged for Amendment

Do **not** edit [docs/PRD.md](./PRD.md) until all four members check off approval below. Once approved, apply the following targeted replacements to `docs/PRD.md`:

| PRD v1.0 Location | Current Text in `docs/PRD.md` | Proposed Amendment Text |
|---|---|---|
| **Document Header Metadata** | `Technology: React + Vite, Node.js + Express, MySQL` | `Technology: React + Vite, Node.js + Express, PostgreSQL` |
| **Section 3.1 — Goals for the MVP** | `Deliver an end-to-end demo using a real MySQL database, not only mock data or localStorage.` | `Deliver an end-to-end demo using a real PostgreSQL database, not only mock data or localStorage.` |
| **Section 6.4 — Lessons and resources** | `Store uploaded files in a suitable storage service; store metadata and access references in MySQL. Large media binaries should not be stored in relational rows.` | `Store uploaded files in a suitable storage service; store metadata and access references in PostgreSQL. Large media binaries should not be stored in relational rows.` |
| **Section 8.3 — Reliability and data integrity** | `MySQL is the source of truth for user, course, enrollment, assessment, progress, feedback, and certificate records.` | `PostgreSQL is the source of truth for user, course, enrollment, assessment, progress, feedback, and certificate records.` |
| **Section 8.4 — Performance and scalability** | `Keep uploaded media in file/object storage rather than MySQL.` | `Keep uploaded media in file/object storage rather than PostgreSQL.` |
| **Section 9.1 — Approved stack** | `- **Database:** MySQL 8+ using InnoDB.`<br>`- **SQL access:** choose one approach centrally (recommended: mysql2/promise with parameterized queries, or an agreed ORM; do not mix access patterns across modules).`<br>`- **Deployment:** static frontend hosting plus Node API hosting and managed MySQL, depending on free-tier availability.` | `- **Database:** PostgreSQL 15+ (with ACID transactions and relational constraints).`<br>`- **SQL access:** choose one approach centrally (recommended: pg / node-postgres Pool with parameterized queries ($1, $2, ...), or an agreed ORM; do not mix access patterns across modules).`<br>`- **Deployment:** static frontend hosting plus Node API hosting and managed PostgreSQL, depending on free-tier availability.` |
| **Section 9.2 — Architecture** | `Browser (React/Vite) → REST API (Node/Express) → MySQL`<br>`The browser must not connect directly to MySQL.` | `Browser (React/Vite) → REST API (Node/Express) → PostgreSQL`<br>`The browser must not connect directly to PostgreSQL.` |
| **Section 11.1 — General rules** | `IDs are generated by the database and never trusted from public signup payloads for ownership or role.` *(Note: referenced as MySQL in API_CONTRACT.md)* | Keep PRD rule; ensure `API_CONTRACT.md` references PostgreSQL identity/serial generation. |
| **Section 17 — Assumptions and constraints** | `The selected database is MySQL; this is not a MERN application.` | `The selected database is PostgreSQL (per faculty requirement); this is not a MERN application.` |
| **Section 20 — Final MVP definition** | `The system must use the agreed React/Vite, Express, and MySQL stack, enforce server-side permissions, and be usable on mobile and desktop.` | `The system must use the agreed React/Vite, Express, and PostgreSQL stack, enforce server-side permissions, and be usable on mobile and desktop.` |

### 5. Approval Checklist (Required by PRD Section 19)
- [ ] Member 1 (Identity, Profiles, Governance)
- [ ] Member 2 (Courses, Lessons, Resources, Enrollments)
- [ ] Member 3 (Assessments, Attempts, Progress, Feedback)
- [ ] Member 4 (Competency, Certificates, Announcements, Admin Dashboard)

---

## ADR-003: Single Source of Truth for PostgreSQL Schema & Proposed PRD Section 12 Amendment ("Database Schema Guidelines")

- **Status:** PROPOSED — Pending Formal Approval from Members 1, 2, 3, and 4
- **Date:** 2026-11-03
- **Related Documents:** [docs/PRD.md](./PRD.md) (Section 12 — Conceptual data model, Section 19 — Change control), [docs/DATA_MODEL.md](./DATA_MODEL.md), `database/migrations/`

### 1. Schema Reference Inspection & Selection
- **Repository Inspection:** A search of `docs/` and `database/` confirmed that no standalone authoritative SQL file (such as `schema.sql`) has been provided or designated by the team lead.
- **Selected Schema Reference Path:** `docs/DATA_MODEL.md` is designated as the proposed schema design reference (single source of truth for design), with `database/migrations/` containing the executable, versioned PostgreSQL migrations for `capsera_db`.

### 2. Reason and Expected Value
- Prevents schema drift, duplicate entities, conflicting foreign keys, or uncoordinated column names across the four parallel module owners (Members 1–4).
- Aligns with **CAPSERA PRD v1.0** Section 12 (*"The exact schema will be documented in `DATA_MODEL.md` and implemented as versioned migrations"*) and **ADR-002** (PostgreSQL substitution).

### 3. Scope, Time, and Data/API Compatibility Impact
- **Scope/Time Impact:** Documentation-only governance rule; zero impact on delivery timeline.
- **Data/API Compatibility Impact:** Ensures all modules reference identical plural `snake_case` PostgreSQL table names, `snake_case` columns, primary/foreign keys, and constraints.

### 4. Proposed Amendment to `docs/PRD.md` (Section 12 — Conceptual data model)

Because `docs/PRD.md` is the finalized v1.0 baseline, it has **not** been modified directly. Upon formal team approval, append the following subsection to **Section 12 (`Conceptual data model`)** of `docs/PRD.md`:

```markdown
### 12.1 Database Schema Guidelines

`docs/DATA_MODEL.md` serves as the proposed schema design reference for the `capsera_db` PostgreSQL database, with `database/migrations/` containing the executable PostgreSQL migrations.

- All team members must follow the agreed PostgreSQL schema.
- The schema reference (`docs/DATA_MODEL.md`) defines table names, columns, primary keys, foreign keys, relationships, and constraints.
- Approved schema changes must be implemented using versioned migrations in `database/migrations/`.
- No member may independently introduce duplicate entities, incompatible column names, or conflicting relationships.
- Shared schema changes require team discussion and approval.
```

### 5. Approval Checklist (Required by PRD Section 19)
- [ ] Member 1 (Identity, Profiles, Governance)
- [ ] Member 2 (Courses, Lessons, Resources, Enrollments)
- [ ] Member 3 (Assessments, Attempts, Progress, Feedback)
- [ ] Member 4 (Competency, Certificates, Announcements, Admin Dashboard)

---

## ADR-001: Database Access Layer — `mysql2/promise` with Parameterized Queries (Historical Record)

- **Status:** SUPERSEDED by **ADR-002** on 2026-11-03 due to faculty requirement for PostgreSQL.
- **Summary:** Originally proposed `mysql2/promise` against MySQL 8+ (`InnoDB`). Replaced by `pg` (`node-postgres`) connection pool with `$1, $2, ...` parameterized queries and `withTransaction` helper in `server/src/config/db.js`.

---

## Unresolved Decisions Requiring Team Agreement

### OPEN-001: Frontend HTTP Client (`fetch` vs `axios`)
- **PRD Reference:** Section 9.1 (*"HTTP client: Axios or `fetch`; team must standardize on one client."*)
- **Current Foundation State:** A minimal `fetch`-based wrapper is provided at `client/src/services/apiClient.js` for the initial `/api/v1/health` check so no unapproved third-party HTTP dependency is forced prematurely.
- **Action Required:** Team must confirm whether to keep native `fetch` via `apiClient.js` or install `axios` before feature modules are built.

### OPEN-002: Authentication Transport & Password Hashing Library
- **PRD Reference:** Section 6.1, Section 8.1 (*"Hash passwords using bcrypt or Argon2... Use HTTPS in deployment and secure HttpOnly cookies or an equivalently secure, consistently implemented authentication design."*)
- **Action Required:** Member 1 to propose the exact token/session mechanism (e.g., JWT in `HttpOnly` cookie with CSRF protection vs Authorization header) and password hashing library (`bcrypt` vs `argon2`).

### OPEN-003: Request Validation Strategy
- **PRD Reference:** Section 9.1 (*"Validation: use one agreed schema validation library or shared validation helpers."*)
- **Action Required:** Team to agree on either a lightweight schema library (e.g., `zod`, `joi`, `express-validator`) or shared validation helper functions in `server/src/shared/`.

### OPEN-004: Certificate Status Representation
- **PRD Reference:** Section 10.3 (*"Certificate statuses should be represented by `issued_at` and nullable `revoked_at`, or by one explicitly standardized status field. Do not mix both models without a decision record."*)
- **Action Required:** Member 4 to propose the exact model in `docs/DATA_MODEL.md` for team sign-off before creating the `certificates` migration.

### OPEN-005: Duplicate Enrollment & Duplicate Attempt Submission Semantics
- **PRD Reference:**
  - Section 6.3: *"Duplicate enrollment is rejected or returns the existing enrollment consistently; the implementation must choose one behaviour and test it."*
  - Section 6.5: *"Submission must be idempotent or reject a second submission consistently."*
- **Action Required:** Member 2 (enrollment) and Member 3 (assessment submission) to record the chosen HTTP status (`409` vs `200`) in `docs/API_CONTRACT.md`.

### OPEN-006: Testing Libraries
- **PRD Reference:** Section 9.1 (*"Testing: API integration tests plus frontend critical-flow tests; exact test libraries are a team decision recorded before implementation."*)
- **Action Required:** Team to select backend test runner (e.g., Node built-in `node:test` or `vitest`/`jest` + `supertest`) and frontend test runner (e.g., `vitest` + React Testing Library).
