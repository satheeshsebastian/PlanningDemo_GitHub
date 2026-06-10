# Functional Test Cases
## Smart Coupon System

**Document ID:** FTC-2026-06-10-smart-coupon-system  
**Version:** v1.0  
**Date:** 2026-06-10  
**Scope:** BRD `features/brd/smart-coupon-system-v1.0.md`, user stories `SC-001` through `SC-011`, story map `features/user-stories/story-map-smart-coupon-system.md`, and traceability `features/user-stories/story-traceability.json`

---

## 1. Scope and Coverage

- Total test cases: **82**
- Story coverage: **SC-001 to SC-011**
- Scenario coverage: Happy path, negative, edge, integration, security/permission, reporting, recovery
- Supported channels: **Email, Checkout, Campaigns, In-App**
- Excluded channels validated as out of scope: **SMS, Mobile App, Push Notification**

### Assumptions

- Story IDs and titles are aligned to the repository user-story files.
- Where the requested topic labels differ from repository story titles, equivalent coverage is included in the closest matching story section.
- Test data uses anonymized loyalty-member profiles and non-production coupon identifiers.

### Automation Legend

- **Automated**: Stable and repeatable functional or integration check
- **Manual**: Visual, accessibility, or operator-validation-heavy scenario
- **Critical-path**: Must be automated in release regression and smoke suites

---

## 2. Test Case Index

| Story | Story Title | Test Count | Test IDs |
|---|---|---:|---|
| SC-001 | System Determines Personalized Coupon Eligibility | 7 | T-001 to T-007 |
| SC-002 | Marketer Manages Smart Coupon Content | 7 | T-008 to T-014 |
| SC-003 | System Orchestrates Multi-Channel Coupon Delivery | 7 | T-015 to T-021 |
| SC-004 | Member Receives Smart Coupons by Email | 7 | T-022 to T-028 |
| SC-005 | Shopper Redeems Eligible Coupons at Checkout | 7 | T-029 to T-035 |
| SC-006 | Marketer Adds Smart Coupons to Targeted Campaigns | 7 | T-036 to T-042 |
| SC-007 | Member Discovers Active Coupons In-App | 7 | T-043 to T-049 |
| SC-008 | Member Redeems Coupons With Minimal Effort | 7 | T-050 to T-056 |
| SC-009 | System Keeps Offer Details Consistent Across Channels | 7 | T-057 to T-063 |
| SC-010 | Stakeholder Reviews Smart Coupon Performance | 7 | T-064 to T-070 |
| SC-011 | Admin Configures Smart Coupon Rules and Channels | 7 | T-071 to T-077 |
| Cross-Cutting | Security, performance, failover, recovery | 5 | T-078 to T-082 |

### Coverage Summary by Scenario Type

| Scenario Type | Count |
|---|---:|
| Happy Path | 23 |
| Error / Negative | 24 |
| Edge Case | 15 |
| Integration | 15 |
| Security / Permission / Recovery | 5 |

### Traceability Summary

| Story | BRD Requirement(s) | Acceptance Criteria Covered |
|---|---|---|
| SC-001 | FR-001 | AC-001 / story AC-1 to AC-3 |
| SC-002 | FR-002 | AC-006 / story AC-1 to AC-3 |
| SC-003 | FR-003 | AC-002 to AC-005 / story AC-1 to AC-3 |
| SC-004 | FR-004, FR-008 | AC-002, AC-006, AC-008 |
| SC-005 | FR-005, FR-008 | AC-003, AC-006, AC-008 |
| SC-006 | FR-006, FR-003 | AC-004, AC-006 |
| SC-007 | FR-007, FR-008 | AC-005, AC-006, AC-008 |
| SC-008 | FR-008 | AC-006, AC-008 |
| SC-009 | FR-009, FR-002 | AC-006, AC-007 |
| SC-010 | FR-010 | AC-010 |
| SC-011 | FR-002, FR-003, FR-009, FR-010 | AC-007, AC-009, AC-010 |

---

## 3. Test Cases by Story

## SC-001 - System Determines Personalized Coupon Eligibility

### T-001 - Select relevant coupon from behavior and preference signals
- **Story Link:** SC-001
- **Traceability:** FR-001, AC-001, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Platform / Eligibility API
- **Data Requirements:** Member `LM-1001`; recent pet-food purchases; preference `Pets`; active coupons `PET20`, `HOME10`
- **Preconditions:** Eligibility rules are active and coupon catalog is published
- **Test Steps:** 1) Submit eligibility request for `LM-1001`. 2) Inspect returned coupon and audit payload.
- **Given** a loyalty member has purchase and preference signals favoring pet offers
- **When** eligibility is evaluated
- **Then** the system returns `PET20` instead of a generic offer and stores the decision basis
- **Postconditions:** Eligibility result is available to downstream delivery services
- **Pass/Fail Criteria:** Pass if selected coupon aligns to signals and audit record is present
- **Automation Recommendation:** Critical-path

### T-002 - Apply approved fallback when preference data is missing
- **Story Link:** SC-001
- **Traceability:** FR-001, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Platform / Eligibility API
- **Data Requirements:** Member `LM-1002`; missing preference field; purchase history available; fallback coupon `SAVE5`
- **Preconditions:** Fallback rules and reason-code mapping are configured
- **Test Steps:** 1) Submit eligibility request with incomplete profile. 2) Review coupon and reason code.
- **Given** a member has incomplete preference data
- **When** eligibility runs
- **Then** the system applies the approved fallback rule, returns `SAVE5`, and records a missing-signal reason code
- **Postconditions:** Audit log includes fallback reason and timestamp
- **Pass/Fail Criteria:** Pass if fallback coupon is returned and no processing error occurs
- **Automation Recommendation:** Automated

### T-003 - Rank highest-priority coupon when multiple offers are eligible
- **Story Link:** SC-001
- **Traceability:** FR-001, story AC-1
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Platform / Eligibility API
- **Data Requirements:** Member `LM-1003`; eligible coupons `PET20`, `PET15`, `LOYALTYBONUS`; rule weights configured
- **Preconditions:** Prioritization rules are enabled
- **Test Steps:** 1) Run eligibility for `LM-1003`. 2) Compare selected coupon to configured priority weights.
- **Given** a member qualifies for more than one coupon
- **When** the engine ranks eligible offers
- **Then** only the highest-priority coupon is returned and lower-priority candidates remain recorded for audit
- **Postconditions:** Ranked result set is persisted
- **Pass/Fail Criteria:** Pass if ranking logic consistently selects the configured winner
- **Automation Recommendation:** Automated

### T-004 - Return no coupon when no business rules are satisfied
- **Story Link:** SC-001
- **Traceability:** FR-001, story AC-1
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Platform / Eligibility API
- **Data Requirements:** Member `LM-1004`; no qualifying behavior; no active broad fallback segment
- **Preconditions:** Generic campaign-only logic is disabled for the test
- **Test Steps:** 1) Evaluate eligibility for `LM-1004`. 2) Inspect response and audit output.
- **Given** a member does not satisfy any coupon rules
- **When** eligibility is evaluated
- **Then** the system returns no eligible coupon and records the no-match outcome without failure
- **Postconditions:** Downstream services receive an empty eligibility result
- **Pass/Fail Criteria:** Pass if no coupon is issued and no false-positive offer appears
- **Automation Recommendation:** Automated

### T-005 - Exclude expired coupon from eligibility results
- **Story Link:** SC-001
- **Traceability:** FR-001, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Platform / Eligibility API
- **Data Requirements:** Member `LM-1005`; one expired coupon `FLASH10`; one active coupon `FRESH10`
- **Preconditions:** Coupon validity dates are loaded into the eligibility engine
- **Test Steps:** 1) Evaluate member against both coupons. 2) Inspect selected result.
- **Given** a member matches a coupon whose validity has passed
- **When** eligibility is evaluated
- **Then** the expired coupon is ignored and only active offers can be returned
- **Postconditions:** Audit output shows expiration-based exclusion
- **Pass/Fail Criteria:** Pass if expired content is never exposed downstream
- **Automation Recommendation:** Automated

### T-006 - Show auditable decision details to operations user
- **Story Link:** SC-001
- **Traceability:** FR-001, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Operations console / Audit view
- **Data Requirements:** Completed eligibility record for `LM-1001`
- **Preconditions:** Operations user has access to decision audit screen
- **Test Steps:** 1) Open the audit record. 2) Verify rule set, input summary, selected coupon, and timestamp.
- **Given** an operations user reviews a coupon assignment
- **When** they inspect the eligibility result
- **Then** they can see the rule set, evaluated inputs, selected coupon, fallback indicators if any, and evaluation time
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if all required audit fields are visible and match stored data
- **Automation Recommendation:** Manual

