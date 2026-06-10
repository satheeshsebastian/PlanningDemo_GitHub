# Functional Test Cases - Smart Checkout System (Complete)
## Master Test Case Summary & Index

**Document ID**: TC-MASTER-SMART-CHECKOUT  
**Date**: 2026-06-10  
**Total Test Cases**: 156  
**Estimated QA Execution Time**: 8-10 hours (all stories)

---

## Test Case Distribution by Story

| Story | Test Cases | Happy Path | Error | Edge Case | Security | Total Points |
|-------|-----------|-----------|-------|-----------|----------|--------------|
| **SC-001** | App Launch | 3 | 4 | 3 | 2 | **12** |
| **SC-002** | Barcode Scanning | 4 | 5 | 3 | 2 | **14** |
| **SC-003** | QR Code Scanning | 3 | 4 | 2 | 1 | **10** |
| **SC-004** | Cart Management | 5 | 4 | 2 | 1 | **12** |
| **SC-005** | Payment (Stripe) | 6 | 8 | 3 | 4 | **21** |
| **SC-006** | Digital Receipt | 3 | 3 | 2 | 2 | **10** |
| **SC-007** | Exit Validation | 4 | 5 | 3 | 2 | **14** |
| **SC-008** | Escalation Handler | 4 | 4 | 2 | 1 | **11** |
| **SC-009** | Manager Dashboard | 6 | 6 | 3 | 2 | **17** |
| **SC-010** | POS Integration | 5 | 6 | 2 | 3 | **16** |
| **SC-011** | Network Resilience | 4 | 5 | 3 | 2 | **14** |
| **TOTAL** | | **47** | **54** | **28** | **22** | **156** |

---

## Test Case Execution Plan (Recommended Sequencing)

### Phase 1: Smoke Tests (30 min) - P0 Critical Only
Run these first to verify basic functionality:
- SC-001-001: App launches
- SC-002-001: Barcode scan works
- SC-004-001: Cart displays items
- SC-005-001: Payment succeeds
- SC-007-001: Exit validation passes

### Phase 2: Core Functional Tests (3.5 hours)
Happy path for each story:
- SC-001: 3 tests (15 min)
- SC-002: 4 tests (20 min)
- SC-003: 3 tests (15 min)
- SC-004: 5 tests (20 min)
- SC-005: 6 tests (30 min)
- SC-006: 3 tests (15 min)
- SC-007: 4 tests (20 min)
- SC-008: 4 tests (20 min)
- SC-009: 6 tests (30 min)
- SC-010: 5 tests (30 min)
- SC-011: 4 tests (20 min)

### Phase 3: Error Scenarios (2 hours)
Test all error paths:
- All stories: Error scenario tests (54 tests total)

### Phase 4: Edge Cases & Security (2 hours)
- All stories: Edge case + security tests (50 tests total)

### Phase 5: Integration Tests (1.5 hours)
- Multi-story workflows
- End-to-end customer journey
- Stress testing (50 concurrent users)

### Phase 6: Performance & Load Testing (1 hour)
- Response time verification
- Concurrency limits
- Latency benchmarks

---

## Test Case Templates & Examples

### SC-002: Barcode Scanning (14 Test Cases)

#### Happy Path Tests (4 cases)
**TC-SC-002-001**: Successful Barcode Scan and Add to Cart [P0]
```gherkin
Given camera is open with barcode overlay visible
When customer points camera at valid UPC barcode
Then barcode is detected within 1 second
  And item name, price displayed
  And item added to cart automatically
  And cart total updated
```

**TC-SC-002-002**: Duplicate Scan Increases Quantity [P0]
```gherkin
Given item already in cart (qty=1)
When same barcode scanned again
Then cart quantity increments to qty=2
  And line total = price × 2
  And cart total updated
```

**TC-SC-002-003**: Scan Multiple Different Items [P1]
```gherkin
Given empty cart
When 5 different items scanned in sequence
Then each item appears in cart
  And quantities correct (1 each)
  And cart total is sum of all items
```

**TC-SC-002-004**: Barcode Overlay Guides Scanning [P1]
```gherkin
Given camera active
When barcode is in alignment rectangle
Then rectangle highlights/changes color
  And scan happens automatically within 1 sec
  And haptic feedback provided (iOS/Android)
```

#### Error Scenario Tests (5 cases)
**TC-SC-002-005**: Item Not Found in POS [P1]
```gherkin
Given invalid barcode scanned
When POS lookup returns 404
Then app displays: "Item not found in system"
  And retry, escalate, discard options shown
  And item NOT added to cart
```

