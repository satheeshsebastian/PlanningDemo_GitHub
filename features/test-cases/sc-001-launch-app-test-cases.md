# Functional Test Cases: Smart Checkout System
## SC-001: Customer Launches Smart Checkout App

**Document ID**: TC-SC-001-MASTER  
**Story**: Customer Launches Smart Checkout App  
**Created**: 2026-06-10  
**Total Test Cases**: 12  

---

## Test Execution Summary

| Category | Count | Priority | Est. Time |
|----------|-------|----------|-----------|
| **Happy Path** | 3 | P0 | 15 min |
| **Error Scenarios** | 5 | P1 | 20 min |
| **Edge Cases** | 3 | P2 | 15 min |
| **Security** | 1 | P1 | 10 min |
| **TOTAL** | 12 | | **60 min** |

---

## Test Cases

### HAPPY PATH TESTS

#### **TC-SC-001-001: App Launches Without Login**
**Acceptance Criterion**: AC-1.1.1 (App displays scanning screen)  
**Test Type**: Functional / Smoke Test  
**Priority**: P0 CRITICAL  
**Automation**: AUTOMATED

**Preconditions**:
- App is installed on iOS 14+ device
- First app launch (clean install)

**Test Steps**:
1. Tap app icon on home screen
2. Wait for app to initialize
3. Verify app displays without login screen

**Given/When/Then**:
```gherkin
Given a customer has downloaded the Smart Checkout app
When the customer opens the app for the first time
Then the app launches successfully
  And the scanning screen is displayed immediately
  And no login form is visible
  And a "Start Scanning" button or similar CTA is visible
```

**Expected Results**:
- ✅ App launches in <3 seconds
- ✅ Scanning screen (camera viewfinder area) visible
- ✅ No login/authentication prompts shown
- ✅ App remains in foreground (no crash)

**Test Data**: None required

**Pass/Fail Criteria**:
- PASS: Scanning screen displays, no login screen
- FAIL: Login screen appears, app crashes, or blank screen

**Notes**: Test on both iOS and Android

---

#### **TC-SC-001-002: Camera Permission Prompt Appears (First Launch)**
**Acceptance Criterion**: AC-1.1.2 (Camera permission prompt)  
**Test Type**: Functional  
**Priority**: P0 CRITICAL  
**Automation**: AUTOMATED

**Preconditions**:
- App is open on scanning screen
- Camera permission NOT yet granted
- First app launch

**Test Steps**:
1. On scanning screen, attempt to scan (or wait 2 seconds)
2. Verify camera permission prompt appears
3. Tap "Allow" button

**Given/When/Then**:
```gherkin
Given the app is open and requesting camera permission for the first time
When the system displays the permission dialog
Then the dialog shows: "Allow '[App]' to access your camera?"
  And buttons are: "Allow" and "Don't Allow"
```

**Expected Results**:
- ✅ Permission dialog appears within 2 seconds of app open
- ✅ Dialog text is clear and readable
- ✅ Tapping "Allow" enables camera access
- ✅ Camera viewfinder becomes visible after permission granted

**Test Data**: N/A

**Pass/Fail Criteria**:
- PASS: Permission dialog displays, camera enables after "Allow"
- FAIL: Dialog doesn't appear, or camera still blocked after allowing

---

#### **TC-SC-001-003: App Maintains State After Background/Foreground**
**Acceptance Criterion**: AC-1.1.3 (App state persists)  
**Test Type**: Functional / Regression  
**Priority**: P1 HIGH  
**Automation**: AUTOMATED

**Preconditions**:
- App is open on scanning screen
- Camera permission granted

**Test Steps**:
1. Open app and verify scanning screen
2. Press home button to send app to background
3. Wait 5 seconds
4. Tap app icon to bring app back to foreground
5. Verify scanning screen still visible

**Given/When/Then**:
```gherkin
Given the app is open with scanning screen visible
When the user closes the app and reopens it
Then the app returns to scanning screen
  And all previous state is preserved
```

**Expected Results**:
- ✅ App resumes to scanning screen (no re-launch)
- ✅ Camera still accessible
- ✅ No data loss
- ✅ App doesn't crash on background/foreground transitions

**Test Data**: N/A

---

### ERROR SCENARIO TESTS

#### **TC-SC-001-004: Camera Permission Denied**
**Acceptance Criterion**: AC-1.2.2 (Handle permission denied)  
**Test Type**: Error Scenario  
**Priority**: P1 HIGH  
**Automation**: MANUAL

