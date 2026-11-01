# Design

## Context

This is a greenfield HTTP API with no existing application code or infrastructure. See proposal.md for the motivation and the capability specs for required behavior. The selected stack is TypeScript, Node.js, Express, and SQLite. Authentication is out of scope; write requests identify the acting employee, and the employee-to-manager directory is seeded.

## Goals / Non-Goals

**Goals:**
- Keep the API and persistence model small enough for a single service.
- Make claim status changes auditable and safe against concurrent decisions.
- Keep monetary comparisons exact at EUR thresholds.

**Non-Goals:**
- Production-grade identity verification or access control.
- External identity-provider, payment, receipt-storage, or finance-system integrations.
- Multi-service deployment or database high availability.

## Decisions

### HTTP API

- `POST /claims` accepts `amountEur`, `description`, and `receiptAttached`; `X-Employee-Id` identifies the submitting employee. It returns the created claim, including its identifier, status, and rejection reason when business validation rejects it. A structurally valid request creates a claim even when its business validation fails.
- `GET /claims/:id` returns claim details and current status without requiring an employee identity, matching the proposal's retrieval requirement.
- `POST /claims/:id/decision` accepts `approve` or `reject`; `X-Employee-Id` identifies the actor. The service looks up the claimant's manager in the seeded directory before allowing a decision.
- Use `201` for a created claim, including a rejected claim; `400` for malformed request data; `403` for an unknown or unauthorized write actor; `404` for an unknown claim; and `409` when a decision targets a claim that is no longer pending.

These are new endpoints, so there is no existing contract to preserve. Separate submission and decision routes keep the two actions explicit. A generic state-update endpoint was considered but would make it easier to bypass the approval rules.

### Persistence and state transitions

- Use SQLite with integer `amount_cents` rather than floating-point amounts. The HTTP amount is EUR with at most two decimal places; enforce this at the boundary, and compare cents against 2500 and 5000.
- Store employees and their manager relationship in a seeded `employees` table. Store claims with their submitter, amount, description, receipt flag, current status, rejection reason, and timestamps.
- Store each initial and subsequent status transition in a `claim_status_history` table, including the actor where applicable and the validation or decision reason. This preserves a trace of automatic approvals, validation rejections, and manager decisions.
- Apply claim creation, initial status selection, and history insertion in one transaction. Apply manager decisions in one transaction using a conditional update from `pending_manager_approval`; if the update affects no row, return a conflict without recording a second decision.
- Derive the permitted manager from the claim's submitter and the employee directory on every decision. Never accept a manager ID supplied by the request as authority.

Alternatives considered: storing EUR as SQLite `REAL` risks threshold and rounding errors; storing only the latest status loses the decision trail. Both are rejected in favor of integer cents and an append-only status history.

### Runtime and project structure

- Implement one TypeScript Express service and one SQLite database file. Keep HTTP parsing, claim/approval rules, and persistence in separate modules so the rules can be tested without starting the server.
- Use parameterized SQL for all database operations and enable SQLite foreign-key enforcement. Seed the employee directory idempotently at startup or through a repeatable seed command.
- Use environment configuration for the database path and listening port; do not make behavior depend on a developer's working directory.

## Risks / Trade-offs

- **A caller can spoof `X-Employee-Id` because authentication is explicitly out of scope** → Treat this as a local/demo API only; do not expose it to untrusted networks. Add real authentication and derive employee identity from verified credentials before production use.
- **A local SQLite file is a single-instance persistence choice** → Back up the database and keep the service single-instance; reassess storage before horizontal scaling or high-availability deployment.
- **Seeded manager relationships may be stale or incomplete** → Reject decisions when the claimant or manager is absent from the directory, and keep directory changes explicit and reviewable.
- **Public claim retrieval exposes submitted claim details** → Limit returned fields to claim data required by this API and revisit read authorization before using real employee data.

## Migration Plan

There is no existing application or data to migrate. On first run, create the SQLite schema and seed the employee directory. Rollback consists of stopping the service and retaining or removing the local database according to the test/deployment environment; there is no external state to undo.
