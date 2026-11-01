# Proposal

## Why

Employees have no structured way to claim business expenses, and the approval rules are applied by individual judgment, so the expense policy is enforced inconsistently and decisions cannot be traced. Finance wants the policy enforced by a system first, so that future policy updates become explicit, reviewable changes instead of informal agreements.

## What Changes

- Employees can submit an expense claim with an amount in EUR, a description, and whether a receipt is attached.
- Claims are validated on submission: the amount must be greater than zero, and a receipt is required for amounts above EUR 25. Invalid claims are rejected with a reason.
- Valid claims of EUR 50 or less are approved automatically.
- Valid claims above EUR 50 wait for approval by the submitting employee's manager.
- Managers can approve or reject a pending claim. Only the employee's own manager may decide, and nobody can approve or reject their own claim.
- Anyone can retrieve a claim and see its current status (approved, pending manager approval, or rejected).

### Out of scope

- Authentication and SSO: the acting employee's identity is supplied with each request, and employees and their managers come from a seeded directory.
- Reimbursement and payments.
- Currencies other than EUR.
- Receipt file upload: a receipt is recorded as attached or not attached.
- A finance approval step and any other changes to the approval policy. These are handled in later changes.
- A user interface: this change provides an HTTP API only.

## Capabilities

### New Capabilities

- `expense-claim-submission`: submitting expense claims, validation rules (amount, receipt), and retrieving a claim with its current status.
- `expense-claim-approval`: how a valid claim is routed (automatic approval threshold, manager approval) and who is allowed to approve or reject it.

### Modified Capabilities

None. This is the first change in the project, and there are no existing specs.

## Impact

- New HTTP API for submitting, retrieving, approving, and rejecting expense claims. Technology choices are recorded in `design.md`.
- New data: claims with their status history, and a seeded employee directory that holds manager relationships.
- No existing code, APIs, or external systems are affected. This is a greenfield project.