**Preconditions**:
- App is open
- Camera permission prompt is showing

**Test Steps**:
1. Tap "Don't Allow" on permission prompt
2. Verify error message displays
3. Tap "Open Settings" button (if shown)

**Given/When/Then**:
```gherkin
Given the camera permission prompt is displayed
When the user taps "Don't Allow"
Then the app displays a message: "Camera access required to scan items"
  And an "Open Settings" option is provided
  And the app remains functional (can retry)
```

**Expected Results**:
- ✅ Error message appears
- ✅ "Open Settings" button directs to system settings
- ✅ Returning to app allows retry
- ✅ No app crash

**Test Data**: N/A

**Pass/Fail Criteria**:
- PASS: Error handled gracefully, user can enable permission via settings
- FAIL: App crashes or settings don't open

---

#### **TC-SC-001-005: App Handles Offline State**
**Acceptance Criterion**: AC-1.3.1 (Offline handling)  
**Test Type**: Error Scenario  
**Priority**: P1 HIGH  
**Automation**: MANUAL

**Preconditions**:
- App is installed
- Device has WiFi enabled

**Test Steps**:
1. Disable WiFi on device
2. Open app
3. Verify warning message displays
4. Verify scanning interface still visible

**Given/When/Then**:
```gherkin
Given the device has no WiFi/cellular connectivity
When the app is opened
Then the app displays a warning: "Limited connectivity. Some features may not work."
  And a "Retry Connection" button is available
  And the scanning screen is still visible (cached/local mode)
```

**Expected Results**:
- ✅ Warning message displays
- ✅ Scanning screen still accessible
- ✅ "Retry Connection" button present
- ✅ App doesn't crash without connectivity

**Test Data**: None

**Pass/Fail Criteria**:
- PASS: Warning shown, app remains functional
- FAIL: App crashes, or scanning disabled without network

---

#### **TC-SC-001-006: App Recovers from Network Restoration**
**Acceptance Criterion**: AC-1.4.1 (Recovery from offline)  
**Test Type**: Error Scenario  
**Priority**: P2 MEDIUM  
**Automation**: MANUAL

**Preconditions**:
- App is open in offline state
- WiFi is disabled

**Test Steps**:
1. Re-enable WiFi on device
2. Tap "Retry Connection" button (or wait 10 seconds)
3. Verify warning message disappears
4. Verify app is fully functional

**Given/When/Then**:
```gherkin
Given the app was in offline state with warning displayed
When network connectivity is restored
Then the warning message disappears
  And the app returns to normal state
  And all features are accessible
```

**Expected Results**:
- ✅ Warning clears after network restored
- ✅ Scanning fully functional
- ✅ No app restart needed
- ✅ Performance returns to normal

---

#### **TC-SC-001-007: App Crash Recovery - Data Persisted**
**Acceptance Criterion**: AC-1.5.1 (Crash resilience)  
**Test Type**: Error Scenario  
**Priority**: P1 HIGH  
**Automation**: MANUAL

**Preconditions**:
- App is open (simulating after a crash)
- Previous app state had scanning screen visible

**Test Steps**:
1. Force close app (kill process)
2. Reopen app
3. Verify app resumes to scanning screen
4. Verify no data loss

**Given/When/Then**:
```gherkin
Given the app was closed unexpectedly (crash)
When the user reopens the app
Then the app recovers gracefully
  And the scanning screen is displayed
  And the previous state is restored
```

**Expected Results**:
- ✅ App starts without crash on reopening
- ✅ Scanning screen displayed (expected state)
- ✅ App is stable
- ✅ No error messages shown

---

### EDGE CASE TESTS

#### **TC-SC-001-008: App Launch with Low Memory**
**Acceptance Criterion**: AC-1.6.1 (Resource handling)  
**Test Type**: Edge Case  
**Priority**: P2 MEDIUM  
**Automation**: MANUAL

**Preconditions**:
- Device has <500MB free memory
- Multiple apps running in background

**Test Steps**:
1. Ensure low memory condition (open many apps)
2. Launch Smart Checkout app
3. Verify app still launches successfully

**Expected Results**:
- ✅ App launches (may take slightly longer)
- ✅ Scanning screen displays
- ✅ No crash due to memory pressure

---

#### **TC-SC-001-009: Permission Request on iOS vs Android**
**Acceptance Criterion**: AC-1.7.1 (Platform-specific UX)  
**Test Type**: Edge Case  
**Priority**: P2 MEDIUM  
**Automation**: AUTOMATED