### T-007 - Publish eligibility output to downstream channel contract
- **Story Link:** SC-001
- **Traceability:** FR-001, SC-003 dependency
- **Scenario Type:** Integration
- **Priority:** P0
- **Channel:** Platform to delivery orchestration
- **Data Requirements:** Valid eligibility event payload for `LM-1001`
- **Preconditions:** Channel orchestration endpoint or event consumer is available
- **Test Steps:** 1) Trigger eligibility. 2) Verify event/API payload consumed by delivery service.
- **Given** eligibility returns a coupon for a member
- **When** the result is published to downstream systems
- **Then** the payload contains member ID, coupon ID, reason summary, and evaluation timestamp in the agreed contract
- **Postconditions:** Delivery orchestration can begin without schema transformation errors
- **Pass/Fail Criteria:** Pass if downstream consumer accepts payload without rejection
- **Automation Recommendation:** Critical-path

## SC-002 - Marketer Manages Smart Coupon Content

### T-008 - Save coupon with all required content fields
- **Story Link:** SC-002
- **Traceability:** FR-002, AC-006, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Admin content management
- **Data Requirements:** Coupon title, offer value, start/end date, conditions, redemption instructions, metadata for Email/Checkout/Campaigns/In-App
- **Preconditions:** Marketer is authenticated with content-authoring permission
- **Test Steps:** 1) Create a new coupon. 2) Populate all required fields. 3) Save draft.
- **Given** a marketer enters a complete coupon definition
- **When** the coupon is saved
- **Then** the system stores all mandatory fields and creates a reusable canonical coupon record
- **Postconditions:** Draft coupon is available for preview and publication
- **Pass/Fail Criteria:** Pass if save succeeds and all fields persist accurately
- **Automation Recommendation:** Critical-path

### T-009 - Store channel metadata for all selected channels
- **Story Link:** SC-002
- **Traceability:** FR-002, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Admin content management
- **Data Requirements:** Coupon `PET20`; metadata for email CTA, checkout badge, campaign token, in-app label
- **Preconditions:** Coupon draft exists
- **Test Steps:** 1) Select all supported channels. 2) Enter metadata per channel. 3) Save changes.
- **Given** a coupon is intended for multiple channels
- **When** the marketer configures channel presentation metadata
- **Then** the system stores metadata for each selected channel without overwriting canonical offer fields
- **Postconditions:** Metadata is available to downstream rendering services
- **Pass/Fail Criteria:** Pass if each channel retains its required metadata
- **Automation Recommendation:** Automated

### T-010 - Block publish when mandatory fields are missing
- **Story Link:** SC-002
- **Traceability:** FR-002, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Admin content management
- **Data Requirements:** Coupon with blank redemption instructions and missing end date
- **Preconditions:** Draft coupon is open in edit mode
- **Test Steps:** 1) Leave required fields blank. 2) Attempt to publish.
- **Given** a coupon has incomplete mandatory content
- **When** the marketer tries to publish it
- **Then** publication is blocked and the UI identifies each missing field
- **Postconditions:** Coupon remains in draft status
- **Pass/Fail Criteria:** Pass if no publish event occurs and validation feedback is specific
- **Automation Recommendation:** Critical-path

### T-011 - Reject invalid validity range
- **Story Link:** SC-002
- **Traceability:** FR-002, AC-006
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Admin content management
- **Data Requirements:** Coupon with end date earlier than start date
- **Preconditions:** Marketer is editing a draft coupon
- **Test Steps:** 1) Enter invalid date range. 2) Save or publish the coupon.
- **Given** a marketer enters a validity period where the end date precedes the start date
- **When** the system validates the coupon
- **Then** the save or publish action is rejected with a date-range validation error
- **Postconditions:** Invalid dates are not persisted as published content
- **Pass/Fail Criteria:** Pass if the system blocks the invalid range consistently
- **Automation Recommendation:** Automated

### T-012 - Preserve version history after approved content update
- **Story Link:** SC-002
- **Traceability:** FR-002, SC-009 dependency
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Admin content management
- **Data Requirements:** Published coupon `PET20 v1`; updated exclusion text for `v2`
- **Preconditions:** Coupon has already been published once
- **Test Steps:** 1) Edit published coupon. 2) Save new version. 3) Review version history.
- **Given** a marketer updates an already-approved coupon
- **When** the new version is saved
- **Then** the prior version remains traceable and the updated version becomes the latest canonical record
- **Postconditions:** Version history includes author and timestamp
- **Pass/Fail Criteria:** Pass if rollback and audit trace remain possible
- **Automation Recommendation:** Manual

### T-013 - Enforce field-length and special-character boundaries
- **Story Link:** SC-002
- **Traceability:** FR-002, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Admin content management
- **Data Requirements:** Maximum-length title, unicode characters, long conditions text, HTML characters
- **Preconditions:** Content validation rules are configured
- **Test Steps:** 1) Enter boundary-length content. 2) Save draft. 3) Preview channel payload.
- **Given** a marketer enters boundary-length and special-character content
- **When** the coupon is saved
- **Then** valid boundary data is preserved safely and unsupported markup is sanitized or rejected per rules
- **Postconditions:** Stored content remains display-safe across channels
- **Pass/Fail Criteria:** Pass if content is saved or rejected predictably with no corruption
- **Automation Recommendation:** Automated

### T-014 - Expose canonical content schema to delivery services
- **Story Link:** SC-002
- **Traceability:** FR-002, SC-003 and SC-009 dependencies
- **Scenario Type:** Integration
- **Priority:** P0
- **Channel:** Admin to distribution/consistency services
- **Data Requirements:** Published coupon payload with full canonical schema
- **Preconditions:** Coupon is published and downstream schema contract is available
- **Test Steps:** 1) Publish coupon. 2) Retrieve payload from downstream consumer. 3) Verify schema mapping.
- **Given** a coupon is published from content management
- **When** downstream services consume the coupon record
- **Then** the canonical fields and channel metadata are available without additional manual mapping
- **Postconditions:** Delivery and consistency jobs can use the same source record
- **Pass/Fail Criteria:** Pass if consuming services read the canonical schema successfully
- **Automation Recommendation:** Critical-path

## SC-003 - System Orchestrates Multi-Channel Coupon Delivery

### T-015 - Fan out eligible coupon to all allowed supported channels
- **Story Link:** SC-003
- **Traceability:** FR-003, AC-002 to AC-005, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email, Checkout, Campaigns, In-App
- **Data Requirements:** Eligible member `LM-1001`; coupon `PET20`; all four channels enabled
- **Preconditions:** Eligibility and canonical content services are available
- **Test Steps:** 1) Trigger distribution run. 2) Verify publish record for each channel.
- **Given** a member is eligible for a coupon allowed in all supported channels
- **When** the distribution job runs
- **Then** the coupon is published once to Email, Checkout, Campaigns, and In-App
- **Postconditions:** Per-channel publish statuses are stored
- **Pass/Fail Criteria:** Pass if all allowed channels receive one publish event each
- **Automation Recommendation:** Critical-path

### T-016 - Suppress restricted channel and log the reason
- **Story Link:** SC-003
- **Traceability:** FR-003, story AC-2
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Distribution orchestration
- **Data Requirements:** Coupon `PET20`; Checkout disabled for this coupon; other channels enabled
- **Preconditions:** Channel restriction rule is configured
- **Test Steps:** 1) Trigger distribution. 2) Review per-channel status log.
- **Given** a coupon is restricted from one supported channel
- **When** distribution executes
- **Then** the restricted channel does not receive the coupon and a logged reason identifies the rule that blocked it
- **Postconditions:** Only permitted channels remain active
- **Pass/Fail Criteria:** Pass if no restricted publish occurs and the audit reason is recorded
- **Automation Recommendation:** Critical-path

### T-017 - Prevent duplicate delivery on rerun
- **Story Link:** SC-003
- **Traceability:** FR-003, story AC-1
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Distribution orchestration
- **Data Requirements:** Same member-coupon pair processed twice with identical run inputs
- **Preconditions:** Idempotency keying is enabled
- **Test Steps:** 1) Run distribution twice. 2) Compare publish records and channel outputs.
- **Given** the same distribution input is processed more than once
- **When** the job reruns
- **Then** the system keeps one delivery record per channel and does not duplicate customer-facing coupon presentation
- **Postconditions:** Duplicate suppression reason is auditable
- **Pass/Fail Criteria:** Pass if second run is idempotent across all channels
- **Automation Recommendation:** Automated

