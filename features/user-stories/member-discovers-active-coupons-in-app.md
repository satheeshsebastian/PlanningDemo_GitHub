# Member Discovers Active Coupons In-App

## Story ID & Overview
- **ID**: SC-007
- **Traceability ID**: BRD-2026-06-10-smart-coupon-system-STORY-007
- **Slug**: member-discovers-active-coupons-in-app
- **Feature**: Smart Coupon System for Loyalty Program
- **Status**: NEW

## User Story Statement
As a loyalty member, I want to see my active personalized coupons in the in-app loyalty experience, so that I can find and use them without searching across channels.

## Story Scope
- **In Scope**:
  - Display active smart coupons in the in-app loyalty experience.
  - Label coupon states such as active, expiring, redeemed, or unavailable.
  - Allow the member to open the supported redemption path from the coupon view.
- **Out of Scope**:
  - Push notifications or proactive alerts.
  - Advanced search, saved lists, or favorites.
  - Native mobile app work beyond the existing in-app loyalty surface.

## Acceptance Criteria (BDD Format)
### AC-1: Active coupon visibility
Given a loyalty member has eligible coupons, When the member opens the in-app loyalty experience, Then active personalized coupons are visible without searching other channels.

### AC-2: Clear coupon labeling
Given the member views a coupon card or detail, When the coupon is displayed, Then the system clearly labels status, value, validity, and key redemption information.

### AC-3: Supported action path
Given the member wants to use an active coupon, When they select the coupon action, Then the experience routes them into the supported redemption flow for that coupon.

## Technical Requirements
- Expose an authenticated coupon feed for the in-app loyalty experience.
- Support coupon state labels and detail views based on redemption status.
- Implement trackable action links or buttons for supported redemption paths.
- Capture in-app impression and engagement events for reporting.

## Dependencies
- **Blocked By**: SC-003, SC-008
- **Blocks**: None
- **Related**: SC-010

## Story Points: 5

### Breakdown
- Coupon feed and list rendering: 2 pts
- Status labels and actions: 2 pts
- Testing: 1 pts
- **Justification**: The story is confined to one discovery surface and relies on reusable platform services, keeping the estimate moderate.

## MoSCoW Classification
**Priority**: MUST
**Rationale**: In-App is a committed launch channel and a primary loyalty engagement surface.

## Assumptions & Dependencies
- **Depends on**: ASSUMPTION-002, ASSUMPTION-004, ASSUMPTION-008
- **Risk**: If the in-app surface cannot accommodate clear labels and action states, additional UX work will be needed before release.
- **Re-estimate**: Yes

## INVEST Validation
- ✅ Independent: It provides a standalone member discovery journey once shared services are available.
- ✅ Negotiable: Card layout and default sort order can be refined during design review.
- ✅ Valuable: It gives members a dependable place to find active offers, increasing engagement.
- ✅ Estimable: The UI behavior and supporting data needs are explicit.
- ✅ Small: At 5 points, the scope remains sprint-friendly.
- ✅ Testable: Visibility, labels, and action routing can be verified end to end.

## Definition of Done
- [ ] Story implemented per acceptance criteria
- [ ] Code reviewed and approved
- [ ] Unit tests written and passing
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] QA sign-off received
- [ ] No blocking bugs open

## Traceability
- **BRD Requirement**: FR-007, FR-008
- **Acceptance Criteria**: AC-005, AC-006, AC-008
- **Assumption Links**: ASSUMPTION-002, ASSUMPTION-004, ASSUMPTION-008
- **Test Cases**: FTC-SC-007-01 to FTC-SC-007-03

