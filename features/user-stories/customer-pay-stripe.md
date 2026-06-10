# User Story: Customer Completes Payment via Stripe

**Story ID**: SC-005 (BRD-2026-06-10-smart-checkout-STORY-005)  
**Feature**: Payment Processing  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer using Smart Checkout  
**I want to** securely pay for my cart using debit card, credit card, or Apple Pay  
**So that** I can complete my purchase without visiting a traditional checkout register

---

## 2. Story Scope

### In Scope
- Payment method selection (Debit, Credit, Apple Pay)
- Stripe payment processing integration
- Card validation (BIN, expiry, CVV)
- Transaction amount limit enforcement ($200 max)
- Velocity check enforcement (5 txns per 10 min)
- PCI-DSS compliant (no card data stored locally)
- Payment confirmation with receipt
- Transaction ID generation

### Out of Scope
- Gift cards, store credit, or check payments
- Cash payment
- Payment plan/financing
- Installment payments

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Select Payment Method
  Given the customer has items in cart and taps "Checkout"
  When the payment method selection screen appears
  Then options are shown: "Debit Card", "Credit Card", "Apple Pay"
  And the customer can select one option
  And a payment processing button appears

Scenario 2: Successful Card Payment
  Given the customer selected "Debit Card" or "Credit Card"
  When the customer enters card details (number, expiry, CVV)
  And the customer taps "Pay"
  Then Stripe processes the payment
  And within 2 seconds, confirmation is received
  And the transaction is marked as successful
  And a transaction ID is generated

Scenario 3: Amount Limit Enforced
  Given the cart total is $250
  When the customer attempts to proceed to payment
  Then the app displays: "Transaction amount exceeds $200 limit"
  And the payment button is disabled
  And the customer cannot proceed with payment

Scenario 4: Velocity Check Enforced
  Given the customer has completed 5 transactions in the last 10 minutes
  When attempting a 6th transaction
  Then the app displays: "Too many transactions. Please try again later."
  And the payment is blocked
  And the customer is advised to wait before retrying

Scenario 5: Payment Declined by Stripe
  Given the customer has entered a valid card that is declined by Stripe
  When the payment is submitted
  Then the app displays the reason: "Card declined: Insufficient funds" (or similar)
  And the customer is offered to retry with the same card or try a different method
  And the cart is preserved (no items lost)

Scenario 6: Apple Pay Integration
  Given the customer selected "Apple Pay"
  When the customer taps the payment button
  Then the Apple Pay sheet appears (native iOS)
  And the customer authenticates with Face ID or Touch ID
  And the payment is processed via Stripe
  And confirmation is shown (same as card payment)

Scenario 7: Payment Timeout
  Given a payment is being processed
  When no response is received from Stripe after 30 seconds
  Then the app displays: "Payment processing timeout. Please check your connection."
  And the customer can retry the payment
  And the cart is preserved
```

---

## 4. Technical Requirements

- **Stripe SDK**: Stripe iOS SDK and Stripe Android SDK (latest)
- **PCI Compliance**: No card data stored locally; all processing via Stripe (tokenization)
- **Card Validation**: Stripe validates BIN, expiry, CVV
- **Amount Limit**: Server-side validation (not just client-side)
- **Velocity Check**: Server-side tracking (track by device ID + timestamp)
- **Apple Pay**: Native integration using PKPaymentRequest (iOS)
- **Error Handling**: Graceful handling of all Stripe error codes
- **Transaction ID**: Unique ID from Stripe charge object

---

## 5. Dependencies

### Blocks
- SC-006 (Receipt Generation)
- SC-008 (Exit Validation QR Generation)

### Blocked By
- SC-004 (Cart Management)
- ASSUMPTION-003: Stripe merchant account configured

---

## 6. Story Points & Breakdown

**Story Points**: 8

| Component | Points | Rationale |
|-----------|--------|-----------|
| **Stripe SDK Integration** | 2 | Complex setup, tokenization, PCI compliance |
| **Payment UI (Card Form + Apple Pay)** | 2 | Form validation, Apple Pay native dialog |
| **Amount & Velocity Validation** | 1.5 | Server-side checks, device ID tracking |
| **Error Handling & Retry Logic** | 1.5 | Multiple error scenarios, graceful recovery |
| **Testing (Unit, Integration, E2E)** | 1.5 | Stripe mock testing, transaction verification |

**Total**: 8 points

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Payment processing is the core monetization flow. Without it, no transactions complete.

---

## 8. Assumption References

| Assumption ID | Statement | Impact on Story |
|---------------|-----------|-----------------|
| ASSUMPTION-003 | Stripe merchant account configured | Story cannot proceed without this |
| ASSUMPTION-007 | Stripe payment latency <1 second | Performance SLA (<2 min checkout) depends on this |

---

## 9. Definition of Done

- [ ] All AC scenarios passing (SC-005.1 through SC-005.7)
- [ ] Stripe integration end-to-end tested
- [ ] Amount limit enforced on server-side
- [ ] Velocity check working correctly (5 per 10 min)
- [ ] Apple Pay integration working on iOS
- [ ] Card payment working on Android
- [ ] All Stripe error scenarios handled gracefully
- [ ] PCI-DSS compliance verified (no card data stored)
- [ ] Unit tests for payment validation logic (>85% coverage)
- [ ] Integration tests with Stripe test environment
- [ ] QA sign-off received
- [ ] Security review completed
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.7 | Payment Method Selection |
| FR-1.8 | Payment Processing (<2 min total) |
| FR-5.1 to FR-5.5 | Stripe Integration & Fraud Prevention |
| NFR-1 | Performance - Checkout Time (<2 min) |
| NFR-5 | Data Security (PCI-DSS) |