### T-018 - Retry failed channel publish without losing other channel results
- **Story Link:** SC-003
- **Traceability:** FR-003, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Distribution orchestration
- **Data Requirements:** Email adapter available; In-App adapter temporarily unavailable
- **Preconditions:** Retry policy and error logging are configured
- **Test Steps:** 1) Trigger publish with one failing adapter. 2) Review statuses. 3) Retry failed channel.
- **Given** one supported channel adapter is unavailable during distribution
- **When** the publish attempt occurs
- **Then** successful channels remain committed, the failed channel is marked failed, and the system supports a targeted retry
- **Postconditions:** Retry converts failed status to success when the adapter recovers
- **Pass/Fail Criteria:** Pass if partial success is preserved and recovery is possible
- **Automation Recommendation:** Automated

### T-019 - Honor delivery timing window
- **Story Link:** SC-003
- **Traceability:** FR-003, story AC-2
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Distribution orchestration
- **Data Requirements:** Coupon valid tomorrow; current run timestamp today
- **Preconditions:** Delivery timing rules are configured
- **Test Steps:** 1) Execute distribution before validity start. 2) Inspect status and scheduled action.
- **Given** a coupon is not yet within its delivery window
- **When** distribution runs early
- **Then** the system does not publish the coupon and records a timing-based defer reason
- **Postconditions:** Coupon remains eligible for future scheduled delivery
- **Pass/Fail Criteria:** Pass if no premature publish occurs
- **Automation Recommendation:** Automated

### T-020 - Display channel-level delivery audit trail
- **Story Link:** SC-003
- **Traceability:** FR-003, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Operations console
- **Data Requirements:** Completed multi-channel publish event
- **Preconditions:** Operations user has audit access
- **Test Steps:** 1) Open delivery event record. 2) Review targeted channels, publish time, and status per channel.
- **Given** an operations user reviews a delivery event
- **When** they inspect the record
- **Then** they can see each targeted channel, publish timestamp, delivery status, and block reason where applicable
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if audit data is complete and matches emitted events
- **Automation Recommendation:** Manual

### T-021 - Deliver eligibility and content payload end to end to channel adapters
- **Story Link:** SC-003
- **Traceability:** FR-003, SC-001 and SC-002 integration
- **Scenario Type:** Integration
- **Priority:** P0
- **Channel:** Platform to all supported adapters
- **Data Requirements:** Published coupon `PET20`; eligible member `LM-1001`; adapter stubs for all supported channels
- **Preconditions:** End-to-end integration environment is available
- **Test Steps:** 1) Run eligibility, content retrieval, and distribution. 2) Validate channel adapter payloads.
- **Given** the platform has a valid eligibility result and canonical content
- **When** the orchestration service prepares channel publishes
- **Then** each adapter receives the correct member, coupon, and presentation metadata payload
- **Postconditions:** Channel services are ready to render or act on the coupon
- **Pass/Fail Criteria:** Pass if contract and content remain consistent across all adapters
- **Automation Recommendation:** Critical-path

## SC-004 - Member Receives Smart Coupons by Email

### T-022 - Render personalized coupon block in loyalty email
- **Story Link:** SC-004
- **Traceability:** FR-004, FR-008, AC-002, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email
- **Data Requirements:** Member `LM-1001`; email template; coupon `PET20` with CTA link
- **Preconditions:** Email channel is enabled and coupon is distributed to Email
- **Test Steps:** 1) Trigger loyalty email send. 2) Open rendered email for `LM-1001`.
- **Given** a member has an eligible smart coupon
- **When** a loyalty email is sent
- **Then** the email includes the member-specific coupon block with personalized details
- **Postconditions:** Email impression event is captured
- **Pass/Fail Criteria:** Pass if the correct coupon appears for the intended recipient only
- **Automation Recommendation:** Critical-path

### T-023 - Show readable offer value, validity, and redemption guidance
- **Story Link:** SC-004
- **Traceability:** FR-004, AC-006, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email
- **Data Requirements:** Coupon with title, value, validity period, exclusions, instructions
- **Preconditions:** Email render is available in desktop and mobile preview
- **Test Steps:** 1) Open the coupon email. 2) Verify details in the coupon block.
- **Given** a member views the coupon email
- **When** coupon content is displayed
- **Then** the email clearly shows the offer value, validity dates, conditions, and redemption guidance
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if required details are visible and unambiguous
- **Automation Recommendation:** Manual

### T-024 - Route email CTA to supported redemption path
- **Story Link:** SC-004
- **Traceability:** FR-008, AC-008, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email to Checkout/In-App
- **Data Requirements:** Trackable CTA URL for coupon `PET20`
- **Preconditions:** Redemption target is configured and active
- **Test Steps:** 1) Click the email CTA. 2) Verify landing page or in-app route. 3) Confirm coupon context persists.
- **Given** a member selects the coupon call to action
- **When** they follow the email link
- **Then** they are taken to the supported redemption path without requiring SMS, push, or another unsupported handoff
- **Postconditions:** Click event is captured with coupon and channel attribution
- **Pass/Fail Criteria:** Pass if CTA resolves to the intended supported experience
- **Automation Recommendation:** Critical-path

### T-025 - Suppress broken or missing CTA link
- **Story Link:** SC-004
- **Traceability:** FR-004, FR-008
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Email
- **Data Requirements:** Coupon content with missing or malformed CTA URL
- **Preconditions:** Email validation rules are enabled
- **Test Steps:** 1) Attempt to generate/send email with malformed link. 2) Review validation or fallback behavior.
- **Given** coupon email content contains an invalid redemption link
- **When** the email is validated or rendered
- **Then** the send is blocked or the coupon block is suppressed with an operational error logged
- **Postconditions:** Customer does not receive a broken CTA
- **Pass/Fail Criteria:** Pass if broken links are prevented from reaching production recipients
- **Automation Recommendation:** Automated

### T-026 - Remove expired coupon from email send population
- **Story Link:** SC-004
- **Traceability:** FR-004, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Email
- **Data Requirements:** Coupon expiring before scheduled send time
- **Preconditions:** Scheduled email job and coupon validity dates are configured
- **Test Steps:** 1) Advance send time beyond coupon validity. 2) Execute send audience generation.
- **Given** a coupon expires before the email send occurs
- **When** the send population is built
- **Then** the expired coupon is excluded from the email content and a suppression reason is recorded
- **Postconditions:** Member receives no invalid offer in email
- **Pass/Fail Criteria:** Pass if expired content is filtered consistently
- **Automation Recommendation:** Automated

### T-027 - Preserve responsive and accessible rendering of coupon email
- **Story Link:** SC-004
- **Traceability:** FR-004, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Email
- **Data Requirements:** Mobile and desktop render previews; screen-reader-friendly text
- **Preconditions:** Email QA previews are available
- **Test Steps:** 1) Open email in desktop and mobile views. 2) Verify coupon text order, alt text, and CTA clarity.
- **Given** the coupon email is rendered in different client contexts
- **When** the member views it on desktop or mobile
- **Then** required coupon details remain readable and the CTA remains actionable
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if layout does not hide critical coupon information
- **Automation Recommendation:** Manual

### T-028 - Capture email impression and click events for analytics
- **Story Link:** SC-004
- **Traceability:** FR-004, FR-010, SC-010 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Email and analytics pipeline
- **Data Requirements:** Email open pixel / click tracking enabled for `PET20`
- **Preconditions:** Analytics ingestion is available
- **Test Steps:** 1) Open the email. 2) Click the CTA. 3) Query analytics events.
- **Given** a member receives and interacts with a coupon email
- **When** impression and click actions occur
- **Then** analytics events are recorded with channel, coupon, member, and timestamp attributes
- **Postconditions:** Reporting can attribute email engagement correctly
- **Pass/Fail Criteria:** Pass if analytics events match user actions one-to-one
- **Automation Recommendation:** Automated

## SC-005 - Shopper Redeems Eligible Coupons at Checkout

### T-029 - Display eligible coupon in checkout flow
- **Story Link:** SC-005
- **Traceability:** FR-005, AC-003, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Checkout
- **Data Requirements:** Authenticated member `LM-1001`; qualifying cart; coupon `PET20`
- **Preconditions:** Member is recognized in checkout and coupon is eligible
- **Test Steps:** 1) Open checkout with qualifying cart. 2) Inspect coupon section.
- **Given** a loyalty member qualifies for a coupon during checkout
- **When** the checkout experience renders
- **Then** the eligible coupon appears in the purchase flow with clear status and value
- **Postconditions:** Checkout impression event is captured
- **Pass/Fail Criteria:** Pass if the coupon is visible only when the cart qualifies
- **Automation Recommendation:** Critical-path

