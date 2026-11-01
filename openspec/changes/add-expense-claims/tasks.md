# Tasks

## 1. Project foundation and persistence

- [ ] 1.1 Set up the TypeScript Node.js Express project, package scripts, and test runner; verify dependency installation, type-checking, and the empty test suite succeed.
- [ ] 1.2 Create the SQLite schema and repository layer for employees, claims, and status history, with foreign keys and integer EUR cents; verify schema and repository tests cover constraints and transactions.
- [ ] 1.3 Add an idempotent seeded employee-manager directory and document local setup, database initialization, and seed behavior; verify repeated seeding produces no duplicate employees and the documented setup commands work.

## 2. Claim submission and retrieval

- [ ] 2.1 Implement claim submission with required fields, exact EUR-cent parsing, validation rejection reasons, and persisted initial status history; verify tests cover non-positive amounts, the EUR 25 receipt boundary, malformed input, and retrievable rejected claims.
- [ ] 2.2 Route valid submissions by amount, automatically approving claims up to and including EUR 50 and leaving larger claims pending manager approval; verify tests cover EUR 50 and an amount above EUR 50, including their initial history entries.
- [ ] 2.3 Implement claim retrieval by identifier without an employee identity; verify tests cover existing and nonexistent identifiers and confirm the response includes submission details and current status.
- [ ] 2.4 Document the submission and retrieval endpoints, request fields, response statuses, and examples; verify examples match the implemented API contract.

## 3. Manager decisions

- [ ] 3.1 Implement approval and rejection for pending claims using the seeded employee-manager relationship and request actor identity; verify tests cover the correct manager, another employee, the claimant, and unknown actors.
- [ ] 3.2 Make decisions conditional and transactional, recording actor and reason in status history; verify tests cover repeated decisions and concurrent attempts without duplicate transitions.
- [ ] 3.3 Document the decision endpoint, authorization behavior, status conflicts, and identity-header limitation; verify examples and documented error responses match the API.

## 4. End-to-end verification

- [ ] 4.1 Add API-level tests for a complete submission, retrieval, and manager-decision flow, including automatic approval and validation rejection; verify the full test suite passes.
- [ ] 4.2 Verify the service starts with a configured database path and port and that the documented local run and test commands succeed from a clean checkout.
