# User Story Map - Smart Coupon System

**Document ID:** `BRD-2026-06-10-smart-coupon-system`  
**Created:** 2026-06-10  
**Total Stories:** 10  
**Total Points:** 73  

---

## Story Inventory Summary

| Epic | Stories | Points | MoSCoW |
|------|---------|--------|--------|
| **Coupon Generation** | US-001, US-002, US-003 | 24 | MUST |
| **Eligibility & Validation** | US-004, US-005, US-006, US-007 | 23 | MUST |
| **Distribution** | US-008, US-009, US-010 | 18 | MUST |
| **Consent & Compliance** | US-011 | 3 | MUST |
| **Analytics & Reporting** | US-012, US-013 | 13 | MUST |

---

## Story Dependency Graph

```
GENERATION (Foundation)
├─ US-001: Generate Behavior-Based Coupons [8pts]
├─ US-002: Generate Preference-Based Coupons [8pts]
└─ US-003: Support Event-Based Triggers [8pts]
       ↓ (depend on ASSUMPTION-001)

VALIDATION (Gating)
├─ US-004: Validate Coupon Eligibility [5pts]
├─ US-005: Enforce Single-Use Constraint [6pts]
├─ US-006: Support Coupon Expiration [4pts]
└─ US-007: Support Category Exclusions [8pts]
       ↓

DISTRIBUTION (Delivery)
├─ US-008: Email Distribution [5pts]
├─ US-009: Checkout Distribution [8pts] ⚠️ ASSUMPTION-002 BLOCKER
└─ US-010: Campaign-Based Distribution [5pts]
       ↓

COMPLIANCE (Legal Gate)
└─ US-011: Customer Consent Management [3pts]
       ↓

ANALYTICS (Measurement)
├─ US-012: Track Redemption Events [5pts]
└─ US-013: Calculate Redemption Metrics [8pts]
```

---

## Story Cards (Quick Reference)

| # | Story ID | Slug | Title | Pts | MoSCoW |
|---|----------|------|-------|-----|--------|
| 1 | US-001 | system-generate-behavior-coupons | Generate Behavior-Based Coupons | 8 | MUST |
| 2 | US-002 | system-generate-preference-coupons | Generate Preference-Based Coupons | 8 | MUST |
| 3 | US-003 | system-support-event-triggers | Support Event-Based Coupon Triggers | 8 | MUST |
| 4 | US-004 | system-validate-coupon-eligibility | Validate Coupon Eligibility | 5 | MUST |
| 5 | US-005 | system-enforce-single-use | Enforce Single-Use Constraint | 6 | MUST |
| 6 | US-006 | system-support-expiration | Support Coupon Expiration | 4 | MUST |
| 7 | US-007 | system-validate-exclusions | Support Category Exclusions | 8 | MUST |
| 8 | US-008 | system-email-distribution | Email Distribution | 5 | MUST |
| 9 | US-009 | system-checkout-distribution | Checkout Distribution | 8 | MUST |
| 10 | US-010 | system-campaign-distribution | Campaign-Based Distribution | 5 | MUST |
| 11 | US-011 | customer-consent-management | Customer Consent Management | 3 | MUST |
| 12 | US-012 | system-track-redemptions | Track Redemption Events | 5 | MUST |
| 13 | US-013 | system-redemption-metrics | Calculate Redemption Metrics | 8 | MUST |

---

## Sprint Planning (Recommended 3 Sprints)

### Sprint 1: Generation & Eligibility Foundations (Week 1-4)
**Effort:** 28 pts  
**Stories:** US-001, US-002, US-003, US-004, US-006

**Milestone:** Coupon generation & basic eligibility working

```
Prerequisites:
✓ ASSUMPTION-001: Customer behavior data validated
✓ ASSUMPTION-005: Loyalty tier data accessible
```

### Sprint 2: Validation & Distribution (Week 5-8)
**Effort:** 26 pts  
**Stories:** US-005, US-007, US-008, US-009, US-010

**Milestone:** Email & checkout distribution live; single-use enforcement

```
Prerequisites:
✓ ASSUMPTION-002: Checkout API integration confirmed
✓ ASSUMPTION-004: Email service tested
```

### Sprint 3: Consent & Analytics (Week 9-12)
**Effort:** 19 pts  
**Stories:** US-011, US-012, US-013

**Milestone:** Full compliance & analytics dashboard

```
Prerequisites:
✓ ASSUMPTION-006: Consent framework reviewed
```

---

## INVEST Health Check Summary

**All 13 stories pass INVEST validation:**
- ✅ Independent (max 2-3 dependencies per story)
- ✅ Negotiable (configurable thresholds, discounts)
- ✅ Valuable (each provides business value)
- ✅ Estimable (all sized 3-8 pts)
- ✅ Small (all < 13 pts)
- ✅ Testable (clear AC in BDD format)

**Overall INVEST Compliance: 100%**

---

## Assumption Traceability

| Assumption | Risk | Stories | Critical? |
|-----------|------|---------|-----------|
| ASSUMPTION-001 | MEDIUM | US-001, US-002, US-003 | No |
| ASSUMPTION-002 | **HIGH** | US-009 | **YES** |
| ASSUMPTION-003 | MEDIUM | US-013 | No |
| ASSUMPTION-004 | LOW | US-008 | No |
| ASSUMPTION-005 | LOW | US-004, US-007 | No |
| ASSUMPTION-006 | LOW | US-011 | No |

---

## Definition of Done (All Stories)

- [ ] Story implemented per acceptance criteria
- [ ] Code reviewed and approved (≥80% coverage for unit tests)
- [ ] Integration tests passing
- [ ] Documentation updated (API docs, algorithms)
- [ ] QA sign-off (all AC verified)
- [ ] No blocking bugs open
- [ ] Performance benchmarks met (if applicable)

---

## Key Metrics

```
Total Story Points:     73
Estimated Duration:     12 weeks (3 sprints)
Team Size:             5-7 engineers
Risk Level:            MEDIUM (1 HIGH-risk assumption)
Success Metric:        35% redemption rate
Compliance:            GDPR-ready (consent built-in)
```

---

**Status:** ✅ READY FOR APPROVAL

