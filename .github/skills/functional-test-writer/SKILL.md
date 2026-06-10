---
name: functional-test-writer
description: >
  Analyzes user stories and generates comprehensive functional test cases in BDD 
  format. Supports two modes: (1) Parallel per-story generation for speed, 
  (2) Monolithic single-file generation for simplicity. 
  Creates test case documents organized by user story.
allowed-tools: read, edit, shell, create, glob
---

# Functional Test Writer Skill

Quality Assurance Engineer specializing in creating comprehensive functional test cases from user stories in BDD format.

## Two Execution Modes

### Mode 1: Parallel Per-Story (RECOMMENDED - Fast)
Generate test cases in parallel, one agent per story.
- **Execution**: 11 parallel sub-agents for 11 stories + 1 for common tests
- **Files**: 12 separate test case files
- **Speed**: ~8-10s vs 30-40s sequential
- **File Output**:
  ```
  features/test-cases/
  ├── smart-coupon-system-common-tests.md (cross-cutting, integration)
  ├── smart-coupon-system-SC-001-tests.md (story 1 only)
  ├── smart-coupon-system-SC-002-tests.md (story 2 only)
  ├── ... (one file per story)
  └── smart-coupon-system-test-index.md (links all tests)
  ```

### Mode 2: Monolithic (Legacy - Simple)
Generate all test cases in one file (original approach).
- **Execution**: Single agent, sequential
- **Files**: 1 large test case file
- **Speed**: 30-40s
- **File Output**: `features/test-cases/smart-coupon-system-test-cases.md`

## Your Workflow

### Step 1: Determine Execution Mode
Check input parameters:
- `mode: "parallel"` → Use Mode 1 (per-story files)
- `mode: "monolithic"` → Use Mode 2 (single file)
- Default: `parallel` (faster)

### Step 2: If Mode = Parallel

#### Sub-Task: Common Test Cases (Shared)
Generate cross-cutting test cases that apply across stories:
- System integration tests
- Error recovery scenarios
- Data consistency validations
- Performance baselines
- Security/compliance checks

**Output**: `features/test-cases/smart-coupon-system-common-tests.md`

#### Sub-Task: Per-Story Test Cases (Parallel)
For EACH user story (SC-001 through SC-011), run ONE parallel agent:

**Each agent generates**:
- Happy path tests (3-5 test cases per story)
- Error/negative tests (2-3 test cases per story)
- Edge case tests (2-3 test cases per story)
- Story-specific integration tests (1-2 test cases per story)

**Output per story**: `features/test-cases/smart-coupon-system-SC-XXX-tests.md`

#### Sub-Task: Test Index (After all parallel agents complete)
Generate index file linking all test files:
- `features/test-cases/smart-coupon-system-test-index.md`
- Lists all 12 test files
- Summary table: Test count per story
- Cross-references between test files
- Execution sequencing recommendations

### Step 3: If Mode = Monolithic

Generate all test cases in single file (see original workflow below).

### Step 4: Test Scenario Analysis
For each user story, identify test scenarios:

**Categories**:
1. **Happy Path** - Success scenarios
2. **Error Scenarios** - Failure paths
3. **Edge Cases** - Boundary conditions
4. **Integration** - Multi-step workflows
5. **Security** - Authorization/permissions
6. **Recovery** - Error recovery

**BDD Format**:
```
TC-XXX: [Test Case Name]
Story: [SC-001]
Type: [Happy Path / Error / Edge / Integration / Security]
Priority: [P0 Critical / P1 High / P2 Medium / P3 Low]
AC Verified: [AC-001]

Given [Precondition]
When [Action]
Then [Expected outcome]
  And [Verification 2]
  And [Verification 3]
```

### Step 5: Save All Test Files

**If Parallel Mode**:
- Save each per-story file immediately
- Save common tests file
- Save test index
- Total files: 13 (12 test files + 1 index)

**If Monolithic Mode**:
- Save single comprehensive file
- Total files: 1

**Each Test Case MUST include**:
1. Basic Information (ID, Name, Story reference, Type)
2. Test Type (Functional / Regression / Smoke / Integration)
3. Preconditions (System state before test)
4. Test Steps (Numbered, actionable steps with expected results)
5. Given/When/Then Format (BDD style)
6. Postconditions (System state after test)
7. Test Data (Specific values used)
8. Pass/Fail Criteria (Clear checkpoints)

### Step 3a: Extract & Systematize Non-Functional Requirements (NEW)
From the BRD, identify all non-functional requirements:

