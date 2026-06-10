# Assumptions & Risks Register
## Smart Checkout Assistant (BRD-2026-06-10-smart-checkout)

**Date**: 2026-06-10  
**Document Version**: 1.0  
**Related BRD**: `smart-checkout-v1.0.md`

---

## Executive Summary

This document captures all explicit and implicit assumptions underlying the Smart Checkout project. Each assumption is classified by risk level and includes mitigation strategies. **All assumptions must be validated before project kickoff.**

---

## Explicit Assumptions

### ASSUMPTION-001: POS API Availability
**Statement**: The existing POS system has API endpoints for inventory lookup and transaction recording that can be accessed by the Smart Checkout app.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | MEDIUM |
| **Validation Required** | YES - Week 1 |
| **If Fails, Impact** | Development delay (2-3 weeks); may require custom POS adapter or manual sync fallback |
| **Validation Method** | Technical audit of POS system; API documentation review; proof-of-concept integration test |
| **Referenced in Stories** | US-04 (POS Integration), US-09 (Inventory Sync) |
| **Owner** | Technical Lead |

---

### ASSUMPTION-002: In-Store WiFi Bandwidth
**Statement**: All 5 pilot stores have in-store WiFi with minimum bandwidth of 10 Mbps.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | MEDIUM |
| **Validation Required** | YES - Before pilot launch |
| **If Fails, Impact** | System performance degradation; checkout times may exceed 2-minute SLA; network upgrade required ($3K-5K per store) |
| **Validation Method** | WiFi bandwidth survey at each pilot store; speedtest.net evaluation |
| **Referenced in Stories** | US-02 (App Performance), NFR-7 (Network Resilience) |
| **Owner** | Store Operations Manager |
| **Contingency** | Upgrade WiFi or implement offline queueing with retry logic |

---

### ASSUMPTION-003: Stripe Merchant Account
**Statement**: Stripe merchant account is already established or will be set up before app launch.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Validation Required** | YES - Week 1 |
| **If Fails, Impact** | Development delay (1 week); payment processing cannot proceed |
| **Validation Method** | Stripe dashboard access verification; merchant account creation (if needed) |
| **Referenced in Stories** | US-06 (Payment Processing), FR-5.1 |
| **Owner** | Finance + Technical Lead |
| **Timeline** | Stripe setup typically takes 1-2 business days |

---

### ASSUMPTION-004: Exit Gate QR Scanning Capability
**Statement**: Store exit gates/barriers can be retrofitted with QR code scanning capability (automated scanner or manual staff scan point).

| Attribute | Value |
|-----------|-------|
| **Risk Level** | HIGH - **CRITICAL ASSUMPTION** |
| **Validation Required** | YES - Week 1 |
| **If Fails, Impact** | Exit validation feature cannot be deployed; entire fraud prevention strategy fails; BRD must be revised |
| **Validation Method** | Physical site survey of all 5 pilot stores; consult with store facilities team on gate specifications |
| **Referenced in Stories** | US-07 (Exit Validation), FR-2.1 to FR-2.5 |
| **Owner** | Store Operations Manager |
| **Contingency** | If QR scanning not possible, pivot to: (a) manual staff verification at exit, (b) remove exit validation for v1.0 (post-pilot enhancement) |
| **Decision Required** | Must determine feasibility before kickoff; impacts project scope |

---

### ASSUMPTION-005: Customer Smartphone Ownership
**Statement**: Target customers own smartphones (iOS 14+ or Android 10+) with camera capability.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Validation Required** | NO (industry data available) |
| **If Fails, Impact** | Low-tech customers cannot use system; adoption targets will be lower than projected |
| **Validation Method** | Market research: 90%+ smartphone ownership in US; 85%+ have iOS 14+ or Android 10+ |
| **Referenced in Stories** | US-01 (App Launch), NFR-6 (Device Compatibility) |
| **Owner** | Product Manager |
| **Note** | Acceptable market risk; app will have fallback for older devices (web-based checkout alternative) |