### T-030 - Apply coupon with one-step action and refresh totals
- **Story Link:** SC-005
- **Traceability:** FR-005, FR-008, AC-008, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Checkout
- **Data Requirements:** Cart subtotal meeting minimum threshold; coupon `PET20`
- **Preconditions:** Coupon is displayed as active
- **Test Steps:** 1) Click Apply or trigger auto-apply. 2) Review order totals and coupon state.
- **Given** a displayed coupon is valid for the cart
- **When** the shopper chooses to use it
- **Then** the coupon is applied with minimal manual effort and checkout totals refresh immediately
- **Postconditions:** Coupon state changes to applied
- **Pass/Fail Criteria:** Pass if the correct discount appears and totals recalculate once
- **Automation Recommendation:** Critical-path

### T-031 - Explain invalid coupon state when conditions are not met
- **Story Link:** SC-005
- **Traceability:** FR-005, AC-006, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Checkout
- **Data Requirements:** Cart below minimum spend or missing required category
- **Preconditions:** Coupon is associated with the member but cart does not qualify
- **Test Steps:** 1) Open checkout with non-qualifying cart. 2) Inspect coupon state message.
- **Given** a coupon is not valid for the current cart
- **When** the shopper views the coupon in checkout
- **Then** the system explains why the coupon cannot be redeemed in plain language
- **Postconditions:** Coupon remains unapplied
- **Pass/Fail Criteria:** Pass if the invalid reason is specific and no discount is taken
- **Automation Recommendation:** Critical-path

### T-032 - Revoke applied discount when cart changes invalidate the coupon
- **Story Link:** SC-005
- **Traceability:** FR-005, FR-008
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Checkout
- **Data Requirements:** Cart initially qualifies, then item removal breaks rule
- **Preconditions:** Coupon has already been applied successfully
- **Test Steps:** 1) Apply coupon. 2) Remove qualifying item or lower subtotal. 3) Review recalculated state.
- **Given** a shopper changes the cart after applying a coupon
- **When** the updated cart no longer meets coupon conditions
- **Then** the discount is removed and the checkout explains that eligibility was lost
- **Postconditions:** Order totals return to valid values
- **Pass/Fail Criteria:** Pass if no invalid discount persists after cart change
- **Automation Recommendation:** Automated

### T-033 - Resolve multiple eligible checkout coupons according to priority
- **Story Link:** SC-005
- **Traceability:** FR-005, FR-001 integration
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Checkout
- **Data Requirements:** Cart qualifies for `PET20` and `LOYALTYBONUS`
- **Preconditions:** Eligibility prioritization is configured
- **Test Steps:** 1) Open checkout with multi-match cart. 2) Review displayed coupon(s). 3) Apply selected offer.
- **Given** a shopper qualifies for multiple coupons in checkout
- **When** coupon presentation is built
- **Then** the prioritized offer is surfaced clearly and incompatible combinations are prevented
- **Postconditions:** Selected coupon remains the only applied discount
- **Pass/Fail Criteria:** Pass if priority rules are honored and totals stay accurate
- **Automation Recommendation:** Automated

### T-034 - Recover gracefully from pricing or coupon-apply service timeout
- **Story Link:** SC-005
- **Traceability:** FR-005, FR-008
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Checkout
- **Data Requirements:** Simulated coupon-apply timeout during checkout
- **Preconditions:** Timeout handling and retry messaging are enabled
- **Test Steps:** 1) Attempt to apply coupon while service times out. 2) Review user message and system state. 3) Retry when service recovers.
- **Given** checkout cannot complete a coupon application due to a transient service failure
- **When** the shopper attempts to redeem the coupon
- **Then** the system shows a recoverable error, leaves totals unchanged, and allows retry without duplicate application
- **Postconditions:** Successful retry applies coupon once
- **Pass/Fail Criteria:** Pass if the checkout remains usable and no incorrect charges occur
- **Automation Recommendation:** Automated

### T-035 - Emit checkout impression and redemption events to analytics
- **Story Link:** SC-005
- **Traceability:** FR-005, FR-010, SC-010 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Checkout and analytics
- **Data Requirements:** Checkout session with one impression and one redemption action
- **Preconditions:** Analytics event pipeline is available
- **Test Steps:** 1) Open checkout. 2) Apply coupon. 3) Validate recorded events.
- **Given** a shopper sees and redeems a coupon at checkout
- **When** the events are published
- **Then** impression and redemption records contain the correct channel, coupon, and order attributes
- **Postconditions:** Reporting can count checkout-assisted redemptions
- **Pass/Fail Criteria:** Pass if recorded events match the actual user journey
- **Automation Recommendation:** Automated

## SC-006 - Marketer Adds Smart Coupons to Targeted Campaigns

### T-036 - Add smart coupon reference to a targeted campaign
- **Story Link:** SC-006
- **Traceability:** FR-006, AC-004, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Campaigns
- **Data Requirements:** Targeted campaign draft; selectable coupon token `PET20`
- **Preconditions:** Campaign marketer has campaign-edit permission
- **Test Steps:** 1) Open campaign builder. 2) Choose smart coupon content. 3) Save campaign.
- **Given** a marketer configures a targeted campaign
- **When** they choose campaign content
- **Then** they can add a smart coupon reference instead of a single generic offer
- **Postconditions:** Campaign stores coupon token reference
- **Pass/Fail Criteria:** Pass if campaign content resolves to the selected coupon token
- **Automation Recommendation:** Critical-path

### T-037 - Resolve recipient-specific coupon at send time
- **Story Link:** SC-006
- **Traceability:** FR-006, FR-003, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Campaigns
- **Data Requirements:** Audience with members `LM-1001` and `LM-1002`; different eligibility outcomes
- **Preconditions:** Campaign send-time resolution is enabled
- **Test Steps:** 1) Launch campaign. 2) Inspect resolved content for both recipients.
- **Given** a campaign targets an eligible loyalty audience
- **When** campaign content is generated at send time
- **Then** each recipient receives coupon content relevant to that member instead of a single generic coupon
- **Postconditions:** Per-recipient resolution is logged
- **Pass/Fail Criteria:** Pass if members receive different content when their eligibility differs
- **Automation Recommendation:** Critical-path

### T-038 - Preview smart coupon resolution before campaign launch
- **Story Link:** SC-006
- **Traceability:** FR-006, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Campaigns
- **Data Requirements:** Campaign draft with coupon token and preview test member
- **Preconditions:** Preview environment is available
- **Test Steps:** 1) Open campaign preview. 2) Select test member. 3) Review resolved coupon block.
- **Given** a marketer prepares a campaign
- **When** they preview the smart coupon content
- **Then** they can confirm that the expected coupon resolves before launch
- **Postconditions:** Preview does not trigger actual send
- **Pass/Fail Criteria:** Pass if preview output matches expected member eligibility
- **Automation Recommendation:** Manual

### T-039 - Block campaign launch when coupon token cannot resolve
- **Story Link:** SC-006
- **Traceability:** FR-006, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Campaigns
- **Data Requirements:** Campaign draft referencing deleted or unpublished coupon token
- **Preconditions:** Validation runs before campaign launch
- **Test Steps:** 1) Attempt to launch campaign with unresolved token. 2) Review validation output.
- **Given** a campaign references invalid smart coupon content
- **When** the marketer validates or launches the campaign
- **Then** the system blocks launch and identifies the unresolved token
- **Postconditions:** Campaign remains in draft or validation-failed status
- **Pass/Fail Criteria:** Pass if no invalid campaign send can proceed
- **Automation Recommendation:** Critical-path

### T-040 - Handle audience members with no eligible coupon during campaign send
- **Story Link:** SC-006
- **Traceability:** FR-006, FR-003
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Campaigns
- **Data Requirements:** Audience containing one eligible and one non-eligible member
- **Preconditions:** Fallback campaign behavior is defined
- **Test Steps:** 1) Launch campaign. 2) Compare send outputs by recipient.
- **Given** some recipients in the campaign audience do not qualify for a coupon
- **When** send-time resolution occurs
- **Then** the system applies the approved fallback behavior and does not send invalid coupon content to non-eligible recipients
- **Postconditions:** Resolution logs distinguish eligible and fallback recipients
- **Pass/Fail Criteria:** Pass if non-eligible recipients do not receive incorrect coupon details
- **Automation Recommendation:** Automated

### T-041 - Respect admin-disabled campaign channel at launch time
- **Story Link:** SC-006
- **Traceability:** FR-006, SC-011 integration
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Campaigns
- **Data Requirements:** Campaign channel disabled by admin configuration
- **Preconditions:** Admin has disabled Campaigns in rollout settings
- **Test Steps:** 1) Attempt campaign launch. 2) Review system response and audit log.
- **Given** the campaign channel is disabled by configuration
- **When** a marketer attempts to launch a smart coupon campaign
- **Then** the launch is blocked and the system records that the channel is not currently enabled
- **Postconditions:** No campaign coupon send occurs
- **Pass/Fail Criteria:** Pass if configuration guardrail overrides the launch attempt
- **Automation Recommendation:** Automated

