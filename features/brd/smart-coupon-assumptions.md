# 📋 Smart Coupon System: Assumptions Register

**BRD ID**: BRD-2026-06-10-smart-coupon  
**Document**: Assumptions & Risk Assessment  
**Updated**: 2026-06-10  
**Owner**: Product Management

---

## Critical Assumptions

### 🔴 HIGH RISK Assumptions

#### ASSUMPTION-001: Loyalty Program Data Quality
- **Statement**: "Loyalty program database provides accurate, clean customer purchase history with reliable product category tagging."
- **Risk Level**: 🔴 **HIGH**
- **Validation Required**: ✅ YES - Week 1 Priority
- **Impact if Fails**: 
  - Personalization rules fail to match intended customers
  - Coupons issued to wrong segments
  - Personalization engine produces unexpected results
  - Requires data cleanup before launch (weeks of delay)
- **Validation Method**: 
  - Data quality audit: Sample 10,000 customer records
  - Check for: NULL values, duplicate records, incorrect categorization
  - Validate purchase history completeness (last 90 days coverage >95%)
- **Mitigation**:
  - Hire data quality contractor if needed
  - Implement data validation rules in personalization engine
  - Manual rule testing on real customer data before launch
- **Referenced in Stories**: SC-002 (Personalization Engine), SC-003 (Rule Creation)

---

#### ASSUMPTION-002: POS System API Availability & Compatibility
- **Statement**: "POS system provides real-time coupon validation API with <300ms response time and supports coupon code/ID validation."
- **Risk Level**: 🔴 **HIGH**
- **Validation Required**: ✅ YES - Technical Integration Testing (Week 1-2)
- **Impact if Fails**:
  - Real-time validation cannot be performed at checkout
  - Coupons cannot be redeemed at POS
  - Entire redemption workflow breaks
  - Product launch blocked indefinitely
- **Validation Method**:
  - Request API documentation from POS vendor
  - Set up integration test environment
  - Load testing: Validate 1000 QPS (requests per second)
  - Latency testing: Measure P50/P99 response times
  - Failover testing: Validate fallback if API unavailable
- **Mitigation**:
  - Implement fallback: Manual coupon code entry if API unavailable
  - Implement circuit breaker pattern (fail-open vs fail-closed)
  - Negotiate SLA with POS vendor (99% uptime, <300ms latency)
- **Referenced in Stories**: SC-005 (Checkout Integration), SC-006 (Validation Engine)

---

#### ASSUMPTION-005: Customer Redemption Rate Target (25-35%)
- **Statement**: "Personalized coupons will achieve 25%+ redemption rate (vs. 10-15% for generic coupons), justifying development investment and ROI."
- **Risk Level**: 🔴 **HIGH**
- **Validation Required**: ✅ YES - Pilot Testing (Week 3-4)
- **Impact if Fails**:
  - If redemption rate is 10%, ROI is negative
  - Business case collapses
  - Feature may be cancelled after launch
  - Marketing budget may be reallocated
- **Validation Method**:
  - Pilot with 10,000 customers for 2 weeks
  - Control group (no coupon) vs Test group (personalized coupon)
  - Measure redemption rate, revenue impact, customer engagement
  - Compare to baseline (generic coupon redemption rate)
- **Mitigation**:
  - A/B test different personalization strategies
  - Adjust targeting rules based on pilot results
  - Consider adding incentives if redemption underperforms
  - Extend pilot if results inconclusive
- **Referenced in Stories**: SC-001 (Core Feature), SC-007 (Analytics)

---

### 🟡 MEDIUM RISK Assumptions

#### ASSUMPTION-003: Marketing Teams Can Self-Service Rule Creation
- **Statement**: "Marketing team members (non-technical) can define personalization rules using self-service UI without IT/developer assistance."
- **Risk Level**: 🟡 **MEDIUM**
- **Validation Required**: ✅ YES - UAT with Marketing Team (Week 2)
- **Impact if Fails**:
  - Marketing dependent on IT for rule changes
  - Slows campaign velocity; feedback cycle is weeks not hours
  - High-touch support model scales poorly
  - IT becomes bottleneck
- **Validation Method**:
  - Conduct UAT with marketing team (5 users, 4 hours testing)
  - Test creating 5 different personalization rules
  - Measure: Average time to create rule, error rate, satisfaction score
  - Success threshold: 80%+ complete rule creation without IT help
- **Mitigation**:
  - Provide rule builder UI with templates and examples
  - Implement validation to catch syntax/logic errors
  - Create training documentation and video tutorials
  - Offer 2-hour training session before launch
  - Have IT support team available for complex rules