**TC-SC-002-006**: Barcode Lookup Timeout [P1]
```gherkin
Given POS API not responding
When scan submitted (waiting >3 sec)
Then timeout message: "Item lookup failed. Try again."
  And customer can retry
  And escalation option available
```

**TC-SC-002-007**: Barcode Out of Focus [P1]
```gherkin
Given blurry/out-of-focus barcode in camera
When attempting to scan for 5 seconds
Then message: "Barcode not detected. Reposition."
  And customer can retry or escalate
```

**TC-SC-002-008**: Damaged/Unreadable Barcode [P1]
```gherkin
Given torn, faded, or partially obscured barcode
When camera cannot decode after 5 sec
Then escalation option prominent
  And store associate can manually verify item
```

**TC-SC-002-009**: Network Timeout During Lookup [P1]
```gherkin
Given WiFi unstable
When barcode submitted, network drops
Then graceful error: "Connection lost. Retrying..."
  And app queues request for retry
  And customer can continue or escalate
```

#### Edge Cases (3 cases)
**TC-SC-002-010**: Barcode Formats (UPC-A, EAN-13, CODE-128) [P2]
```gherkin
Given different valid barcode formats
When each scanned
Then all recognized and processed correctly
  And results consistent regardless of format
```

**TC-SC-002-011**: Very Long Item Name [P2]
```gherkin
Given item with 100+ character name
When scanned and displayed
Then name wrapped/truncated appropriately
  And full name available on detail view
  And UI doesn't break
```

**TC-SC-002-012**: High Velocity Scanning (10 items/min) [P2]
```gherkin
Given rapid scanning (one item every 6 seconds)
When 10 items scanned in 1 minute
Then all items added correctly
  And no duplicates
  And cart total accurate
  And app responsive (no lag)
```

#### Security Tests (2 cases)
**TC-SC-002-013**: SQL Injection Prevention [P1]
```gherkin
Given SQL injection attempt in barcode
When scanned (e.g., "123'; DROP TABLE items;--")
Then barcode treated as literal string
  And no database executed
  And error message generic (no SQL errors shown)
```

**TC-SC-002-014**: Barcode Data Not Logged [P1]
```gherkin
When barcode scanned and logged
Then app logs do NOT contain full barcode values
  And logs may contain masked version (e.g., "***4242")
```

---

### SC-005: Payment Processing (21 Test Cases) - CRITICAL

#### Happy Path Tests (6 cases)
**TC-SC-005-001**: Successful Debit Card Payment [P0]
```gherkin
Given cart total $50
When customer selects "Debit Card" → enters card details
Then Stripe processes payment within 2 sec
  And transaction succeeds
  And "Payment Complete" screen displays
  And transaction ID generated
```

**TC-SC-005-002**: Successful Credit Card Payment [P0]
**TC-SC-005-003**: Successful Apple Pay Payment [P0]
**TC-SC-005-004**: Payment Confirmation Receipt [P1]
**TC-SC-005-005**: Multiple Payments in Sequence [P1]
**TC-SC-005-006**: Payment with Exact Dollar Amount [P1]

#### Error Scenarios (8 cases)
**TC-SC-005-007**: Amount Exceeds $200 Limit [P0]
```gherkin
Given cart total $250
When customer proceeds to payment
Then error: "Amount exceeds $200 limit"
  And payment button disabled
  And customer must reduce cart to <$200
```

**TC-SC-005-008**: Velocity Check - Too Many Transactions [P1]
```gherkin
Given 5 successful transactions in last 10 minutes
When attempting 6th transaction
Then blocked: "Too many transactions. Try later."
  And transaction NOT processed
  And customer must wait
```

**TC-SC-005-009**: Declined Card - Insufficient Funds [P1]
```gherkin
Given card with insufficient balance
When payment submitted
Then Stripe error: "Insufficient funds"
  And payment fails gracefully
  And customer can retry with different card
  And cart preserved
```

**TC-SC-005-010**: Declined Card - Expired Card [P1]
**TC-SC-005-011**: Invalid CVV [P1]
**TC-SC-005-012**: Payment Timeout (>30 sec) [P1]
**TC-SC-005-013**: Network Error During Payment [P1]
**TC-SC-005-014**: Duplicate Payment Prevention [P1]

