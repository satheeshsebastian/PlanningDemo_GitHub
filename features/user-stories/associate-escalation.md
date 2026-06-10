# User Story: Store Associate Handles Failed Scans

**Story ID**: SC-008 (BRD-2026-06-10-smart-checkout-STORY-008)  
**Feature**: Failed Scan Escalation  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** store associate in a pilot store  
**I want to** receive escalation alerts when a customer's item fails to scan  
**So that** I can manually verify the item and help the customer complete their checkout

---

## 2. Story Scope

### In Scope
- Escalation alert system (in-store notifications)
- Escalation queue/list for store associates
- Manual item verification process
- Item confirmation and cart update
- Escalation status tracking (pending, resolved)

### Out of Scope
- Barcode database management
- POS system troubleshooting
- Inventory adjustments

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Escalation Alert Received
  Given a customer's barcode/QR scan failed
  When the customer taps "Escalate to Staff"
  Then a notification is sent to store associates' devices/dashboard
  And the notification shows: Customer location (aisle/checkout), Item description (if available), Transaction ID
  And an "Accept" button allows the associate to claim the escalation

Scenario 2: Associate Accepts Escalation
  Given the escalation notification appeared
  When the store associate taps "Accept"
  Then the escalation is marked as "In Progress"
  And the customer is notified: "A store associate is helping with your item"
  And the associate can view the customer's current cart and transaction details

Scenario 3: Manual Item Verification
  Given the associate has accepted an escalation
  When the associate manually scans the item barcode
  Then the POS system looks up the item (same as customer scan)
  And the item details are displayed to the associate
  And the associate can confirm: "Correct item" or "Incorrect - try again"

Scenario 4: Add Item to Cart
  Given the associate confirmed the correct item
  When the associate taps "Add to Customer's Cart"
  Then the item is added to the customer's cart in real-time
  And the customer's phone displays the updated cart
  And the escalation is marked "Resolved"
  And the customer is notified: "Item added to your cart. Ready to continue?"

Scenario 5: Escalation History
  Given the associate is on duty
  When the associate views the escalation queue
  Then a list shows all pending escalations with:
  - Timestamp (how long ago)
  - Customer location (if available)
  - Status (pending, in-progress, resolved)
  - Associate assigned (if claimed)
```

---

## 4. Technical Requirements

- **Push Notification System**: Firebase Cloud Messaging (Android) or APNs (iOS)
- **Associate Device/Web Interface**: Tablet or web dashboard at service desk
- **Escalation Queue**: Real-time queue with priority (first-in, first-out)
- **WebSocket/Real-Time Updates**: Sync between customer phone and associate device
- **Item Verification**: Re-use same barcode scanning as customer (backend API)
- **Cart Sync**: Update customer's cart in real-time when associate adds item

---

## 5. Dependencies

### Blocks
- None (independent)

### Blocked By
- SC-002 (Barcode Scanning - must be implemented for escalation to work)
- SC-004 (Cart Management - must support remote cart updates)

---

## 6. Story Points & Breakdown

**Story Points**: 8

| Component | Points | Rationale |
|-----------|--------|-----------|
| **Push Notification System** | 2 | FCM/APNs setup, notification delivery |
| **Associate UI/Dashboard** | 2 | Escalation queue, item verification flow |
| **Real-Time Sync** | 2 | WebSocket/polling for live cart updates |
| **Testing & Integration** | 2 | Mock notifications, end-to-end flow testing |

**Total**: 8 points

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Critical for handling scan failures. Without this, customers with problematic barcodes are stuck and unable to complete checkout.

---

## 8. Definition of Done

- [ ] Push notification system set up and tested
- [ ] Escalation queue displaying correctly
- [ ] Store associate can claim escalation
- [ ] Item verification flow working end-to-end
- [ ] Item added to customer cart in real-time
- [ ] All AC scenarios passing
- [ ] Unit tests for escalation logic
- [ ] Integration tests with mock notification service
- [ ] Load testing: handle 50 concurrent escalations
- [ ] QA sign-off received
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.4 | Failed Scan Handling (escalate to staff) |