### T-042 - Track campaign impression and engagement events
- **Story Link:** SC-006
- **Traceability:** FR-006, FR-010, SC-010 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Campaigns and analytics
- **Data Requirements:** Campaign send with open/click activity
- **Preconditions:** Analytics tagging is enabled for campaign coupon content
- **Test Steps:** 1) Send campaign. 2) Simulate recipient open and click. 3) Validate analytics output.
- **Given** campaign recipients receive and interact with smart coupon content
- **When** impression and engagement events are emitted
- **Then** the analytics pipeline records events with campaign and coupon attribution
- **Postconditions:** Stakeholder reporting can segment campaign performance
- **Pass/Fail Criteria:** Pass if event counts and attribution are accurate
- **Automation Recommendation:** Automated

## SC-007 - Member Discovers Active Coupons In-App

### T-043 - Show active personalized coupons on in-app entry
- **Story Link:** SC-007
- **Traceability:** FR-007, AC-005, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** In-App
- **Data Requirements:** Authenticated member `LM-1001`; active coupons `PET20`, `GROOM5`
- **Preconditions:** In-app loyalty surface is available and member is authenticated
- **Test Steps:** 1) Open the in-app loyalty experience. 2) Review coupon feed.
- **Given** a member has eligible coupons
- **When** the in-app loyalty experience loads
- **Then** active personalized coupons are visible without searching across other channels
- **Postconditions:** In-app impression event is captured
- **Pass/Fail Criteria:** Pass if relevant active coupons appear on initial view
- **Automation Recommendation:** Critical-path

### T-044 - Display clear status, value, validity, and redemption information
- **Story Link:** SC-007
- **Traceability:** FR-007, FR-008, AC-006, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** In-App
- **Data Requirements:** Coupon cards in states Active, Expiring Soon, Redeemed
- **Preconditions:** Member has coupons in different statuses
- **Test Steps:** 1) Open coupon list. 2) Open coupon detail. 3) Verify labels and details.
- **Given** the member views coupon cards or details
- **When** the coupon is displayed
- **Then** the system clearly labels status, value, validity, and key redemption information
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if the member can distinguish usable versus non-usable coupons
- **Automation Recommendation:** Manual

### T-045 - Route in-app action into supported redemption flow
- **Story Link:** SC-007
- **Traceability:** FR-008, AC-008, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** In-App to redemption target
- **Data Requirements:** Active coupon with action button configured
- **Preconditions:** Redemption target exists for the coupon
- **Test Steps:** 1) Tap coupon action. 2) Verify handoff to checkout or supported flow.
- **Given** a member wants to use an active coupon
- **When** they select the coupon action
- **Then** the experience routes them into the supported redemption path without requiring out-of-scope channels
- **Postconditions:** Engagement event is captured
- **Pass/Fail Criteria:** Pass if the in-app route preserves coupon context
- **Automation Recommendation:** Critical-path

### T-046 - Show empty state when member has no eligible coupons
- **Story Link:** SC-007
- **Traceability:** FR-007, AC-005
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** In-App
- **Data Requirements:** Authenticated member with no active coupon assignments
- **Preconditions:** Member account exists but has zero active coupons
- **Test Steps:** 1) Open in-app loyalty experience. 2) Review default state.
- **Given** a member has no eligible coupons
- **When** they open the in-app coupon area
- **Then** the system shows a clear empty state instead of stale, expired, or unrelated coupon content
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if no incorrect coupons are displayed
- **Automation Recommendation:** Automated

### T-047 - Update label when coupon expires at boundary time
- **Story Link:** SC-007
- **Traceability:** FR-007, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** In-App
- **Data Requirements:** Coupon expiring at 23:59 with app session spanning expiration
- **Preconditions:** Time-travel or controlled test clock is available
- **Test Steps:** 1) Open coupon before expiry. 2) Advance clock past expiry. 3) Refresh view.
- **Given** a coupon reaches its expiration boundary while the member is viewing it
- **When** the app refreshes the coupon state
- **Then** the label changes from Active to Expired and the action becomes unavailable
- **Postconditions:** Expired coupon remains visible only with non-redeemable status
- **Pass/Fail Criteria:** Pass if state transitions correctly at the validity boundary
- **Automation Recommendation:** Automated

### T-048 - Prevent action on already redeemed coupon
- **Story Link:** SC-007
- **Traceability:** FR-008, AC-006
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** In-App
- **Data Requirements:** Coupon already redeemed in Checkout
- **Preconditions:** Redemption status sync from other channels is available
- **Test Steps:** 1) Open in-app coupon list after checkout redemption. 2) Select the coupon.
- **Given** a coupon has already been redeemed in another supported channel
- **When** the member views it in-app
- **Then** the coupon is labeled Redeemed and no active redemption action is offered
- **Postconditions:** No duplicate redemption attempt is possible
- **Pass/Fail Criteria:** Pass if stale active state is not shown
- **Automation Recommendation:** Automated

### T-049 - Keep in-app coupon details consistent with email and checkout
- **Story Link:** SC-007
- **Traceability:** FR-007, FR-009, SC-009 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** In-App, Email, Checkout
- **Data Requirements:** Same coupon published to all three channels
- **Preconditions:** Coupon is visible in multiple channels for the same member
- **Test Steps:** 1) View coupon in-app. 2) Compare title, value, validity, and conditions against email and checkout.
- **Given** the same coupon appears in multiple channels
- **When** a member compares in-app details with other supported channels
- **Then** key offer details remain consistent
- **Postconditions:** Any mismatch should trigger consistency follow-up through SC-009
- **Pass/Fail Criteria:** Pass if canonical fields match across channels
- **Automation Recommendation:** Manual

## SC-008 - Member Redeems Coupons With Minimal Effort

### T-050 - Display coupon status and usage conditions before redemption
- **Story Link:** SC-008
- **Traceability:** FR-008, AC-006, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Shared status model / all supported channels
- **Data Requirements:** Active coupon with conditions, validity dates, and channel availability
- **Preconditions:** Coupon is visible in Email, Checkout, or In-App
- **Test Steps:** 1) View coupon in a supported channel. 2) Inspect status and usage details.
- **Given** a member views a supported coupon
- **When** the coupon is displayed
- **Then** the member can see current status, validity period, and usage conditions before redeeming it
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if status data is complete and channel-appropriate
- **Automation Recommendation:** Critical-path

### T-051 - Complete redemption from email-to-checkout path with minimal effort
- **Story Link:** SC-008
- **Traceability:** FR-008, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email to Checkout
- **Data Requirements:** Email CTA linked to checkout with coupon context
- **Preconditions:** Member received coupon email and has qualifying cart
- **Test Steps:** 1) Click email CTA. 2) Land in checkout. 3) Apply or auto-apply coupon.
- **Given** a member chooses to redeem a supported coupon from email
- **When** they complete the redemption action in checkout
- **Then** the flow requires minimal additional data entry and the coupon is successfully applied
- **Postconditions:** Coupon status changes to redeemed or applied
- **Pass/Fail Criteria:** Pass if the member can redeem without unsupported-channel dependency
- **Automation Recommendation:** Critical-path

### T-052 - Update coupon state across channels immediately after redemption
- **Story Link:** SC-008
- **Traceability:** FR-008, story AC-2
- **Scenario Type:** Integration
- **Priority:** P0
- **Channel:** Checkout, Email, In-App
- **Data Requirements:** One coupon redeemed in Checkout while also visible in Email and In-App
- **Preconditions:** Status synchronization job or eventing is active
- **Test Steps:** 1) Redeem coupon in checkout. 2) Refresh in-app and email-linked coupon views.
- **Given** a coupon is redeemed in one supported channel
- **When** status synchronization completes
- **Then** other channels show Redeemed or Applied instead of Active
- **Postconditions:** Duplicate redemption is prevented everywhere
- **Pass/Fail Criteria:** Pass if cross-channel state converges within the expected synchronization window
- **Automation Recommendation:** Critical-path

### T-053 - Block duplicate redemption of an already used coupon
- **Story Link:** SC-008
- **Traceability:** FR-008, story AC-2
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Shared redemption service
- **Data Requirements:** Coupon already marked redeemed
- **Preconditions:** Redemption service has recorded prior successful redemption
- **Test Steps:** 1) Attempt second redemption. 2) Review user message and service response.
- **Given** a member tries to redeem a coupon that has already been used
- **When** the redemption request is submitted
- **Then** the system blocks the request, preserves prior state, and explains that the coupon is no longer available
- **Postconditions:** Original redemption record remains unchanged
- **Pass/Fail Criteria:** Pass if no duplicate discount or status reset occurs
- **Automation Recommendation:** Critical-path