---

### ASSUMPTION-006: Store Manager Availability
**Statement**: Store managers are available during 6am-11pm operating hours to handle transaction overrides and escalations.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | MEDIUM |
| **Validation Required** | YES - Store staffing plan |
| **If Fails, Impact** | Escalations and fraud alerts cannot be handled; system effectiveness compromised; customer wait times increase |
| **Validation Method** | Confirm store manager shift schedules; schedule coverage for all operating hours |
| **Referenced in Stories** | US-08 (Store Manager Dashboard), FR-3.2 |
| **Owner** | Store Manager (each pilot store) |
| **Mitigation** | Identify backup manager per store; train assistant manager as secondary |

---

### ASSUMPTION-007: Stripe Payment Latency
**Statement**: Payment processing via Stripe (auth + capture) will complete in <1 second under normal conditions.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Validation Required** | YES - Integration testing |
| **If Fails, Impact** | Checkout time SLA (<2 min) may not be achievable; impacts user experience |
| **Validation Method** | Stripe SLA documentation (typically 99.99% uptime, <1 sec auth); load testing with concurrent transactions |
| **Referenced in Stories** | US-06 (Payment Processing), NFR-1 (Checkout Time) |
| **Owner** | Technical Lead |
| **Stripe SLA** | Stripe guarantees 99.99% uptime; acceptable risk |

---

### ASSUMPTION-008: Exit QR Code Expiration
**Statement**: Exit QR codes are valid for 30 minutes after checkout. Codes expire and cannot be reused.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Validation Required** | YES - Business approval |
| **If Fails, Impact** | If expiration not acceptable, must implement: (a) time-based re-generation, (b) different QR per scan attempt (replay attack prevention) |
| **Validation Method** | Product Owner approval of 30-minute window |
| **Referenced in Stories** | US-07 (Exit Validation), FR-2.1 |
| **Owner** | Product Owner |
| **Rationale** | 30 minutes balances fraud prevention (prevents old QR reuse) with customer experience (reasonable buffer for delays) |

---

## Implicit Assumptions (Derived from Context)

### ASSUMPTION-009: Transaction Type Separation
**Statement**: "Smart Checkout" transactions are recorded as a separate transaction type in the POS system (e.g., "MOBILE_CHECKOUT") and are distinguishable from traditional register checkouts.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Implied By** | Requirement: "recorded in POS system as separate transaction type" (FR-4.4) |
| **Validation Required** | YES - POS configuration |
| **If Fails, Impact** | Analytics/reporting cannot isolate Smart Checkout performance; pilot metrics unreliable |
| **Owner** | Technical Lead |

---

### ASSUMPTION-010: Failed Scans Don't Block Checkout
**Statement**: Customers experiencing failed scans can request store associate assistance and still proceed with checkout (partial or complete cart).

| Attribute | Value |
|-----------|-------|
| **Risk Level** | LOW |
| **Implied By** | Brainstorming: "escalate to store associate" (not "abort checkout") |
| **Validation Required** | NO (captured in requirement FR-1.4) |
| **If Fails, Impact** | High customer frustration; abandoned carts |
| **Owner** | Product Owner |

---

### ASSUMPTION-011: Store Manager Override Authority
**Statement**: Store Managers have authority to override fraud rules (override transaction limits, velocity checks) without corporate approval.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | MEDIUM |
| **Implied By** | Brainstorming: "Store Manager can override/cancel" (FR-3.2) |
| **Validation Required** | YES - Corporate policy review |
| **If Fails, Impact** | Managers cannot respond to edge cases; customer frustration increases |
| **Owner** | Product Owner + Store Operations |
| **Potential Issue** | Risk of override abuse (fraud); may require audit trail + limits on override frequency |

---

### ASSUMPTION-012: Exit Validation is Mandatory
**Statement**: Every Smart Checkout transaction must undergo exit validation (QR code scan). No bypass option for pilots.

