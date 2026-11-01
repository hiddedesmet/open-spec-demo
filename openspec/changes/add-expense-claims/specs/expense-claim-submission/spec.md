# Spec Delta

## Purpose

Defines how employees submit expense claims, how submissions are validated, and how anyone retrieves a claim and its current status.

## ADDED Requirements

### Requirement: Employee can submit an expense claim
The system SHALL accept a submitting employee, an amount in EUR, a description, and whether a receipt is attached, and SHALL create a claim for that employee when the required submission fields are supplied.

#### Scenario: Submit a claim with required fields
- **WHEN** an employee submits an amount in EUR, a description, and a receipt-attached value
- **THEN** the system creates a claim associated with that employee and returns its identifier and current status

### Requirement: Submission validation records rejected claims
The system SHALL reject claims whose amount is not greater than zero or whose amount exceeds EUR 25 without an attached receipt, and SHALL record each rejected claim with a reason and a retrievable identifier.

#### Scenario: Reject a non-positive amount
- **WHEN** an employee submits a claim with an amount of zero or less
- **THEN** the system records the claim as rejected and provides a reason stating that the amount must be greater than zero

#### Scenario: Reject an amount above EUR 25 without a receipt
- **WHEN** an employee submits a claim with an amount greater than EUR 25 and no receipt attached
- **THEN** the system records the claim as rejected and provides a reason stating that a receipt is required for amounts above EUR 25

#### Scenario: Accept an amount of exactly EUR 25 without a receipt
- **WHEN** an employee submits a claim for exactly EUR 25 without a receipt attached
- **THEN** the system does not reject the claim for missing receipt

### Requirement: Anyone can retrieve a claim
The system SHALL allow a caller to retrieve an existing claim by its identifier and return its current status.

#### Scenario: Retrieve an existing claim
- **WHEN** a caller requests an existing claim by its identifier
- **THEN** the system returns the claim's current status and its submission details

#### Scenario: Retrieve a nonexistent claim
- **WHEN** a caller requests an identifier that does not match an existing claim
- **THEN** the system reports that the claim was not found