### T-054 - Prevent unsupported channel dependency during redemption
- **Story Link:** SC-008
- **Traceability:** FR-008, AC-008, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** All supported channels
- **Data Requirements:** Redemption flows for Email, Checkout, and In-App
- **Preconditions:** Out-of-scope channels remain disabled
- **Test Steps:** 1) Walk each redemption path. 2) Verify no SMS, push, or native mobile dependency is introduced.
- **Given** a member redeems a coupon in a supported channel
- **When** the redemption completes
- **Then** the process never requires an unsupported channel handoff
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if all supported flows remain self-contained
- **Automation Recommendation:** Automated

### T-055 - Resolve concurrent redemption attempts safely
- **Story Link:** SC-008
- **Traceability:** FR-008
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Shared redemption service
- **Data Requirements:** Same coupon submitted simultaneously from two sessions
- **Preconditions:** Concurrency controls are enabled
- **Test Steps:** 1) Submit two redemption requests at nearly the same time. 2) Compare responses and resulting state.
- **Given** the same coupon is redeemed concurrently from multiple sessions
- **When** both requests reach the redemption service
- **Then** only one request succeeds and the other receives a clear already-used or conflict message
- **Postconditions:** Final coupon state is consistent
- **Pass/Fail Criteria:** Pass if double redemption cannot occur
- **Automation Recommendation:** Automated

### T-056 - Log redemption attempts, failures, and outcomes for support and analytics
- **Story Link:** SC-008
- **Traceability:** FR-008, FR-010
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Shared redemption service and analytics/support logs
- **Data Requirements:** One successful redemption and one rejected redemption attempt
- **Preconditions:** Logging and analytics sinks are enabled
- **Test Steps:** 1) Perform successful redemption. 2) Attempt duplicate redemption. 3) Review logs/events.
- **Given** redemption requests succeed or fail
- **When** outcomes are recorded
- **Then** the system logs attempts, statuses, error reasons, and timestamps for analytics and support use
- **Postconditions:** Support staff can trace the redemption lifecycle
- **Pass/Fail Criteria:** Pass if outcome logging covers both success and failure states
- **Automation Recommendation:** Automated

## SC-009 - System Keeps Offer Details Consistent Across Channels

### T-057 - Read canonical offer details from one source across channels
- **Story Link:** SC-009
- **Traceability:** FR-009, AC-007, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email, Checkout, Campaigns, In-App
- **Data Requirements:** Published coupon `PET20` with canonical title, value, validity, conditions
- **Preconditions:** Same coupon is available in multiple channels
- **Test Steps:** 1) Retrieve coupon in each channel. 2) Compare against canonical record.
- **Given** a coupon is published
- **When** supported channels retrieve coupon data
- **Then** each channel reads value, validity, and conditions from the canonical source record
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if all channel views match the canonical record exactly
- **Automation Recommendation:** Critical-path

### T-058 - Detect and flag mismatched offer details
- **Story Link:** SC-009
- **Traceability:** FR-009, story AC-2
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Consistency validation service
- **Data Requirements:** Simulated channel cache mismatch for validity end date
- **Preconditions:** Reconciliation or validation job is enabled
- **Test Steps:** 1) Introduce mismatch in one channel copy. 2) Run consistency check. 3) Review alert.
- **Given** the same coupon appears with different details in two supported channels
- **When** a consistency check runs
- **Then** the mismatch is detected, flagged, and routed to operations alerting
- **Postconditions:** Drift record is stored for remediation
- **Pass/Fail Criteria:** Pass if even a single key-field mismatch is flagged
- **Automation Recommendation:** Critical-path

### T-059 - Propagate approved coupon update without conflicting versions
- **Story Link:** SC-009
- **Traceability:** FR-009, FR-002, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Canonical content to all supported channels
- **Data Requirements:** Coupon `PET20 v1`; updated `v2` condition text
- **Preconditions:** Coupon is already live in more than one channel
- **Test Steps:** 1) Publish updated coupon version. 2) Refresh channel payloads. 3) Compare versions.
- **Given** a marketer updates an approved coupon
- **When** the update is published
- **Then** supported channels receive the revised canonical details without conflicting live versions
- **Postconditions:** Previous version remains historical only
- **Pass/Fail Criteria:** Pass if channels converge on the same new version
- **Automation Recommendation:** Critical-path

### T-060 - Recover stale channel cache to canonical state
- **Story Link:** SC-009
- **Traceability:** FR-009, story AC-2
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Consistency validation service
- **Data Requirements:** One channel intentionally serving stale cached value
- **Preconditions:** Cache refresh or republish mechanism is available
- **Test Steps:** 1) Detect stale cache. 2) Trigger recovery action. 3) Re-compare channel state.
- **Given** one supported channel serves stale coupon data
- **When** recovery or republish is triggered
- **Then** the channel is refreshed back to canonical content and the alert is cleared
- **Postconditions:** Drift issue is resolved and auditable
- **Pass/Fail Criteria:** Pass if stale values no longer appear after remediation
- **Automation Recommendation:** Automated

### T-061 - Preserve consistency for currency and rounding presentation
- **Story Link:** SC-009
- **Traceability:** FR-009, AC-006
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Email, Checkout, In-App
- **Data Requirements:** Coupon with percentage discount and currency amount edge values
- **Preconditions:** Channel formatting rules are configured
- **Test Steps:** 1) Publish coupon with boundary monetary values. 2) Compare display across channels.
- **Given** a coupon uses price-sensitive value formatting
- **When** it is displayed in multiple channels
- **Then** rounding, symbols, and displayed value remain consistent with the canonical definition
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if no customer-visible monetary mismatch exists
- **Automation Recommendation:** Manual

### T-062 - Ignore disabled channels during consistency validation scope
- **Story Link:** SC-009
- **Traceability:** FR-009, SC-011 integration
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Consistency validation service
- **Data Requirements:** In-App channel disabled by admin while Email and Checkout remain enabled
- **Preconditions:** Channel enablement config is current
- **Test Steps:** 1) Disable one channel. 2) Run consistency validation. 3) Review results.
- **Given** a supported channel is intentionally disabled
- **When** the consistency job evaluates active coupon publication
- **Then** only enabled channels are included in drift comparisons
- **Postconditions:** No false-positive mismatch alert is created for the disabled channel
- **Pass/Fail Criteria:** Pass if validation scope respects channel enablement
- **Automation Recommendation:** Automated

### T-063 - Validate end-to-end consistency after multi-channel publish and redemption
- **Story Link:** SC-009
- **Traceability:** FR-009, SC-003 and SC-008 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Email, Checkout, In-App
- **Data Requirements:** Same coupon published then redeemed in one channel
- **Preconditions:** Publish and redemption flows are operational
- **Test Steps:** 1) Publish coupon to multiple channels. 2) Redeem it in checkout. 3) Re-check all channels.
- **Given** a coupon is both distributed and redeemed across channels
- **When** consistency validation runs after state changes
- **Then** key offer details and redemption state remain aligned everywhere
- **Postconditions:** Drift monitoring stays green
- **Pass/Fail Criteria:** Pass if content and state are synchronized after the full lifecycle
- **Automation Recommendation:** Critical-path

## SC-010 - Stakeholder Reviews Smart Coupon Performance

### T-064 - Capture impression, engagement, and redemption events from supported channels
- **Story Link:** SC-010
- **Traceability:** FR-010, AC-010, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Email, Checkout, Campaigns, In-App
- **Data Requirements:** One member journey per channel including view, click/engage, and redeem actions
- **Preconditions:** Event taxonomy and analytics pipeline are deployed
- **Test Steps:** 1) Perform the expected actions in each channel. 2) Query analytics pipeline outputs.
- **Given** smart coupons are shown and used in supported channels
- **When** the member interacts with them
- **Then** impression, engagement, and redemption events are captured with channel identity
- **Postconditions:** Analytics warehouse contains attributed events
- **Pass/Fail Criteria:** Pass if required event types exist for each channel
- **Automation Recommendation:** Critical-path

### T-065 - Display channel-segmented reporting output
- **Story Link:** SC-010
- **Traceability:** FR-010, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P0
- **Channel:** Reporting dashboard / export
- **Data Requirements:** Seeded events across all four channels
- **Preconditions:** Dashboard or export view is available to stakeholders
- **Test Steps:** 1) Open reporting output. 2) Filter or group by channel. 3) Review totals.
- **Given** stakeholders review Smart Coupon performance
- **When** they access reporting outputs
- **Then** they can evaluate metrics separately for Email, Checkout, Campaigns, and In-App
- **Postconditions:** None
- **Pass/Fail Criteria:** Pass if each supported channel has distinct reporting rows or filters
- **Automation Recommendation:** Manual

