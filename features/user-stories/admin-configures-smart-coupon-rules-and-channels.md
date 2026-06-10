# Admin Configures Smart Coupon Rules and Channels

## Story ID & Overview
- **ID**: SC-011
- **Traceability ID**: BRD-2026-06-10-smart-coupon-system-STORY-011
- **Slug**: admin-configures-smart-coupon-rules-and-channels
- **Feature**: Smart Coupon System for Loyalty Program
- **Status**: NEW

## User Story Statement
As a product admin, I want to configure smart coupon rules, channel enablement, and rollout guardrails, so that the feature can be governed without code changes.

## Story Scope
- **In Scope**:
  - Enable or disable supported channels for smart coupon rollout.
  - Manage rule parameters such as channel availability windows and rollout guardrails.
  - Preserve v1.0 scope control by preventing out-of-scope channels from being activated.
- **Out of Scope**:
  - A full role-based access control redesign.
  - CI/CD deployment controls.
  - Governance for future out-of-scope channels beyond v1.0 review needs.

## Acceptance Criteria (BDD Format)
### AC-1: Channel enablement control
Given an admin manages Smart Coupon settings, When they update channel enablement, Then the system allows configuration for Email, Checkout, Campaigns, and In-App only.

### AC-2: Rule and guardrail updates
Given an admin needs to adjust rollout settings, When they update approved parameters, Then the new settings are stored with an audit trail and take effect without code deployment.

### AC-3: Scope protection
Given an admin attempts to activate SMS, Mobile App, or Push Notification support, When they save the configuration, Then the system blocks the change because those channels are out of scope for v1.0.

## Technical Requirements
- Implement a configuration store or UI for supported-channel enablement and rollout parameters.
- Reuse existing admin identity and access controls where available.
- Record who changed a rule, what changed, and when it changed for audit purposes.
- Enforce hard validation to prevent activation of out-of-scope channels in v1.0.

## Dependencies
- **Blocked By**: None
- **Blocks**: SC-006, SC-009, SC-010
- **Related**: SC-001, SC-002, SC-003

## Story Points: 5

### Breakdown
- Configuration model and validation: 2 pts
- Audit history and guardrails: 2 pts
- Testing: 1 pts
- **Justification**: The story is configuration-focused and avoids large-scale admin-platform changes, keeping it moderate in size.

## MoSCoW Classification
**Priority**: SHOULD
**Rationale**: The platform can technically launch with manual governance, but configuration controls reduce operational risk and protect scope during rollout.

## Assumptions & Dependencies
- **Depends on**: ASSUMPTION-002, ASSUMPTION-007, ASSUMPTION-008
- **Risk**: If governance ownership is unclear, teams may rely on manual changes and create inconsistent rollout behavior.
- **Re-estimate**: Yes

## INVEST Validation
- ✅ Independent: It provides operational control without requiring channel-specific delivery features to be built first.
- ✅ Negotiable: The exact settings exposed to admins can be tuned during implementation.
- ✅ Valuable: It reduces release risk and enforces agreed scope boundaries.
- ✅ Estimable: The supported channels and required guardrails are clear.
- ✅ Small: At 5 points, the story fits within a sprint.
- ✅ Testable: Supported and unsupported channel configuration scenarios are easy to validate.

## Definition of Done
- [ ] Story implemented per acceptance criteria
- [ ] Code reviewed and approved
- [ ] Unit tests written and passing
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] QA sign-off received
- [ ] No blocking bugs open

## Traceability
- **BRD Requirement**: FR-002, FR-003, FR-009, FR-010
- **Acceptance Criteria**: AC-007, AC-009, AC-010
- **Assumption Links**: ASSUMPTION-002, ASSUMPTION-007, ASSUMPTION-008
- **Test Cases**: FTC-SC-011-01 to FTC-SC-011-03