| Attribute | Value |
|-----------|-------|
| **Risk Level** | MEDIUM |
| **Implied By** | Brainstorming: exit validation mandatory (stated as critical fraud prevention measure) |
| **Validation Required** | YES - Loss Prevention approval |
| **If Fails, Impact** | Fraud prevention goal compromised; loss rates may increase |
| **Owner** | Loss Prevention Director |
| **Flexibility** | Can be revisited post-pilot if operational burden is high |

---

## Assumption Validation Checklist

**BEFORE PROJECT KICKOFF** - All HIGH and MEDIUM assumptions must be validated:

- [ ] ASSUMPTION-001: POS API audit completed; API docs reviewed
- [ ] ASSUMPTION-002: WiFi survey completed; all stores meet 10 Mbps minimum
- [ ] ASSUMPTION-003: Stripe account confirmed active
- [ ] ASSUMPTION-004: **CRITICAL** Exit gate QR scanning feasibility confirmed at all 5 stores
- [ ] ASSUMPTION-005: Smartphone ownership market data confirmed
- [ ] ASSUMPTION-006: Store manager coverage plan documented
- [ ] ASSUMPTION-007: Stripe SLA reviewed; acceptable
- [ ] ASSUMPTION-008: Product Owner approves 30-minute QR expiration
- [ ] ASSUMPTION-011: Corporate policy on Store Manager overrides confirmed

**Sign-Off Required From**: Product Owner, Technical Lead, Store Operations, Loss Prevention

---

## Risk Mitigation Strategies

### High-Risk Assumptions

#### ASSUMPTION-004 (Exit Gate QR Scanning) - **CRITICAL**
**Mitigation Approach** (3-part strategy):

1. **Early Validation** (Week 1):
   - Physical site survey of all 5 pilot stores
   - Consult with facilities team on gate specifications
   - Document current gate hardware/software

2. **Contingency Planning** (If scanning not possible):
   - Option A: Upgrade gates with QR scanners (cost: $5K-10K per store, timeline: 2-3 weeks)
   - Option B: Manual staff verification at exit (lower tech, requires training + staffing)
   - Option C: Remove exit validation for v1.0, implement in v1.1 post-pilot (reduces fraud prevention but enables faster launch)
   - Decision required by Week 2; project timeline adjusts accordingly

3. **Go/No-Go Decision**:
   - If QR scanning feasible: Proceed as planned
   - If not feasible: Select Option B or C and adjust BRD accordingly

---

### Medium-Risk Assumptions

#### ASSUMPTION-001 (POS API Availability)
**Mitigation**:
- Allocate Technical Lead for POS audit in Week 1
- Identify POS vendor contact for API support
- Prepare fallback: Manual inventory sync (batch job, lower automation)
- Timeline buffer: +2 weeks if custom adapter needed

#### ASSUMPTION-002 (WiFi Bandwidth)
**Mitigation**:
- WiFi survey before pilot launch (Week 3)
- Pre-budget for network upgrades if needed ($3K-5K per store)
- Implement offline queueing + retry logic (technical backup)

#### ASSUMPTION-006 (Store Manager Availability)
**Mitigation**:
- Document staff schedules for all pilot stores
- Identify backup manager per store
- Conduct training on dashboard + override procedures
- Monitor escalation response times during pilot

---

## Document Sign-Off

| Role | Name | Signature | Date | Comments |
|------|------|-----------|------|----------|
| **Product Owner** | [Awaiting] | [ ] | [ ] | |
| **Technical Lead** | [Awaiting] | [ ] | [ ] | |
| **Store Operations** | [Awaiting] | [ ] | [ ] | |
| **Loss Prevention** | [Awaiting] | [ ] | [ ] | |

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-06-10 | Copilot CLI | Initial assumptions extracted from BRD brainstorming |

---

**END OF ASSUMPTIONS DOCUMENT**
