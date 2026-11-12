# ADR 0003: No database for the MVP

**Status:** Accepted, Phase 1

## Context
We need participant settings and a consent status. We do not yet need user accounts,
multiple participants, runtime admin edits, or stored conversation data.

## Decision
Store non-secret participant configuration as typed TypeScript files and keep IDs and keys in
environment variables. Keep signed consent documents outside the repository.

## Rationale
- No new requirement currently needs a database, and each added service is more to secure and pay for.
- Changing consent status requires a deploy, which is acceptable for a single trusted operator.

## Revisit when
Phase 5 needs runtime revocation without a deploy, multiple participants, or an admin UI.
Candidates are managed PostgreSQL (Supabase, or AWS RDS for AWS learning).
