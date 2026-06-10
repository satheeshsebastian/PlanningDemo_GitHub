# Smart Coupon System - Assumptions & Risk Register

**Document ID:** `BRD-2026-06-10-smart-coupon-system`  
**Date Created:** 2026-06-10  
**Version:** 1.0

---

## Key Assumptions

### ASSUMPTION-001: Customer Behavior Data Available
**Statement:** Customer behavior and purchase history data are available, quality-assured, and accessible for personalization analysis.

**Risk Level:** MEDIUM  
**Validation Required:** YES  
**If Fails, Impact:** Coupon personalization cannot be implemented; system degrades to random/generic issuance

---

### ASSUMPTION-002: Checkout API Integration Possible
**Statement:** Existing checkout/payment system has API capability to integrate coupon validation and redemption in real-time (<500ms).

**Risk Level:** HIGH  
**Validation Required:** YES - PRIORITY  
**If Fails, Impact:** Checkout distribution channel blocked; entire redemption flow compromised; project viability questioned

---

### ASSUMPTION-003: 35% Redemption Rate Achievable
**Statement:** With personalization, a 35% coupon redemption rate is realistically achievable within 90 days of launch.

**Risk Level:** MEDIUM  
**Validation Required:** YES  
**If Fails, Impact:** Success metric will fail; business justification weakened; may require discount strategy revision

---

### ASSUMPTION-004: Email Delivery Service Available
**Statement:** Email delivery infrastructure (SendGrid, AWS SES, etc.) is available and can handle email distribution at scale.

**Risk Level:** LOW  
**Validation Required:** NO (standard service)  
**If Fails, Impact:** Email distribution channel delayed; fallback to in-app notifications

---

### ASSUMPTION-005: Loyalty Tier/Customer Data Accessible
**Statement:** Existing loyalty program customer data (tier, preferences, segments) is accessible via API or data feed.

**Risk Level:** LOW  
**Validation Required:** NO (existing system)  
**If Fails, Impact:** Tier-based personalization delayed; basic functionality unaffected

---

### ASSUMPTION-006: GDPR Compliance Framework Exists
**Statement:** Customer consent management and data deletion capability already implemented in loyalty platform.

**Risk Level:** LOW  
**Validation Required:** NO (compliance requirement)  
**If Fails, Impact:** Consent storage must be built; adds 1-2 week effort

---

---

## Risk Summary Table

| Assumption | Risk Level | Validation Status | Blocker? |
|-----------|-----------|-------------------|----------|
| ASSUMPTION-001 | MEDIUM | Pending | No |
| ASSUMPTION-002 | **HIGH** | **PRIORITY** | **YES - CRITICAL** |
| ASSUMPTION-003 | MEDIUM | Pending | No |
| ASSUMPTION-004 | LOW | Decided | No |
| ASSUMPTION-005 | LOW | Decided | No |
| ASSUMPTION-006 | LOW | Decided | No |

---

## Pre-Development Validation Checklist

**CRITICAL (Must validate before Sprint 1):**
- [ ] ASSUMPTION-002: Engineering team confirms checkout API integration feasible
- [ ] ASSUMPTION-001: Data team confirms purchase history quality & availability
- [ ] ASSUMPTION-003: Business benchmark against industry redemption rates

**Standard (Validate during Sprint 1):**
- [ ] ASSUMPTION-004: Email service confirmed & tested
- [ ] ASSUMPTION-005: Loyalty data API accessible
- [ ] ASSUMPTION-006: Consent framework reviewed with compliance

---

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-06-10 | Copilot BA | Initial assumptions & risk assessment |