### T-066 - Calculate redemption rate and engagement inputs for launch metrics
- **Story Link:** SC-010
- **Traceability:** FR-010, story AC-3
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Reporting dashboard / warehouse
- **Data Requirements:** Seed data producing known redemption-rate and engagement-rate outputs
- **Preconditions:** Metric formulas are documented and configured
- **Test Steps:** 1) Load known event dataset. 2) Run metric calculation. 3) Compare result to expected values.
- **Given** the feature is live and metrics are reviewed
- **When** redemption rate and engagement uplift are calculated
- **Then** the output supports evaluation against the 45% redemption and 60% engagement targets
- **Postconditions:** Metric audit worksheet can be retained
- **Pass/Fail Criteria:** Pass if reported calculations match the expected dataset math
- **Automation Recommendation:** Automated

### T-067 - Reject or quarantine events missing channel attribution
- **Story Link:** SC-010
- **Traceability:** FR-010, story AC-1
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Analytics ingestion
- **Data Requirements:** Event payload missing `channel`
- **Preconditions:** Data-quality validation is enabled
- **Test Steps:** 1) Submit malformed event. 2) Review ingestion result and error log.
- **Given** an analytics event lacks required channel attribution
- **When** the event reaches the pipeline
- **Then** the pipeline rejects or quarantines the event and records a data-quality issue
- **Postconditions:** Bad event does not contaminate official reporting
- **Pass/Fail Criteria:** Pass if invalid events are isolated and visible to operations
- **Automation Recommendation:** Automated

### T-068 - De-duplicate repeated analytics events
- **Story Link:** SC-010
- **Traceability:** FR-010
- **Scenario Type:** Edge Case
- **Priority:** P1
- **Channel:** Analytics ingestion
- **Data Requirements:** Duplicate event IDs for the same user action
- **Preconditions:** Event deduplication keys are configured
- **Test Steps:** 1) Publish duplicate event payloads. 2) Review warehouse counts.
- **Given** the same customer action is reported more than once
- **When** analytics ingestion processes the events
- **Then** only one counted event remains in reporting outputs
- **Postconditions:** Duplicate is logged for investigation if needed
- **Pass/Fail Criteria:** Pass if dashboards do not overcount the duplicated action
- **Automation Recommendation:** Automated

### T-069 - Reconcile late-arriving events into channel totals
- **Story Link:** SC-010
- **Traceability:** FR-010
- **Scenario Type:** Edge Case
- **Priority:** P2
- **Channel:** Analytics pipeline
- **Data Requirements:** Delayed redemption event arriving after initial daily load
- **Preconditions:** Late-event handling is supported
- **Test Steps:** 1) Process initial reporting batch. 2) Send delayed event. 3) Run reconciliation.
- **Given** some coupon events arrive after the initial reporting window
- **When** reconciliation runs
- **Then** channel totals update correctly without double counting prior data
- **Postconditions:** Audit notes show late-event adjustment
- **Pass/Fail Criteria:** Pass if reporting reflects the corrected totals after reconciliation
- **Automation Recommendation:** Automated

### T-070 - Validate end-to-end reporting from channel action to stakeholder dashboard
- **Story Link:** SC-010
- **Traceability:** FR-010, SC-004 to SC-007 integration
- **Scenario Type:** Integration
- **Priority:** P0
- **Channel:** Email, Checkout, Campaigns, In-App to dashboard
- **Data Requirements:** One full user journey per channel
- **Preconditions:** Channel instrumentation and dashboard are available in the same test environment
- **Test Steps:** 1) Execute channel actions. 2) Confirm events in pipeline. 3) Confirm aggregated dashboard results.
- **Given** a member interacts with smart coupons across channels
- **When** stakeholders later review the dashboard
- **Then** the dashboard reflects the underlying events accurately by channel and by metric type
- **Postconditions:** Full data lineage is verified
- **Pass/Fail Criteria:** Pass if dashboard totals reconcile to raw event counts
- **Automation Recommendation:** Critical-path

## SC-011 - Admin Configures Smart Coupon Rules and Channels

### T-071 - Enable or disable supported channels through admin controls
- **Story Link:** SC-011
- **Traceability:** FR-003, AC-009, story AC-1
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Admin configuration
- **Data Requirements:** Channel settings for Email, Checkout, Campaigns, In-App
- **Preconditions:** Admin user is authenticated and authorized
- **Test Steps:** 1) Open Smart Coupon settings. 2) Toggle supported channels. 3) Save configuration.
- **Given** an admin manages Smart Coupon settings
- **When** they update channel enablement
- **Then** the system allows configuration only for the four supported v1.0 channels
- **Postconditions:** Updated settings are stored with timestamp
- **Pass/Fail Criteria:** Pass if only supported channels are available for enablement
- **Automation Recommendation:** Automated

### T-072 - Save rollout guardrail changes with audit trail
- **Story Link:** SC-011
- **Traceability:** FR-003, FR-010, story AC-2
- **Scenario Type:** Happy Path
- **Priority:** P1
- **Channel:** Admin configuration
- **Data Requirements:** Change to availability window, rollout percentage, or guardrail setting
- **Preconditions:** Existing configuration record is present
- **Test Steps:** 1) Update approved rollout parameter. 2) Save configuration. 3) Review audit history.
- **Given** an admin adjusts rollout settings
- **When** the new settings are saved
- **Then** the system persists the change with who changed it, what changed, and when
- **Postconditions:** New settings are available to dependent services
- **Pass/Fail Criteria:** Pass if audit trail is complete and configuration takes effect without deployment
- **Automation Recommendation:** Automated

### T-073 - Block activation of SMS, Mobile App, or Push Notification
- **Story Link:** SC-011
- **Traceability:** FR-003, AC-009, story AC-3
- **Scenario Type:** Error / Negative
- **Priority:** P0
- **Channel:** Admin configuration
- **Data Requirements:** Attempt to enable `SMS`, `Mobile App`, or `Push Notification`
- **Preconditions:** Out-of-scope channel validation is active
- **Test Steps:** 1) Attempt to add unsupported channel. 2) Save settings.
- **Given** an admin attempts to activate an out-of-scope channel
- **When** the configuration is saved
- **Then** the system blocks the change and explains that the channel is out of scope for v1.0
- **Postconditions:** Unsupported channel remains disabled
- **Pass/Fail Criteria:** Pass if no unsupported channel can be persisted
- **Automation Recommendation:** Critical-path

### T-074 - Reject invalid rollout parameter combinations
- **Story Link:** SC-011
- **Traceability:** FR-003, story AC-2
- **Scenario Type:** Error / Negative
- **Priority:** P1
- **Channel:** Admin configuration
- **Data Requirements:** Invalid window dates, negative rollout percentage, blank reason for override
- **Preconditions:** Validation rules are enabled
- **Test Steps:** 1) Enter invalid parameters. 2) Save configuration. 3) Review validation feedback.
- **Given** an admin enters invalid rollout settings
- **When** they attempt to save the configuration
- **Then** the system rejects the change and identifies the specific invalid values
- **Postconditions:** Last valid configuration remains active
- **Pass/Fail Criteria:** Pass if invalid settings do not affect live behavior
- **Automation Recommendation:** Automated

### T-075 - Restrict configuration changes by unauthorized users
- **Story Link:** SC-011
- **Traceability:** FR-003, security control
- **Scenario Type:** Security / Permission
- **Priority:** P0
- **Channel:** Admin configuration
- **Data Requirements:** Non-admin user account and admin-only settings page
- **Preconditions:** Role-based access to settings UI/API is configured
- **Test Steps:** 1) Sign in as non-admin user. 2) Attempt to view or modify Smart Coupon settings.
- **Given** a user without admin rights accesses Smart Coupon configuration
- **When** they attempt to change rules or channels
- **Then** access is denied and no configuration update is stored
- **Postconditions:** Security event is logged if required
- **Pass/Fail Criteria:** Pass if unauthorized users cannot read/write protected settings
- **Automation Recommendation:** Critical-path