**Performance Tests**:
- Response time limits (e.g., <10 sec)
- Throughput requirements (e.g., 100+ txn/hr)
- Concurrency limits (e.g., 1000 simultaneous users)
- Generate test cases for each: T-NFR-001, T-NFR-002, etc.

**Security Tests**:
- Compliance standards (PCI-DSS, GDPR, SOC 2)
- Data protection (encryption, PII handling)
- Authentication/Authorization
- Input validation, SQL injection, XSS prevention
- Generate test cases: T-SEC-001, T-SEC-002, etc.

**Load Tests**:
- Stress testing (sudden spike handling)
- Soak testing (24+ hour sustained load)
- Failover scenarios
- Generate test cases: T-LOAD-001, T-LOAD-002, etc.

**Test Data Strategy** (NEW):
- Valid test data sources (Stripe test ranges, anonymized production data)
- Invalid formats for negative testing
- Boundary values (min/max)
- Edge case data (unicode, very long strings)
- Setup/teardown procedures

### Step 3b: Systematic Negative Test Framework (NEW - Tier 1)
For each acceptance criterion, generate negative tests:

**Negative Test Strategy**:
For AC: "User can enter valid email"
- NULL email → error
- Invalid format (no @, no domain) → error
- Too long (>255 chars) → error
- Special characters only → error
- SQL injection attempt → error
- XSS payload → error

**Categories**:
1. **NULL/Empty**: Empty string, NULL, whitespace only
2. **Format Violations**: Wrong structure, missing parts
3. **Boundary Violations**: Too long, too short, negative numbers
4. **Type Violations**: String when number expected, etc.
5. **Security Violations**: SQL injection, XSS, command injection
6. **Business Logic**: Violates business rules (negative quantity, etc.)

**Naming Convention**: T[N]-NEG-[Category]-[Case]
- Example: T1-NEG-FORMAT-001, T1-NEG-SECURITY-001

### Step 4: Map Tests to Acceptance Criteria & Link to Story IDs (NEW - Tier 2)
Create traceability linking:
- Test Case ID → Story ID → BRD Requirement ID
- Example: T1-001 → SC-001 → BRD-2026-06-10-checkout-STORY-001 → REQ-003
Create traceability matrix linking tests to acceptance criteria.

**Coverage Rules**:
- [ ] Each acceptance criterion tested at least once (happy path)
- [ ] Each has error scenario testing where applicable
- [ ] Edge cases identified and tested
- [ ] Security implications tested

### Step 5: Identify Test Priorities
Classify tests:
- **P0 (Critical)**: Must pass for feature to work
- **P1 (High)**: Important but feature works without it
- **P2 (Medium)**: Nice to have coverage
- **P3 (Low)**: Documentation/future reference

### Step 6: Create Test Case Document
Generate markdown file(s) following template in `.github/rules/planning-standards.md`.

**File Organization**: One file per story: `[story-slug]-test-cases.md`

### Step 7: Add Test Execution Guidance
Include:
- Manual testing instructions
- Automated testing framework hints
- Regression testing guidance
- Test data setup requirements
- **NEW: Test Automation Strategy** - Flag each test:
  - AUTOMATED: Deterministic, repeatable (login, data entry)
  - MANUAL: Requires judgment (UX, accessibility)
  - SEMI-AUTO: Automated setup, manual validation

### Step 8a: Provide Test Execution Sequencing (NEW)
Define test execution order:
```
1. Smoke tests (basic functionality) - 30 min
2. Regression tests (existing features) - 1 hr
3. Feature-specific tests - 1.5 hrs
4. Integration tests - 1 hr
5. Performance tests - 30 min
6. Security tests - 30 min
Total: ~5 hours for complete regression
```

### Step 8: Provide Summary
Display:
- Test breakdown by type
- Coverage matrix
- Estimated QA time
- Files generated

## Skill Behavior Rules

- **Support two modes**: Parallel (per-story + common) and Monolithic (single file)
- **Default to parallel mode** for speed (8-10s vs 30-40s)
- **Per-story files** keep tests focused and reviewable
- **Common file** captures cross-cutting test scenarios
- **Test index** links all files and provides summary
- **BDD format** for all test cases (Given/When/Then)
- **Complete test data** - use real values, not placeholders
- **Traceability** - link tests to AC, stories, and BRD requirements
- **Prioritize tests** - P0 critical path, P1 important, P2 nice-to-have
- **Save all files** before completing
- **Support parallel agents** - each story agent runs independently
