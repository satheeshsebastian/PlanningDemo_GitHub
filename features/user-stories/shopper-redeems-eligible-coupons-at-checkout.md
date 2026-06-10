# Shopper Redeems Eligible Coupons at Checkout

## Story ID & Overview
- **ID**: SC-005
- **Traceability ID**: BRD-2026-06-10-smart-coupon-system-STORY-005
- **Slug**: shopper-redeems-eligible-coupons-at-checkout
- **Feature**: Smart Coupon System for Loyalty Program
- **Status**: NEW

## User Story Statement
As a shopper in checkout, I want my eligible coupon surfaced and easy to apply during purchase, so that I can save money without leaving the checkout flow.

## Story Scope
- **In Scope**:
  - Display eligible coupons in cart or checkout when the shopper qualifies.
  - Allow one-step apply or auto-apply behavior where business rules permit.
  - Show coupon status, conditions, and validity before confirmation.
- **Out of Scope**:
  - Offline or store POS redemption.
  - Post-purchase adjustments or retroactive coupon application.
  - Unsupported payment-channel experiences.

## Acceptance Criteria (BDD Format)
### AC-1: Checkout surfacing
Given a loyalty member qualifies for a coupon during checkout, When the checkout experience renders, Then the eligible coupon is displayed in the purchase flow.

### AC-2: Low-friction application
Given a displayed coupon is valid for the current cart, When the shopper chooses to use it, Then the coupon is applied with minimal manual effort and the totals refresh.

### AC-3: Clear invalid state
Given a coupon is expired or conditions are not met, When the shopper views the coupon in checkout, Then the system clearly explains why it cannot be redeemed.

## Technical Requirements
- Perform real-time or near-real-time eligibility retrieval for the active checkout session.
- Support apply, remove, and recalculation behavior within the checkout service.
- Display coupon state messaging for active, invalid, expired, and redeemed outcomes.
- Capture checkout impression and redemption events for analytics.

## Dependencies
- **Blocked By**: SC-001, SC-008
- **Blocks**: None
- **Related**: SC-010

## Story Points: 8

### Breakdown
- Checkout eligibility and display integration: 3 pts
- Apply or auto-apply workflow: 3 pts
- Testing and edge-case validation: 2 pts
- **Justification**: Checkout redemption touches transactional logic and requires careful handling of state changes, making it one of the higher-risk stories.

## MoSCoW Classification
**Priority**: MUST
**Rationale**: Checkout is the highest-intent moment in scope and is essential to conversion and redemption outcomes.

## Assumptions & Dependencies
- **Depends on**: ASSUMPTION-002, ASSUMPTION-003, ASSUMPTION-004
- **Risk**: If checkout cannot support low-friction application or dependable eligibility lookup, the launch experience will fall short of the core value proposition.
- **Re-estimate**: Yes

## INVEST Validation
- ✅ Independent: The checkout journey is separately deployable once eligibility and redemption foundations are available.
- ✅ Negotiable: Apply button behavior versus auto-apply can be finalized during checkout design.
- ✅ Valuable: It creates immediate customer value at the point of purchase.
- ✅ Estimable: The flow, channel, and failure states are well understood.
- ✅ Small: At 8 points, it is significant but still fits within a sprint for a checkout squad.
- ✅ Testable: Valid, invalid, and applied states are measurable and repeatable in testing.

## Definition of Done
- [ ] Story implemented per acceptance criteria
- [ ] Code reviewed and approved
- [ ] Unit tests written and passing
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] QA sign-off received
- [ ] No blocking bugs open

## Traceability
- **BRD Requirement**: FR-005, FR-008
- **Acceptance Criteria**: AC-003, AC-006, AC-008
- **Assumption Links**: ASSUMPTION-002, ASSUMPTION-003, ASSUMPTION-004
- **Test Cases**: FTC-SC-005-01 to FTC-SC-005-03