### T-076 - Roll back to last known good configuration after bad change
- **Story Link:** SC-011
- **Traceability:** FR-003, FR-009
- **Scenario Type:** Recovery
- **Priority:** P1
- **Channel:** Admin configuration
- **Data Requirements:** One valid config version and one newly saved bad-but-allowed config version
- **Preconditions:** Versioned configuration history is available
- **Test Steps:** 1) Save a problematic configuration. 2) Trigger rollback to prior version. 3) Validate dependent services.
- **Given** a newly saved configuration causes operational issues
- **When** an admin restores the previous version
- **Then** the last known good configuration becomes active again with full audit trace
- **Postconditions:** Dependent services resume correct behavior
- **Pass/Fail Criteria:** Pass if rollback is complete and no unsupported settings remain active
- **Automation Recommendation:** Manual

### T-077 - Propagate config changes to dependent campaign, consistency, and reporting services
- **Story Link:** SC-011
- **Traceability:** FR-003, FR-009, FR-010, SC-006/SC-009/SC-010 integration
- **Scenario Type:** Integration
- **Priority:** P1
- **Channel:** Admin configuration to downstream services
- **Data Requirements:** Toggle Campaigns off, keep Email and Checkout on
- **Preconditions:** Dependent services poll or subscribe to config changes
- **Test Steps:** 1) Save config update. 2) Attempt campaign launch. 3) Run consistency and reporting checks.
- **Given** admin settings are updated
- **When** downstream services consume the new configuration
- **Then** campaign delivery, consistency validation, and reporting scope reflect the updated channel state
- **Postconditions:** System behavior matches current governance settings
- **Pass/Fail Criteria:** Pass if downstream services use the new config without manual restarts
- **Automation Recommendation:** Automated

## 4. Cross-Cutting Validation Scenarios

### T-078 - Protect coupon authoring and audit inputs from malicious content injection
- **Story Link:** SC-002, SC-011
- **Traceability:** FR-002, security control
- **Scenario Type:** Security / Negative
- **Priority:** P0
- **Channel:** Admin content and configuration
- **Data Requirements:** Script tag, SQL-like string, and over-encoded text in coupon fields
- **Preconditions:** Input validation and output encoding are enabled
- **Test Steps:** 1) Submit malicious input through coupon authoring and admin APIs. 2) Review saved data and rendered outputs.
- **Given** a privileged user enters malicious content into coupon fields
- **When** the system validates and stores the data
- **Then** unsafe input is rejected or neutralized and no executable payload is rendered downstream
- **Postconditions:** Security logs capture the rejected attempt if policy requires
- **Pass/Fail Criteria:** Pass if no stored or reflected injection is possible
- **Automation Recommendation:** Automated

### T-079 - Maintain coupon eligibility and checkout responsiveness under launch-day load
- **Story Link:** SC-001, SC-005, SC-008
- **Traceability:** Launch readiness / performance
- **Scenario Type:** Performance
- **Priority:** P1
- **Channel:** Eligibility and Checkout
- **Data Requirements:** Concurrent member sessions and coupon evaluations sized to expected launch traffic
- **Preconditions:** Load environment mirrors launch architecture
- **Test Steps:** 1) Run concurrent eligibility and checkout redemption workload. 2) Review response times and error rates.
- **Given** launch-day traffic increases coupon requests across eligibility and checkout
- **When** the system processes concurrent load
- **Then** response times remain acceptable for customer-facing flows and error rates stay within release tolerance
- **Postconditions:** Performance observations are recorded for go-live review
- **Pass/Fail Criteria:** Pass if no material degradation blocks coupon discovery or redemption
- **Automation Recommendation:** Automated

### T-080 - Recover multi-channel delivery after temporary adapter outage
- **Story Link:** SC-003, SC-004, SC-007
- **Traceability:** FR-003, recovery
- **Scenario Type:** Recovery / Integration
- **Priority:** P1
- **Channel:** Email and In-App adapters
- **Data Requirements:** One distribution batch with one adapter intentionally unavailable
- **Preconditions:** Retry and dead-letter handling are configured
- **Test Steps:** 1) Run delivery with adapter outage. 2) Restore adapter. 3) Reprocess failed items.
- **Given** one channel adapter fails during coupon distribution
- **When** the adapter becomes available again
- **Then** only failed deliveries are replayed successfully and already completed channel publishes are not duplicated
- **Postconditions:** Recovery audit trail is complete
- **Pass/Fail Criteria:** Pass if end users receive one valid delivery after recovery
- **Automation Recommendation:** Automated

### T-081 - Preserve data completeness through analytics pipeline restart
- **Story Link:** SC-010
- **Traceability:** FR-010, recovery
- **Scenario Type:** Recovery / Negative
- **Priority:** P1
- **Channel:** Analytics ingestion and reporting
- **Data Requirements:** Buffered coupon events during controlled analytics outage
- **Preconditions:** Event queue or replay mechanism exists
- **Test Steps:** 1) Stop analytics consumer. 2) Generate coupon events. 3) Restart consumer. 4) Validate final counts.
- **Given** analytics ingestion is temporarily unavailable
- **When** the consumer restarts and replays buffered events
- **Then** channel metrics are restored without data loss or double counting
- **Postconditions:** Dashboard totals reconcile to raw replayed events
- **Pass/Fail Criteria:** Pass if reporting completeness is retained after restart
- **Automation Recommendation:** Automated

### T-082 - Validate end-to-end release smoke across supported and unsupported channels
- **Story Link:** SC-003, SC-008, SC-011
- **Traceability:** AC-008, AC-009, launch smoke
- **Scenario Type:** Integration / Smoke
- **Priority:** P0
- **Channel:** Email, Checkout, Campaigns, In-App, unsupported channel controls
- **Data Requirements:** One eligible member, one active coupon, admin configuration snapshot
- **Preconditions:** Release candidate environment is deployed
- **Test Steps:** 1) Verify coupon availability in each supported channel. 2) Redeem in one supported flow. 3) Confirm unsupported channels remain unavailable.
- **Given** the Smart Coupon System release candidate is deployed
- **When** smoke validation runs
- **Then** supported channels show the coupon correctly, one redemption path succeeds, analytics start flowing, and SMS/Mobile App/Push remain excluded
- **Postconditions:** Release candidate is marked ready or blocked based on smoke result
- **Pass/Fail Criteria:** Pass if all critical launch controls behave as expected
- **Automation Recommendation:** Critical-path

---

## 5. Channel-Specific Notes

### Email
- Validate rendering in at least one desktop and one mobile email client.
- Confirm CTA links preserve coupon and member attribution.
- Email tests should verify no broken or expired content reaches recipients.

### Checkout
- Validate both manual apply and auto-apply behaviors if both exist.
- Re-test cart recalculation whenever qualifying items or subtotal changes.
- Concurrency and timeout handling are critical because pricing integrity is customer-visible.

### Campaigns
- Preview and launch validation must prevent unresolved coupon tokens.
- Audience-level eligibility differences should be verified with at least two contrasting member profiles.

### In-App
- Status labels must distinguish Active, Expiring, Redeemed, Unavailable, and Empty State scenarios.
- Route validation should confirm handoff to only supported redemption flows.

### Analytics / Reporting
- Verify channel attribution, deduplication, and reconciliation behavior before stakeholder sign-off.
- Dashboard totals should reconcile with raw events for at least one controlled dataset.

---

## 6. Test Data Strategy

- Use anonymized loyalty members with distinct behavior profiles:
  - `LM-1001` pet-category loyalist
  - `LM-1002` incomplete preference profile
  - `LM-1003` multi-match high-value shopper
  - `LM-1004` no-match member
- Use canonical coupon samples:
  - `PET20` active personalized discount
  - `SAVE5` fallback generic-safe offer
  - `FLASH10` expired coupon
  - `LOYALTYBONUS` competing high-priority offer
- Use boundary data:
  - Expiry at midnight / 23:59
  - Maximum title and conditions text
  - Missing channel metadata
  - Duplicate event IDs and concurrent redemption attempts

---

## 7. Execution Sequencing

1. **Smoke / Critical Path (45-60 min)**  
   T-001, T-008, T-015, T-022, T-029, T-036, T-043, T-050, T-057, T-064, T-073, T-082
2. **Story Functional Regression (2-3 hrs)**  
   Remaining happy-path, negative, and edge tests by story
3. **Cross-Story Integration (60-90 min)**  
   T-007, T-014, T-021, T-028, T-035, T-042, T-049, T-052, T-063, T-070, T-077
4. **Recovery / Security / Load (60-90 min)**  
   T-075, T-078, T-079, T-080, T-081

---

## 8. Known Limitations and Assumptions

- The BRD does not define exact numerical non-functional thresholds; performance and load tests should use agreed release-readiness tolerances.
- Native mobile app, SMS, push notifications, and offline redemption are intentionally excluded and should fail scope-protection checks.
- Cross-channel consistency tests validate canonical offer fields, not visual styling consistency.
- Some recovery tests assume replay, retry, and audit capabilities exist in the implementation.

