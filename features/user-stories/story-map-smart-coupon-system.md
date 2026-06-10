# Story Map - Smart Coupon System

## Overview
- **BRD ID**: BRD-2026-06-10-smart-coupon-system
- **Feature**: Smart Coupon System for Loyalty Program
- **Total Story Count**: 11
- **Total Story Points**: 70
- **No Circular Dependencies**: Confirmed during decomposition review.
- **INVEST Health**: All stories pass INVEST validation and are sized at 5 or 8 points.

## Stories by Priority
### MUST (10 stories / 65 pts)
- SC-001 - System Determines Personalized Coupon Eligibility (8 pts)
- SC-002 - Marketer Manages Smart Coupon Content (5 pts)
- SC-003 - System Orchestrates Multi-Channel Coupon Delivery (8 pts)
- SC-004 - Member Receives Smart Coupons by Email (5 pts)
- SC-005 - Shopper Redeems Eligible Coupons at Checkout (8 pts)
- SC-006 - Marketer Adds Smart Coupons to Targeted Campaigns (5 pts)
- SC-007 - Member Discovers Active Coupons In-App (5 pts)
- SC-008 - Member Redeems Coupons With Minimal Effort (8 pts)
- SC-009 - System Keeps Offer Details Consistent Across Channels (5 pts)
- SC-010 - Stakeholder Reviews Smart Coupon Performance (8 pts)

### SHOULD (1 stories / 5 pts)
- SC-011 - Admin Configures Smart Coupon Rules and Channels (5 pts)

### COULD (0 stories / 0 pts)
- None for v1.0

## Dependency Graph
- SC-001 <- [None] -> [SC-003, SC-005, SC-008, SC-010]
- SC-002 <- [None] -> [SC-003, SC-008, SC-009]
- SC-003 <- [SC-001, SC-002] -> [SC-004, SC-006, SC-007, SC-010]
- SC-004 <- [SC-003, SC-008] -> [None]
- SC-005 <- [SC-001, SC-008] -> [None]
- SC-006 <- [SC-003, SC-011] -> [None]
- SC-007 <- [SC-003, SC-008] -> [None]
- SC-008 <- [SC-001, SC-002] -> [SC-004, SC-005, SC-007]
- SC-009 <- [SC-002, SC-011] -> [None]
- SC-010 <- [SC-003, SC-011] -> [None]
- SC-011 <- [None] -> [SC-006, SC-009, SC-010]

## Story Matrix
| Story ID | Slug | Title | Points | Priority | Blocked By | Blocks | BRD Requirements |
|---|---|---|---:|---|---|---|---|
| SC-001 | system-determines-personalized-coupon-eligibility | System Determines Personalized Coupon Eligibility | 8 | MUST | None | SC-003, SC-005, SC-008, SC-010 | FR-001 |
| SC-002 | marketer-manages-smart-coupon-content | Marketer Manages Smart Coupon Content | 5 | MUST | None | SC-003, SC-008, SC-009 | FR-002 |
| SC-003 | system-orchestrates-multi-channel-coupon-delivery | System Orchestrates Multi-Channel Coupon Delivery | 8 | MUST | SC-001, SC-002 | SC-004, SC-006, SC-007, SC-010 | FR-003 |
| SC-004 | member-receives-smart-coupons-by-email | Member Receives Smart Coupons by Email | 5 | MUST | SC-003, SC-008 | None | FR-004, FR-008 |
| SC-005 | shopper-redeems-eligible-coupons-at-checkout | Shopper Redeems Eligible Coupons at Checkout | 8 | MUST | SC-001, SC-008 | None | FR-005, FR-008 |
| SC-006 | marketer-adds-smart-coupons-to-targeted-campaigns | Marketer Adds Smart Coupons to Targeted Campaigns | 5 | MUST | SC-003, SC-011 | None | FR-006, FR-003 |
| SC-007 | member-discovers-active-coupons-in-app | Member Discovers Active Coupons In-App | 5 | MUST | SC-003, SC-008 | None | FR-007, FR-008 |
| SC-008 | member-redeems-coupons-with-minimal-effort | Member Redeems Coupons With Minimal Effort | 8 | MUST | SC-001, SC-002 | SC-004, SC-005, SC-007 | FR-008 |
| SC-009 | system-keeps-offer-details-consistent-across-channels | System Keeps Offer Details Consistent Across Channels | 5 | MUST | SC-002, SC-011 | None | FR-009, FR-002 |
| SC-010 | stakeholder-reviews-smart-coupon-performance | Stakeholder Reviews Smart Coupon Performance | 8 | MUST | SC-003, SC-011 | None | FR-010 |
| SC-011 | admin-configures-smart-coupon-rules-and-channels | Admin Configures Smart Coupon Rules and Channels | 5 | SHOULD | None | SC-006, SC-009, SC-010 | FR-002, FR-003, FR-009, FR-010 |

## Assumption Coverage
| Assumption | Stories |
|---|---|
| ASSUMPTION-001 | SC-001 |
| ASSUMPTION-002 | SC-002, SC-003, SC-004, SC-005, SC-006, SC-007, SC-009, SC-011 |
| ASSUMPTION-003 | SC-001, SC-003, SC-005, SC-008 |
| ASSUMPTION-004 | SC-004, SC-005, SC-007, SC-008 |
| ASSUMPTION-005 | SC-010 |
| ASSUMPTION-006 | SC-001, SC-010 |
| ASSUMPTION-007 | SC-002, SC-003, SC-006, SC-008, SC-009, SC-010, SC-011 |
| ASSUMPTION-008 | SC-004, SC-006, SC-007, SC-011 |

## Definition of Done
- [ ] Story implemented per acceptance criteria
- [ ] Code reviewed and approved
- [ ] Unit tests written and passing
- [ ] Integration tests passing
- [ ] Documentation updated
- [ ] QA sign-off received
- [ ] No blocking bugs open

## Story Decomposition Approval
- ✅ All stories pass INVEST validation.
- ✅ All MoSCoW classifications are justified.
- ✅ All story points include a breakdown justification.
- ✅ Assumption dependencies are clearly linked.
- ✅ All dependencies are mapped with no circular references.
- ✅ Definition of Done is included on every story.
- ✅ Traceability IDs are assigned and consistent.
- ✅ No story exceeds 13 story points.
- ✅ Every story is independently valuable.
- **Ready for GitHub Upload?** Yes - artifacts are prepared for downstream test-case and issue generation.

