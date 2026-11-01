# Spec Delta

## Purpose

Defines how valid expense claims are routed for approval and which manager may approve or reject a pending claim.

## ADDED Requirements

### Requirement: Claims at or below EUR 50 are approved automatically
The system SHALL automatically approve a valid claim whose amount is EUR 50 or less.

#### Scenario: Automatically approve a claim of EUR 50
- **WHEN** a valid claim for exactly EUR 50 is submitted
- **THEN** the claim is approved without manager action

#### Scenario: Automatically approve a claim below EUR 50
- **WHEN** a valid claim for less than EUR 50 is submitted
- **THEN** the claim is approved without manager action

### Requirement: Claims above EUR 50 wait for manager approval
The system SHALL place a valid claim whose amount is greater than EUR 50 into pending manager approval.

#### Scenario: Route a claim above EUR 50
- **WHEN** a valid claim for more than EUR 50 is submitted
- **THEN** the claim has pending manager approval status

### Requirement: Only the submitting employee's manager may decide a pending claim
The system SHALL allow only the submitting employee's manager to approve or reject a pending claim, and SHALL prohibit the claimant from deciding their own claim.

#### Scenario: The employee's manager approves a pending claim
- **GIVEN** a claim is pending manager approval
- **WHEN** the submitting employee's manager approves it
- **THEN** the claim status becomes approved

#### Scenario: The employee's manager rejects a pending claim
- **GIVEN** a claim is pending manager approval
- **WHEN** the submitting employee's manager rejects it
- **THEN** the claim status becomes rejected

#### Scenario: Another employee attempts to decide a pending claim
- **GIVEN** a claim is pending manager approval
- **WHEN** someone other than the submitting employee's manager attempts to approve or reject it
- **THEN** the system denies the decision and leaves the claim pending

#### Scenario: The claimant attempts to decide their own claim
- **GIVEN** a claim is pending manager approval
- **WHEN** the submitting employee attempts to approve or reject their own claim
- **THEN** the system denies the decision and leaves the claim pending

### Requirement: A decision can be made only while a claim is pending manager approval
The system SHALL reject an approval or rejection attempt for a claim that is not pending manager approval and SHALL leave its status unchanged.

#### Scenario: Decide an already resolved claim
- **GIVEN** a claim is approved or rejected
- **WHEN** a manager attempts to approve or reject it again
- **THEN** the system denies the decision and leaves the claim status unchanged