- **Referenced in Stories**: SC-003 (Rule Creation UI), SC-004 (Rule Management)

---

#### ASSUMPTION-006: Single-Use-Per-Customer Policy Acceptable
- **Statement**: "Business stakeholders accept single-use coupon policy (customers cannot redeem same coupon multiple times)."
- **Risk Level**: 🟡 **MEDIUM**
- **Validation Required**: ✅ YES - Stakeholder Approval (Week 1)
- **Impact if Fails**:
  - If business wants multi-use coupons, architecture must be redesigned
  - Core data model changes required
  - Additional fraud prevention logic needed
  - 2-3 week delay to re-scope and rebuild
- **Validation Method**:
  - Document constraint clearly in BRD
  - Present to VP Marketing, VP Operations
  - Obtain written approval (email sign-off)
- **Mitigation**:
  - Explicitly list this as constraint in project charter
  - If business later wants multi-use, treat as Phase 2 enhancement
  - Implement feature flag to enable multi-use if needed (future-proofing)
- **Referenced in Stories**: SC-005 (Redemption Engine), SC-006 (Fraud Prevention)

---

#### ASSUMPTION-007: 7-Day Coupon Expiration Optimizes for Balance
- **Statement**: "Weekly (7-day) coupon expiration creates optimal balance between redemption urgency and offer fatigue/customer dissatisfaction."
- **Risk Level**: 🟡 **MEDIUM**
- **Validation Required**: ✅ YES - Pilot A/B Testing (Week 4-5)
- **Impact if Fails**:
  - If 7 days too short → redemption rate drops
  - If 7 days too long → inventory bloat, budget pressure
  - May need to adjust after pilot
- **Validation Method**:
  - Pilot test with 3 segments: 5-day, 7-day, 14-day expiration
  - Measure redemption rate % for each group
  - Measure customer satisfaction (survey)
  - Identify optimal window
- **Mitigation**:
  - Implement configurable expiration (admin can adjust)
  - Monitor actual redemption rates and adjust if needed
  - Collect customer feedback on expiration window
- **Referenced in Stories**: SC-005 (Coupon Lifecycle), SC-007 (Analytics)

---

#### ASSUMPTION-008: Loyalty Program Budget Allocated for Coupon Seeding
- **Statement**: "Loyalty program stakeholders have budgeted $X for initial coupon issuance and marketing spend to support launch."
- **Risk Level**: 🟡 **MEDIUM**
- **Validation Required**: ✅ YES - Financial Approval (Week 1)
- **Impact if Fails**:
  - Without budget, cannot issue coupons at launch
  - Pilot cannot execute
  - Launch delayed indefinitely
- **Validation Method**:
  - Obtain written budget approval from Finance / VP Marketing
  - Establish daily/weekly budget caps
  - Get sign-off on pilot budget spend
- **Mitigation**:
  - Secure budget commitment in writing before design phase begins
  - Create detailed budget forecast (pilot vs production)
  - Identify budget owner responsible for cap monitoring
- **Referenced in Stories**: SC-002 (Coupon Creation), SC-003 (Distribution)

---

### 🟢 LOW RISK Assumptions

#### ASSUMPTION-004: Email Delivery Rate 95%+ (Industry Standard)
- **Statement**: "Email platform achieves 95%+ delivery rate to customer inboxes (vs. spam, bounces, blocks)."
- **Risk Level**: 🟢 **LOW**
- **Validation Required**: ❌ NO (Industry Standard)
- **Impact if Fails**:
  - Some customers miss coupon emails
  - Engagement rate may be 3-5% lower than expected
  - Not a launch blocker
- **Mitigation**:
  - Use reputable email provider (SendGrid, Mailchimp, etc.)
  - In-app notifications as backup channel for email failures
  - Monitor email bounce rates weekly
- **Referenced in Stories**: SC-004 (Email Distribution)

---

## Assumption Validation Roadmap

### Week 1: Critical Validations (Must-Pass Gates)
- [ ] **ASSUMPTION-001**: Data quality audit completed
  - Owner: Data Analyst + Product Manager
  - Success: >95% purchase history completeness, <2% NULL values
  - Go/No-Go Gate: IF FAILS → 2 weeks data cleanup → delay launch
  
- [ ] **ASSUMPTION-002**: POS API integration technical feasibility confirmed
  - Owner: Tech Lead + POS Vendor
  - Success: API docs received, test environment access granted, <300ms latency confirmed
  - Go/No-Go Gate: IF FAILS → implement fallback mode → redesign checkout flow
  
