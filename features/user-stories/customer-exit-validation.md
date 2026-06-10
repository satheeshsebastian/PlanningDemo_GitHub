# User Story: Customer Passes Exit Validation

**Story ID**: SC-007 (BRD-2026-06-10-smart-checkout-STORY-007)  
**Feature**: Exit Validation (Fraud Prevention)  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer who completed Smart Checkout and has a digital receipt  
**I want to** scan my exit QR code at the store exit to verify my purchase  
**So that** the system confirms I paid for all items I'm taking out (fraud prevention and compliance)

---

## 2. Story Scope

### In Scope
- Generate unique exit QR code (valid for 30 minutes)
- QR code display on post-checkout confirmation screen
- Exit QR code scanning at store exit (by staff or automated gate)
- Receipt vs. scanned items verification
- Approval/denial messages
- Failed exit escalation to store manager

### Out of Scope
- Gate hardware/barrier integration (assumed available)
- Physical enforcement of barrier
- Exit override without escalation (v1.0)

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Generate Exit QR Code
  Given payment was successful
  When the payment confirmation screen displays
  Then a unique QR code is generated
  And the QR code is displayed with text: "Scan QR code at exit to complete checkout"
  And the QR code contains the transaction ID and item list
  And the QR code is valid for 30 minutes

Scenario 2: Successful Exit Validation
  Given the customer has the exit QR code
  When store staff (or automated scanner) scans the QR code at the exit gate
  Then the system compares scanned items against receipt
  And all items match (count, names, prices)
  Then the system displays: "APPROVED TO EXIT"
  And the exit gate/barrier opens
  And the customer can leave

Scenario 3: Exit Validation Failed - Item Mismatch
  Given the exit QR code is scanned
  When the scanned items do NOT match the receipt
  (e.g., customer has different items, missing items, or extra items)
  Then the system displays: "VERIFICATION FAILED - Item mismatch"
  And the exit gate/barrier remains closed or locked
  And a store manager is notified via dashboard alert

Scenario 4: QR Code Expired
  Given more than 30 minutes have passed since checkout
  When the customer attempts to scan the exit QR code
  Then the system displays: "QR code expired. Please contact store staff."
  And the customer is directed to escalate to a manager

Scenario 5: Exit Validation Escalation
  Given exit validation failed or QR expired
  When the store manager receives the alert
  Then the manager can override the system on the dashboard
  And the exit gate is manually opened
  And the escalation is logged with timestamp and reason
```

---

## 4. Technical Requirements

- **QR Code Generation**: Generate dynamic QR with transaction ID + item list
- **QR Encoding**: Include item names, quantities, prices, total in QR data
- **Expiration**: Server-side tracking of 30-minute expiration
- **Exit Scanner**: Integration with store exit gate scanner (API or webhook)
- **Verification Logic**: Compare scanned items list vs. receipt in system
- **Escalation System**: Send alert to store manager dashboard if verification fails
- **Logging**: Log all exit validation attempts (success/failure)

---

## 5. Dependencies

### Blocks
- None (customer flow ends here)

### Blocked By
- SC-005 (Payment Processing - must complete before exit validation)
- ASSUMPTION-004: Exit gates retrofitted with QR scanning capability

---

## 6. Story Points & Breakdown

**Story Points**: 8

| Component | Points | Rationale |
|-----------|--------|-----------|
| **QR Generation & Encoding** | 2 | Dynamic QR with item data, secure encoding |
| **Exit Gate Scanner Integration** | 2 | Hardware/API integration (critical dependency) |
| **Verification Logic** | 2 | Compare receipts, handle item mismatches |
| **Manager Escalation & Logging** | 1.5 | Dashboard alerts, escalation logic |
| **Testing & Error Scenarios** | 0.5 | Integration tests with mock scanner |

**Total**: 8 points

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Core fraud prevention mechanism. Critical for pilot success and loss prevention.

---

## 8. Assumption References

| Assumption ID | Statement | Impact on Story |
|---------------|-----------|-----------------|
| ASSUMPTION-004 | Exit gates can be retrofitted with QR scanning | **CRITICAL** - If false, entire story is invalid; must pivot |
| ASSUMPTION-008 | Exit QR valid for 30 minutes | Determines expiration window; can be adjusted post-pilot |
| ASSUMPTION-012 | Exit validation is mandatory | Determines workflow (cannot bypass) |

---

## 9. Definition of Done

- [ ] Exit QR code generation working correctly
- [ ] QR code valid for 30 minutes (verified with timestamp)
- [ ] Exit gate scanner integration end-to-end tested
- [ ] Item verification logic working correctly
- [ ] All AC scenarios passing
- [ ] Manager escalation alerts working on dashboard
- [ ] All failed exits logged with details
- [ ] Unit tests for verification logic (>85% coverage)
- [ ] Integration tests with mock scanner
- [ ] Security review: QR code encryption/integrity verified
- [ ] QA sign-off received
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-2.1 to FR-2.5 | Exit Validation Flow |
| FR-1.11 | Exit QR Generation |

