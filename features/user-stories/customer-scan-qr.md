# User Story: Customer Scans Item by QR Code

**Story ID**: SC-003 (BRD-2026-06-10-smart-checkout-STORY-003)  
**Feature**: Item Scanning (QR Code)  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer using the Smart Checkout app  
**I want to** scan a product QR code (in addition to barcode)  
**So that** I have flexibility to scan items that have QR codes or damaged/hard-to-read barcodes

---

## 2. Story Scope

### In Scope
- QR code detection and decoding
- Support product QR codes (linked to inventory system)
- Real-time camera preview with QR overlay
- Item lookup by QR code in POS system
- Identical functionality to barcode scanning (add to cart, feedback, etc.)

### Out of Scope
- QR code generation
- Exit validation QR codes (separate story: SC-008)
- Dynamic QR codes (time-based expiration)

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Successful QR Code Scan
  Given the customer has the camera open in the app
  And a product QR code is visible in the camera frame
  When the QR code is in focus and within the frame for 1 second
  Then the app decodes the QR code
  And the item lookup is performed on POS system
  And the item name, unit price, quantity (1) are displayed
  And audio/haptic feedback confirms the scan
  And the item is added to cart automatically
  And the camera returns to scanning state

Scenario 2: QR Code Not Found in POS
  Given the QR code was successfully decoded
  When the POS system does not have the item linked to this QR code
  Then the app displays: "Item not found in system"
  And "Retry Scan", "Escalate to Staff", "Discard" options are shown
  And no item is added to cart

Scenario 3: Invalid QR Code Format
  Given the QR code is scanned successfully
  When the decoded QR data does not contain valid item identification
  Then the app displays: "Invalid product code. Please try again."
  And the customer can retry or escalate

Scenario 4: Mixed Barcode & QR Scanning
  Given the customer has already scanned some items by barcode
  When the customer scans an item by QR code
  Then the app treats it the same as barcode scanning
  And duplicate detection works across barcode + QR (same item)
  And cart quantity updates correctly if item was already present
```

---

## 4. Technical Requirements

- **QR Decoding**: ZXing library (Android) or Vision Framework (iOS)
- **QR Format**: Standard QR code containing UPC/EAN code or item SKU
- **POS API Call**: RESTful GET `/api/inventory/lookup?qr={qr_data}` or `/api/inventory/lookup?barcode={extracted_code}`
- **Response Time**: <500ms (same as barcode)
- **Duplicate Detection**: Cross-reference barcode + QR codes for same item
- **Debouncing**: Ignore duplicate QR scans within 2 seconds

---

## 5. Dependencies

### Blocks
- SC-004 (Cart Management)

### Blocked By
- SC-001 (App Launch & Camera Permission)
- SC-002 (Barcode Scanning - should be implemented first for consistency)

### Related
- SC-002 (Item Scanning - Barcode)
- SC-011 (Network Resilience)

---

## 6. Story Points & Breakdown

**Story Points**: 5

| Component | Points | Rationale |
|-----------|--------|-----------|
| **QR Detection & Decoding** | 1.5 | Similar to barcode; reuse of ZXing/Vision framework |
| **POS API Integration** | 1 | Same API endpoint as barcode (already built in SC-002) |
| **Cross-Format Duplicate Detection** | 1 | Logic to recognize same item scanned as barcode vs. QR |
| **Testing & Error Scenarios** | 1.5 | Unit, integration, UI tests |

**Total**: 5 points  
**Justification**: Smaller than barcode story because QR detection library is similar and POS integration already exists from SC-002. Main complexity is cross-format duplicate detection.

---

## 7. MoSCoW Classification

**MOSCOW**: SHOULD  
**Rationale**: Important for flexibility and edge cases (damaged barcodes) but not strictly required for MVP. Barcode scanning alone is sufficient for pilot. Can be deferred to Sprint 2 if needed.

---

## 8. Assumption References

| Assumption ID | Statement | Impact on Story |
|---------------|-----------|-----------------|
| ASSUMPTION-001 | POS API has inventory lookup endpoint | Story depends on this API |
| ASSUMPTION-002 | WiFi bandwidth sufficient | <500ms response time requirement |

---

## 9. Definition of Done

- [ ] QR code detection working on iOS 14+ and Android 10+
- [ ] All AC scenarios passing
- [ ] Cross-format duplicate detection verified (barcode + QR for same item)
- [ ] Response time <500ms verified
- [ ] Unit tests for QR decoding and duplicate detection (>85% coverage)
- [ ] UI tests for QR scanning and error messages
- [ ] Integration tests with POS mock server
- [ ] QA sign-off received
- [ ] No blocking bugs open

---

## 10. INVEST Validation

| Criterion | Status | Notes |
|-----------|--------|-------|
| **I (Independent)** | ✅ PASS | Can be developed after SC-002; reuses infrastructure |
| **N (Negotiable)** | ✅ PASS | Details negotiable (QR format, error handling) |
| **V (Valuable)** | ✅ PASS | Clear user value: flexibility in scanning methods |
| **E (Estimable)** | ✅ PASS | 5 points justified |
| **S (Small)** | ✅ PASS | 5 points; achievable in <1 week sprint |
| **T (Testable)** | ✅ PASS | Clear AC; mockable POS API |

**Verdict**: ✅ INVEST Healthy

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.3 | Item Scanning (QR Code) |
| FR-4.1 | Real-Time Inventory Lookup |