**Preconditions**:
- Clean install on both iOS and Android devices

**Test Steps**:
1. Launch app on iOS device
2. Verify permission dialog matches iOS UX standards
3. Launch app on Android device
4. Verify permission dialog matches Android UX standards

**Expected Results**:
- ✅ iOS: Dialog follows Apple's permission design
- ✅ Android: Dialog follows Google's Material Design
- ✅ Both platforms fully functional

---

#### **TC-SC-001-010: Device Orientation Changes**
**Acceptance Criterion**: AC-1.8.1 (UI responsiveness)  
**Test Type**: Edge Case  
**Priority**: P2 MEDIUM  
**Automation**: MANUAL

**Preconditions**:
- App is open in portrait orientation

**Test Steps**:
1. Rotate device to landscape orientation
2. Verify scanning screen adapts
3. Rotate back to portrait
4. Verify UI returns to normal

**Expected Results**:
- ✅ UI adapts to both portrait and landscape
- ✅ Scanning interface remains functional in both orientations
- ✅ No UI elements cut off or misaligned
- ✅ No crash on rotation

---

### SECURITY TEST

#### **TC-SC-001-011: No Sensitive Data Logged**
**Acceptance Criterion**: AC-1.9.1 (Security/Privacy)  
**Test Type**: Security  
**Priority**: P1 HIGH  
**Automation**: AUTOMATED

**Preconditions**:
- App is open
- Device logs are accessible

**Test Steps**:
1. Open app and perform basic operations
2. Review device logs (iOS Console, Android Logcat)
3. Search for any sensitive data (credit card, personal info)

**Expected Results**:
- ✅ No credit card numbers in logs
- ✅ No personal identifying information
- ✅ No authentication tokens exposed
- ✅ Error messages are generic (no stack traces leaking info)

---

#### **TC-SC-001-012: Camera Access Limited to App**
**Acceptance Criterion**: AC-1.10.1 (Permission boundaries)  
**Test Type**: Security  
**Priority**: P1 HIGH  
**Automation**: MANUAL

**Preconditions**:
- App has camera permission

**Test Steps**:
1. Open app with camera active
2. Deny app access to photo library (if applicable)
3. Verify app cannot access other sensitive data (contacts, calendar, etc.)

**Expected Results**:
- ✅ App only has camera permission (isolated access)
- ✅ App cannot access other device data
- ✅ Permissions are clearly defined and limited

---

## Test Execution Checklist

- [ ] TC-SC-001-001: App Launch Happy Path
- [ ] TC-SC-001-002: Camera Permission Granted
- [ ] TC-SC-001-003: State Persistence
- [ ] TC-SC-001-004: Permission Denied Error
- [ ] TC-SC-001-005: Offline State Handling
- [ ] TC-SC-001-006: Network Recovery
- [ ] TC-SC-001-007: Crash Recovery
- [ ] TC-SC-001-008: Low Memory Handling
- [ ] TC-SC-001-009: Platform-Specific UX (iOS vs Android)
- [ ] TC-SC-001-010: Device Orientation Changes
- [ ] TC-SC-001-011: Security - No Data Leaks
- [ ] TC-SC-001-012: Security - Permission Isolation

---

## Test Coverage Matrix

| Acceptance Criterion | Test Case(s) | Status |
|---------------------|-------------|--------|
| AC-1.1 (App Launch) | TC-SC-001-001, 003 | ✅ Covered |
| AC-1.2 (Camera Permission) | TC-SC-001-002, 004 | ✅ Covered |
| AC-1.3 (Offline Handling) | TC-SC-001-005, 006 | ✅ Covered |
| AC-1.4 (Error Recovery) | TC-SC-001-007, 008 | ✅ Covered |
| AC-1.5 (Platform UX) | TC-SC-001-009, 010 | ✅ Covered |
| AC-1.6 (Security) | TC-SC-001-011, 012 | ✅ Covered |
| **TOTAL COVERAGE** | **100%** | ✅ |

---

## Estimated QA Time

- **Manual Testing**: 35-40 minutes
- **Automated Testing**: 10-15 minutes (CI/CD)
- **Total Regression Time**: ~50 minutes
- **Defect Re-Testing**: +20 minutes per defect

---

## Notes for QA Team

1. Test on minimum supported devices (iPhone SE, budget Android)
2. Test on latest devices (iPhone 15, Pixel 8)
3. Network tests should simulate real WiFi drops (use network simulator tools)
4. Security testing requires logcat/Console access
5. Consider accessibility testing (screen readers) in next phase