- [ ] **ASSUMPTION-008**: Budget approved and caps established
  - Owner: Finance + VP Marketing
  - Success: Written approval, budget allocated, daily spend cap defined
  - Go/No-Go Gate: IF FAILS → cannot launch → defer to next fiscal year

- [ ] **ASSUMPTION-006**: Single-use policy approved by stakeholders
  - Owner: Product Manager
  - Success: Written sign-off from VP Marketing + VP Operations
  - Go/No-Go Gate: IF FAILS → re-scope → 2 week delay

### Week 2: Technical & UAT Validations
- [ ] **ASSUMPTION-003**: Marketing UAT successful (self-service rule creation)
  - Owner: Product Manager + QA
  - Success: 80%+ users can create rules without IT help, <1% errors
  - Mitigation: If <80%, provide training → retest

### Week 3-4: Pilot Phase Validations
- [ ] **ASSUMPTION-005**: Redemption rate reaches 25%+ (vs. 10-15% baseline)
  - Owner: Product Manager + Analytics
  - Success: Pilot 10K customers, measure 25%+ redemption
  - Decision: IF <25% → analyze why → adjust targeting/offers → retest
  
- [ ] **ASSUMPTION-007**: 7-day expiration window optimized
  - Owner: Product Manager + Analytics
  - Success: A/B test 3 expiration windows, identify optimal
  - Action: If 7 days not optimal, update in next batch

---

## Dependency Chain

These assumptions have **interdependencies** - failure of one affects others:

```
ASSUMPTION-001 (Data Quality)
    ↓
    └─→ ASSUMPTION-003 (Rule Creation works)
        └─→ ASSUMPTION-005 (Redemption rate target achieves)

ASSUMPTION-002 (POS API)
    ↓
    └─→ ASSUMPTION-006 (Single-use policy enforceable)
        └─→ ASSUMPTION-005 (Redemption rate target achieves)

ASSUMPTION-008 (Budget Available)
    ↓
    └─→ ASSUMPTION-005 (Pilot can execute)
        └─→ ASSUMPTION-007 (Expiration window tested)
```

**Critical Path**: ASSUMPTION-001 + ASSUMPTION-002 + ASSUMPTION-008 must pass for go/no-go decision on launch.

---

## Tracking & Status

| Assumption | Status | Validation Deadline | Owner | Notes |
|-----------|--------|-------------------|-------|-------|
| ASSUMPTION-001 | ⏳ PENDING | Week 1, Friday | Data Analyst | Data quality audit scheduled |
| ASSUMPTION-002 | ⏳ PENDING | Week 1, Monday | Tech Lead | Waiting for POS API docs |
| ASSUMPTION-003 | ⏳ PENDING | Week 2, Wednesday | Product Manager | UAT planned with 5 marketing users |
| ASSUMPTION-004 | ✅ CONFIRMED | N/A (Industry Std) | N/A | No validation needed |
| ASSUMPTION-005 | ⏳ PENDING | Week 4, Friday | Analytics | Pilot execution in progress |
| ASSUMPTION-006 | ⏳ PENDING | Week 1, Tuesday | Product Manager | Awaiting stakeholder sign-off |
| ASSUMPTION-007 | ⏳ PENDING | Week 5, Friday | Product Manager | A/B test during pilot |
| ASSUMPTION-008 | ⏳ PENDING | Week 1, Friday | Finance | Budget approval pending |

---

## Assumption Failure Response Plan

**IF ASSUMPTION-001 (Data Quality) FAILS**:
- Hire data quality contractor (2 weeks, $15-20K)
- Delay launch by 3 weeks
- Reduce initial customer segment size (100K vs 1M)

**IF ASSUMPTION-002 (POS API) FAILS**:
- Implement fallback: Manual coupon code entry at POS
- Extend checkout time by 30-45 seconds (acceptable trade-off)
- Continue with reduced feature set (email + app only)

**IF ASSUMPTION-005 (Redemption Rate) FAILS**:
- IF 10-20% rate achieved (partial success):
  - Investigate: Wrong customer segment? Offers unattractive?
  - A/B test better offers / targeting rules
  - Extend pilot 2 weeks to retest
  - ROI still positive? YES → proceed to production
  
- IF <10% rate (failure):
  - Feature ROI negative
  - Reassess business case
  - Consider deferring to Phase 2 with ML-driven personalization
  - Pivot to simpler offering (generic coupons with automation)

**IF ASSUMPTION-008 (Budget) FAILS**:
- Reduce launch scope (100K customers vs 1M)
- Deferlaunch 1 quarter until budget available
- Operate in low-volume "beta" mode (internal testing only)

---

*For questions about assumptions or validation status, contact the Product Manager.*
