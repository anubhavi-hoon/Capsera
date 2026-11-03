# CAPSERA Database Migrations

This directory holds versioned SQL migration files for the `capsera_db` database (PostgreSQL, per faculty requirement recorded in `docs/DECISIONS.md` — ADR-002).

## Conventions (per PRD v1.0, `docs/DECISIONS.md` & `docs/DATA_MODEL.md`)

1. **Naming Format:** `NNN_description.sql` (e.g., `001_create_users_and_profiles.sql`).
2. **PostgreSQL Compatibility:**
   - Do **not** use MySQL-specific clauses such as `ENGINE=InnoDB`, `AUTO_INCREMENT`, `UNSIGNED`, or backtick identifiers (`` ` ``).
   - Use standard PostgreSQL types and constraints: `GENERATED ALWAYS AS IDENTITY` (or `BIGSERIAL`/`SERIAL`), `TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP`, `CHECK (...)` constraints or PostgreSQL `ENUM` types, and double quotes (`"`) only if identifier quoting is required.
3. **Table & Column Casing:** Plural `snake_case` table names and `snake_case` column names.
4. **Timestamps:** Store timestamps in UTC using `TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP`.
5. **Approval Gate:** Do not add schema migrations until the corresponding table definitions in `docs/DATA_MODEL.md` and `ADR-002` in `docs/DECISIONS.md` are reviewed and approved by the team.