#### Edge Cases (3 cases)
**TC-SC-005-015**: $0.01 Transaction (Minimum) [P2]
**TC-SC-005-016**: $199.99 Transaction (Near Limit) [P2]
**TC-SC-005-017**: Partial Payment Retry [P2]

#### Security Tests (4 cases)
**TC-SC-005-018**: PCI-DSS Compliance - No Card Data Stored [P0]
```gherkin
Given successful payment
When app data checked
Then NO credit card numbers stored locally
  And NO full card data in memory
  And PCI-DSS requirements verified
```

**TC-SC-005-019**: Stripe Token Used (Not Raw Card) [P1]
**TC-SC-005-020**: Payment Data Encrypted in Transit [P1]
**TC-SC-005-021**: No Payment Data in Logs/Analytics [P1]

---

### SC-007: Exit Validation (14 Test Cases)

#### Happy Path Tests (4 cases)
**TC-SC-007-001**: Successful Exit QR Scan & Approval [P0]
```gherkin
Given customer has completed checkout with receipt
When exit gate scanner reads QR code
Then system verifies scanned items match receipt
  And displays "APPROVED TO EXIT"
  And gate opens
```

**TC-SC-007-002**: Exit QR Valid for 30 Minutes [P1]
**TC-SC-007-003**: Multiple Customers Exit Successfully [P1]
**TC-SC-007-004**: Exit Log Recorded [P1]

#### Error Scenarios (5 cases)
**TC-SC-007-005**: Exit Validation Failed - Item Mismatch [P0]
```gherkin
Given customer's actual items don't match receipt
When QR scanned at exit
Then system displays "VERIFICATION FAILED"
  And gate remains closed
  And manager alert triggered
```

**TC-SC-007-006**: QR Code Expired [P1]
```gherkin
Given >30 minutes since checkout
When QR scanned
Then error: "QR expired. See staff."
  And customer escalated to manager
```

**TC-SC-007-007**: Invalid QR Format [P1]
**TC-SC-007-008**: Duplicate QR Scan Prevention [P1]
**TC-SC-007-009**: Scanner Hardware Failure [P1]

#### Edge Cases (3 cases)
**TC-SC-007-010**: QR Code High Contrast Printing [P2]
**TC-SC-007-011**: QR Scan from Phone Screen [P2]
**TC-SC-007-012**: Multiple Item Additions During Validation [P2]

#### Security Tests (2 cases)
**TC-SC-007-013**: QR Code Encryption & Integrity [P1]
**TC-SC-007-014**: Transaction Data Confidentiality [P1]

---

### SC-010: POS Integration (16 Test Cases)

#### Happy Path Tests (5 cases)
**TC-SC-010-001**: Item Lookup - Barcode Found [P0]
```gherkin
Given valid UPC barcode
When POS lookup called (GET /api/inventory/lookup?barcode=123456)
Then response within 500ms
  And returns {itemId, name, price, taxable}
  And item data accurate
```

**TC-SC-010-002**: Item Lookup - Multiple Items [P1]
**TC-SC-010-003**: Inventory Decrement on Checkout [P1]
**TC-SC-010-004**: Transaction Recording as MOBILE_CHECKOUT [P1]
**TC-SC-010-005**: Stock Accurately Updated Post-Transaction [P1]

#### Error Scenarios (6 cases)
**TC-SC-010-006**: Item Not Found (404) [P1]
**TC-SC-010-007**: POS API Timeout (>2 sec) [P1]
**TC-SC-010-008**: Price Change Mid-Transaction [P1]
**TC-SC-010-009**: Inventory Out of Stock [P1]
**TC-SC-010-010**: POS Connection Error (Retry Logic) [P1]
**TC-SC-010-011**: Authentication Failure [P1]

#### Edge Cases (2 cases)
**TC-SC-010-012**: Concurrent 50+ Lookups [P2]
**TC-SC-010-013**: Very Large Item Database (100k+ SKUs) [P2]

#### Security Tests (3 cases)
**TC-SC-010-014**: No SQL Injection in Lookup [P1]
**TC-SC-010-015**: API Auth Token Not Exposed [P1]
**TC-SC-010-016**: Inventory Data Encrypted [P1]

---

### SC-011: Network Resilience (14 Test Cases)

#### Happy Path Tests (4 cases)
**TC-SC-011-001**: Scan with Stable Network [P0]
**TC-SC-011-002**: Payment with Stable Connection [P1]
**TC-SC-011-003**: Network Recovery Automatic [P1]
**TC-SC-011-004**: Offline Queue Processed on Reconnect [P1]

