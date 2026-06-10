# User Story: Customer Receives Digital Receipt

**Story ID**: SC-006 (BRD-2026-06-10-smart-checkout-STORY-006)  
**Feature**: Digital Receipt Generation  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer who completed a Smart Checkout purchase  
**I want to** receive a digital receipt via email or SMS  
**So that** I have a record of my transaction for returns, warranty claims, or expense reimbursement

---

## 2. Story Scope

### In Scope
- Receipt generation in PDF or text format
- Email delivery option (customer provides email at checkout)
- SMS delivery option (customer provides phone number)
- Receipt contains: transaction ID, items, total, timestamp, store location
- Delivery confirmation
- Receipt can be resent later (optional v1.0)

### Out of Scope
- Physical receipt printing
- Receipt attachment to loyalty account
- Recurring receipt delivery

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Generate Receipt After Payment
  Given payment was successful
  When the payment confirmation screen appears
  Then the app displays a "Receipt" section
  And options are shown: "Email Receipt", "SMS Receipt", "Skip"
  And a default email field is pre-populated (if available)

Scenario 2: Email Receipt Delivery
  Given the customer selected "Email Receipt"
  When the customer enters an email and taps "Send"
  Then the receipt is generated in PDF format
  And the receipt is sent to the provided email
  And a confirmation message appears: "Receipt sent to [email]"

Scenario 3: SMS Receipt Delivery
  Given the customer selected "SMS Receipt"
  When the customer enters a phone number and taps "Send"
  Then the receipt is generated in text format
  And the receipt is sent to the provided phone number
  And a confirmation message appears: "Receipt sent to [phone]"

Scenario 4: Receipt Content
  Given a receipt has been generated
  When the receipt is opened
  Then it contains:
  - Transaction ID (unique identifier)
  - Store location and address
  - Date and time of transaction
  - List of items (name, qty, price per item)
  - Subtotal (before tax, if applicable)
  - Tax (if applicable)
  - Total amount
  - Payment method used (masked, e.g., "VISA ending in 4242")
```

---

## 4. Technical Requirements

- **PDF Generation**: Library like iText or PDFKit (cross-platform)
- **Email Delivery**: SendGrid or AWS SES API
- **SMS Delivery**: Twilio or AWS SNS API
- **Receipt Template**: HTML template with store branding
- **Data Storage**: Store receipt data for 90 days (auditability)
- **Compliance**: Secure transmission (TLS), no plaintext storage

---

## 5. Dependencies

### Blocks
- None (standalone)

### Blocked By
- SC-005 (Payment Processing - must complete before receipt)

---

## 6. Story Points & Breakdown

**Story Points**: 5

| Component | Points | Rationale |
|-----------|--------|-----------|
| **PDF/Text Generation** | 1 | Template-based, straightforward |
| **Email/SMS Delivery Integration** | 1.5 | SendGrid/SMS API integration, error handling |
| **Receipt UI in App** | 1 | Simple form to collect email/phone |
| **Testing & Error Scenarios** | 1.5 | Mock email/SMS delivery, verify content |

**Total**: 5 points

---

## 7. MoSCoW Classification

**MOSCOW**: SHOULD  
**Rationale**: Important for customer satisfaction and audit trails, but not strictly required for MVP pilot. Can be deferred to Sprint 2 if delivery timeline is tight.

---

## 8. Definition of Done

- [ ] Receipt generated with all required fields
- [ ] Email delivery working via SendGrid (or equivalent)
- [ ] SMS delivery working via Twilio (or equivalent)
- [ ] Delivery confirmation shown to customer
- [ ] Receipt data stored securely for 90 days
- [ ] Unit tests for receipt generation logic
- [ ] Integration tests with email/SMS providers
- [ ] QA sign-off received
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.10 | Digital Receipt Generation & Delivery |

