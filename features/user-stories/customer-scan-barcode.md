# User Story: Customer Scans Item by Barcode

**Story ID**: SC-002 (BRD-2026-06-10-smart-checkout-STORY-002)  
**Feature**: Item Scanning (Barcode)  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer using the Smart Checkout app  
**I want to** scan a product barcode using my phone camera  
**So that** the item is automatically added to my cart with the correct price and details

---

## 2. Story Scope

### In Scope
- Barcode detection and decoding (UPC-A, EAN-13, CODE-128)
- Real-time camera preview with barcode overlay indicator
- Item lookup in POS system by barcode
- Display scanned item name, price, and quantity
- Add item to cart automatically
- Audio/haptic feedback on successful scan
- Scan timeout (5 seconds max)

### Out of Scope
- Manual barcode entry by customer
- Barcode printing or generation
- Barcode database seeding (POS owns this)

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Successful Barcode Scan
  Given the customer has the camera open in the app
  And a product barcode is visible in the camera frame
  When the barcode is in focus and within the frame for 1 second
  Then the app detects the barcode
  And the app displays the item name, unit price, quantity (1)
  And a "Add to Cart" button appears
  And audio/haptic feedback confirms the scan
  And the item is added to cart automatically
  And the camera returns to scanning state for next item

Scenario 2: Item Not Found in POS
  Given the barcode was successfully scanned
  When the POS system does not have the item in its database
  Then the app displays an error: "Item not found in system"
  And options are offered: "Retry Scan", "Escalate to Staff", "Discard"
  And no item is added to cart

Scenario 3: Failed Barcode Detection
  Given the customer is pointing the camera at the product
  When the barcode is out of focus, partially obscured, or damaged
  Then the app continuously attempts to detect the barcode
  And after 5 seconds without detection, a message appears: "Barcode not detected. Try repositioning."
  And the customer can retry or tap "Escalate to Staff"

Scenario 4: Quantity Increase on Duplicate Scan
  Given an item is already in the cart (qty = 1)
  When the customer scans the same barcode again
  Then the app recognizes the duplicate
  And the cart quantity increases (qty = 2)
  And the line total is updated (price × 2)
  And the running cart total is updated
  And no duplicate line item is created

Scenario 5: Network Timeout
  Given the app is attempting to lookup a barcode in POS
  When the response takes longer than 2 seconds
  Then the app displays: "Item lookup timeout. Please try again."
  And the customer can retry the scan or escalate
```

---

## 4. Technical Requirements

- **Barcode Scanning**: ZXing library (Android) or Vision Framework (iOS)
- **Supported Formats**: UPC-A, EAN-13, CODE-128 (others optional)
- **Barcode Overlay**: Visual guide on camera (rectangle, grid, lines indicating valid zone)
- **POS API Call**: RESTful GET `/api/inventory/lookup?barcode={barcode}`
- **Response Time SLA**: <500ms (FR-4.1)
- **Debouncing**: Ignore duplicate scans within 2 seconds (same barcode)
- **Haptic/Audio Feedback**: VibrationPattern (Android), AVAudioSession (iOS)
- **Offline Queueing**: Cache failed lookups to retry when online

---

## 5. Dependencies

### Blocks
- SC-004 (Cart Management)
- SC-005 (Cart Totals & Display)

### Blocked By
- SC-001 (App Launch & Camera Permission)
- POS API availability (ASSUMPTION-001)

### Related
- SC-003 (Item Scanning - QR Code)
- SC-011 (Network Resilience)

### Assumes
- ASSUMPTION-001: POS has inventory lookup API
- ASSUMPTION-002: Network bandwidth sufficient for <500ms lookup

---

## 6. Story Points & Breakdown

**Story Points**: 8

| Component | Points | Rationale |
|-----------|--------|-----------|
| **Barcode Detection** | 2 | ZXing/Vision integration, overlay UI, real-time processing |
| **POS API Integration** | 2 | API call, error handling, response parsing, timeout logic |
| **Cart Addition Logic** | 1.5 | Duplicate detection, quantity increment, state management |
| **Haptic/Audio Feedback** | 1 | Platform-specific vibration & sound APIs |
| **Testing & Error Scenarios** | 1.5 | Unit tests, integration tests with POS mock, UI tests |

**Total**: 8 points  
**Justification**: Medium-complexity story involving real-time camera processing, network calls, and state management. Barcode detection library complexity and POS integration add risk.

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Core feature of Smart Checkout. Without barcode scanning, the entire app is non-functional. Barcode is the primary scanning method for pilot.

---

## 8. Assumption References

| Assumption ID | Statement | Impact on Story |
|---------------|-----------|-----------------|
| ASSUMPTION-001 | POS API has inventory lookup endpoint | Story depends on this API; if unavailable, story blocked |
| ASSUMPTION-002 | WiFi bandwidth ≥10 Mbps | Item lookup must complete <500ms; if bandwidth insufficient, SLA fails |

**If assumptions change**:
- ASSUMPTION-001 fails: Must implement fallback (manual lookup, pre-cached data, or escalate to staff)
- ASSUMPTION-002 fails: Reestimate needed (+2 points); must implement offline queueing and retry logic
- AC might break: <500ms lookup SLA may not be achievable

---

## 9. Definition of Done

- [ ] Barcode detection working on iOS 14+ and Android 10+
- [ ] All AC scenarios passing (SC-002.1 through SC-002.5)
- [ ] POS API integration tested end-to-end (happy path + error cases)
- [ ] Response time <500ms verified (load testing with 50 concurrent users)
- [ ] Haptic/audio feedback working on both platforms
- [ ] Duplicate scan debouncing working correctly
- [ ] Unit tests: barcode detection, POS API call, cart state update (>85% coverage)
- [ ] UI tests: camera overlay, item display, error messages
- [ ] Integration tests: POS mock server responds correctly
- [ ] QA sign-off received
- [ ] Documentation updated (user guide + troubleshooting)
- [ ] No blocking bugs open

---

## 10. INVEST Validation

| Criterion | Status | Notes |
|-----------|--------|-------|
| **I (Independent)** | ✅ PASS | Depends on SC-001 (app launch) but can be developed in parallel after SC-001 design is done |
| **N (Negotiable)** | ✅ PASS | Details negotiable (barcode formats, feedback mechanisms, UI styling) |
| **V (Valuable)** | ✅ PASS | Clear user value: enables item addition without manual entry |
| **E (Estimable)** | ✅ PASS | 8 points justified; team can estimate with breakdown |
| **S (Small)** | ✅ PASS | 8 points; achievable in <2 week sprint |
| **T (Testable)** | ✅ PASS | Clear AC; mockable POS API allows isolated testing |

**Verdict**: ✅ INVEST Healthy

---

## 11. Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.2 | Item Scanning (Barcode) |
| FR-4.1 | Real-Time Inventory Lookup (<500ms) |
| NFR-2 | Performance - Scan Response Time |

---

## Story History

| Date | Update | Author |
|------|--------|--------|
| 2026-06-10 | Initial story creation | Copilot CLI |