#### Error Scenarios (5 cases)
**TC-SC-011-005**: WiFi Drops Mid-Scan [P1]
**TC-SC-011-006**: Network Timeout with Retry [P1]
**TC-SC-011-007**: Payment Blocked Without Network [P0]
**TC-SC-011-008**: Queue Overflow (100+ Items) [P1]
**TC-SC-011-009**: Network Restored After 5+ Minutes [P1]

#### Edge Cases (3 cases)
**TC-SC-011-010**: Switch from WiFi to Cellular [P2]
**TC-SC-011-011**: Intermittent Network (drops every 10 sec) [P2]
**TC-SC-011-012**: Very Slow Connection (2G Equivalent) [P2]

#### Security Tests (2 cases)
**TC-SC-011-013**: Queued Data Encrypted [P1]
**TC-SC-011-014**: Queue Data Not Leaked on App Crash [P1]

---

## Test Priority Matrix

### P0 (Critical - Must Pass for Release)
- **27 test cases** covering essential functionality
- If ANY P0 fails → BLOCK release
- Examples: App launch, barcode scan, payment, exit validation

### P1 (High - Important Features)
- **82 test cases** covering main flows and error handling
- If >5 P1 fail → Consider delaying
- Should have <5 open P1 defects at release

### P2 (Medium - Nice to Have)
- **38 test cases** covering edge cases and polish
- Can defer some P2 testing to v1.1
- <2% of overall test suite

### P3 (Low - Documentation)
- **9 test cases** for future reference
- Optional testing

---

## Test Automation Strategy

| Test Type | Automation Level | Tools | Effort |
|-----------|-----------------|-------|--------|
| **Smoke** | Fully Automated | Appium / XCTest | 2 weeks |
| **Functional (Happy Path)** | Fully Automated | Appium / XCTest | 3 weeks |
| **Error Scenarios** | Semi-Automated | Manual verification | 2 weeks |
| **Edge Cases** | Manual | Human testers | 1 week |
| **Security** | Manual + Automated | OWASP ZAP, manual | 1 week |
| **Performance** | Automated | JMeter / LoadRunner | 1 week |

**Total Automation Effort**: ~6-8 weeks

---

## Regression Test Suite

### Minimal Regression (30 min)
- All P0 tests
- Key P1 tests (payment, cart, exit validation)

### Standard Regression (2 hours)
- All P0 + all P1 tests

### Full Regression (8 hours)
- All test cases P0 through P2

### Release Regression (12 hours)
- Complete test suite including exploratory testing

---

## Test Environment Requirements

- **Test Devices**: iPhone SE, iPhone 14, Pixel 4a, Pixel 8
- **OS Versions**: iOS 14+, Android 10+
- **Network**: WiFi, 4G/5G, offline simulation
- **Stripe**: Test account with test card numbers
- **POS Mock**: Mockito/WireMock for API testing
- **Test Data**: Anonymized production data set (100+ items)

---

## Known Issues & Workarounds

(To be updated as testing progresses)

---

## Sign-Off

| Role | Name | Date | Status |
|------|------|------|--------|
| **QA Lead** | [Awaiting] | [ ] | [ ] |
| **Test Automation Lead** | [Awaiting] | [ ] | [ ] |

---

## Appendix: Test Case File Structure

Test case files created:
- `sc-001-launch-app-test-cases.md` (12 tests) ✅ CREATED
- `sc-002-barcode-scan-test-cases.md` (14 tests) [SUMMARY ONLY]
- `sc-003-qr-scan-test-cases.md` (10 tests) [SUMMARY ONLY]
- `sc-004-cart-management-test-cases.md` (12 tests) [SUMMARY ONLY]
- `sc-005-payment-test-cases.md` (21 tests) [SUMMARY ONLY]
- `sc-006-receipt-test-cases.md` (10 tests) [SUMMARY ONLY]
- `sc-007-exit-validation-test-cases.md` (14 tests) [SUMMARY ONLY]
- `sc-008-escalation-test-cases.md` (11 tests) [SUMMARY ONLY]
- `sc-009-manager-dashboard-test-cases.md` (17 tests) [SUMMARY ONLY]
- `sc-010-pos-integration-test-cases.md` (16 tests) [SUMMARY ONLY]
- `sc-011-network-resilience-test-cases.md` (14 tests) [SUMMARY ONLY]

**Total**: 156 test cases | ~8-10 hours manual QA | ~6-8 weeks automation setup

