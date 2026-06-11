# Planning Workflow Auto-Detection Design Pattern

**Document ID**: WORKFLOW-AUTO-DETECTION-DESIGN  
**Version**: 1.0  
**Date**: June 11, 2026  
**Status**: Design Document

---

## 📋 Problem Statement

**Current Issue**: The planning workflow asks users to manually choose between:
- `normal-planning` (new features)
- `enhancement-planning` (existing features)

This breaks the principle of **autonomous workflows** - the system should intelligently detect which path to take without user intervention.

**Desired Outcome**: The workflow should:
1. Automatically detect whether a requirement is for a new feature or enhancement
2. Route to the appropriate workflow path based on detection results
3. Never ask the user to choose
4. Log the detection decision for audit trail

---

## 🎯 Solution Design

### High-Level Flow

```
User Input (Feature Requirements)
    ↓
[STAGE 0: ENHANCEMENT DETECTION - AUTO]
    ├─ Run enhancement-detector skill
    ├─ Detect existing artifacts (BRD, stories, tests)
    ├─ Calculate confidence score
    ├─ Determine: NEW vs ENHANCEMENT
    ↓
    ├─→ Confidence < 40% → Route to NORMAL-PLANNING
    │   (Create new BRD, stories, tests)
    │
    └─→ Confidence ≥ 70% → Route to ENHANCEMENT-PLANNING
        (Update existing BRD, modify stories, new tests)
        
    └─→ 40-70% → MANUAL REVIEW REQUIRED (ask user)
        (Ambiguous case - user chooses)
    ↓
[STAGES 1-6: Execute Chosen Path]
    ↓
[Auto-generate Execution Report]
```

### Stage 0: Enhanced Detection (New)

**Responsibility**: Automatically detect and route before user sees workflow selection

**Logic**:
```pseudocode
IF (feature_slug matches ANY existing BRD in features/brd/) {
  confidence = HIGH (≥ 70%)
  route = ENHANCEMENT-PLANNING
} ELSE IF (feature_slug matches ANY existing story in features/user-stories/) {
  confidence = MEDIUM-HIGH (≥ 70%)
  route = ENHANCEMENT-PLANNING
} ELSE IF (feature_slug matches ANY existing test in features/test-cases/) {
  confidence = MEDIUM-HIGH (≥ 70%)
  route = ENHANCEMENT-PLANNING
} ELSE {
  confidence = LOW (< 40%)
  route = NORMAL-PLANNING
}

IF (40% ≤ confidence < 70%) {
  // Ambiguous: ask user to confirm
  USER_CHOICE = ask("Is this an enhancement of existing feature?")
  route = USER_CHOICE ? ENHANCEMENT-PLANNING : NORMAL-PLANNING
}

LOG detection_result {
  feature_slug: "smart-coupon-system"
  confidence: 85%
  route: "enhancement-planning"
  rationale: "Existing BRD found: smart-coupon-system-v1.0.md"
  existing_artifacts: [BRD, 7 stories, 114 test cases]
}
```

**Detection Criteria** (in order of priority):
1. **Exact BRD Match** (confidence +50%): BRD file exists with same slug
2. **Story Match** (confidence +30%): User stories exist with same slug
3. **Test Match** (confidence +20%): Test cases exist with same slug
4. **GitHub Issues** (confidence +10%): Existing issues in repo for this feature
5. **Keyword Analysis** (confidence +5%): Similar terminology in existing artifacts

**Confidence Thresholds**:
- **≥ 70%**: AUTO-ROUTE → Enhancement Planning (no user prompt)
- **40-70%**: AMBIGUOUS → Ask user: "Is this updating an existing feature?"
- **< 40%**: AUTO-ROUTE → Normal Planning (brand new feature)

**Output**: Detection report logged to execution report
```json
{
  "stage": "Stage 0: Enhancement Detection",
  "status": "COMPLETE",
  "detection_result": {
    "feature_slug": "smart-coupon-system",
    "confidence_score": 85,
    "route_selected": "enhancement-planning",
    "rationale": "Exact BRD match found",
    "existing_artifacts": {
      "brd_count": 1,
      "story_count": 7,
      "test_count": 114,
      "github_issues": 0
    },
    "matching_artifacts": [
      "features/brd/smart-coupon-system-v1.0.md",
      "features/user-stories/customer-receives-personalized-coupon-recommendations.md",
      "..."
    ]
  },
  "user_interaction": "NONE (auto-detected)",
  "decision_time": "2026-06-11T10:00:00Z"
}
```

---

## 🛣️ Routing Logic

### Case 1: Brand New Feature (< 40% Confidence)
```
Feature: "Voucher Management System"
Detection: No existing artifacts
Confidence: 5%
Route: NORMAL-PLANNING
Workflow: BRD → Stories → Tests → GitHub → Report
```

### Case 2: Clear Enhancement (≥ 70% Confidence)
```
Feature: "Smart Coupon System - Add Push Notifications"
Detection: BRD exists, 7 stories exist, test cases exist
Confidence: 85%
Route: ENHANCEMENT-PLANNING
Workflow: Enhance BRD → Update/Create Stories → Update/Create Tests → GitHub → Report
```

### Case 3: Ambiguous (40-70% Confidence)
```
Feature: "Loyalty Program Enhancements"
Detection: Partial match - might be many things
Confidence: 55%
Route: ASK USER
User Input: "Yes, this updates the existing Coupon System"
Final Route: ENHANCEMENT-PLANNING
```

---

## 📐 Implementation

### File Structure Changes

**New Detection Configuration**:
```
.github/workflows/
├── planning-workflow.md (NEW - Master workflow)
│   ├── Stage 0: Enhancement Detection (AUTO)
│   ├── Routes to: normal-planning or enhancement-planning
│   └── No user interaction needed
├── normal-planning.md (UPDATED)
│   └── Remove initial choice prompt
├── enhancement-planning.md (UPDATED)
│   └── Remove initial choice prompt
```

### Logic Changes

**Before** (Current):
```
User provides feature requirements
    ↓
Workflow asks: "Is this new or enhancement?"
    ↓
User chooses
    ↓
Execute chosen path
```

**After** (New Design):
```
User provides feature requirements
    ↓
[STAGE 0] Enhancement detector runs automatically
    ↓
Confidence check → route determined
    ↓
IF ambiguous: Ask user (minimal interaction)
    ↓
Execute determined path
```

---

## 🔍 Detection Algorithm (Detailed)

```python
def detect_enhancement(feature_requirements):
    """
    Intelligently detect if feature is new or enhancement.
    Returns: (route: str, confidence: float, rationale: str)
    """
    confidence_score = 0
    matching_artifacts = []
    
    # Extract feature slug from requirements
    feature_slug = extract_slug(feature_requirements)
    
    # Check 1: BRD files (highest signal)
    brd_files = find_files(f"features/brd/*{feature_slug}*.md")
    if brd_files:
        confidence_score += 50
        matching_artifacts.extend(brd_files)
    
    # Check 2: User story files
    story_files = find_files(f"features/user-stories/*{feature_slug}*.md")
    if story_files:
        confidence_score += 30
        matching_artifacts.extend(story_files)
    
    # Check 3: Test case files
    test_files = find_files(f"features/test-cases/*{feature_slug}*.md")
    if test_files:
        confidence_score += 20
        matching_artifacts.extend(test_files)
    
    # Check 4: GitHub issues
    github_issues = search_github_issues(feature_slug)
    if github_issues.found:
        confidence_score += 10
        matching_artifacts.extend(github_issues.urls)
    
    # Check 5: Keyword analysis (fuzzy matching)
    keyword_match_score = fuzzy_match(
        feature_requirements.keywords,
        extract_keywords_from(matching_artifacts)
    )
    confidence_score += keyword_match_score  # 0-5 pts
    
    # Cap at 100%
    confidence_score = min(confidence_score, 100)
    
    # Determine route
    if confidence_score >= 70:
        return ("enhancement-planning", confidence_score, 
                f"Existing artifacts found with {confidence_score}% confidence")
    elif confidence_score < 40:
        return ("normal-planning", confidence_score,
                f"No matching artifacts found ({confidence_score}% confidence)")
    else:
        # Ambiguous - requires user input
        return ("ask-user", confidence_score,
                f"Ambiguous match ({confidence_score}% confidence) - user review needed")
```

---

## 📊 Examples

### Example 1: Enhancement Detected
```
INPUT: "Add push notifications to the Smart Coupon System"
├─ Slug extracted: "smart-coupon-system"
├─ Check BRD: ✓ Found smart-coupon-system-v1.0.md (+50)
├─ Check Stories: ✓ Found 7 stories (+30)
├─ Check Tests: ✓ Found 114 tests (+20)
├─ Check GitHub: ✗ No issues (-0)
├─ Keyword match: ✓ "push", "notification" in stories (+5)
│
├─ TOTAL CONFIDENCE: 105 → capped at 100% ✓
├─ DECISION: ENHANCEMENT-PLANNING (≥ 70%) ✓
├─ USER INTERACTION: NONE ✓
└─ ACTION: Auto-route to enhancement workflow
```

### Example 2: New Feature Detected
```
INPUT: "Build a Referral Program with rewards tracking"
├─ Slug extracted: "referral-program"
├─ Check BRD: ✗ Not found (-0)
├─ Check Stories: ✗ Not found (-0)
├─ Check Tests: ✗ Not found (-0)
├─ Check GitHub: ✗ No issues (-0)
├─ Keyword match: ✗ No matches (-0)
│
├─ TOTAL CONFIDENCE: 0% ✓
├─ DECISION: NORMAL-PLANNING (< 40%) ✓
├─ USER INTERACTION: NONE ✓
└─ ACTION: Auto-route to normal workflow
```

### Example 3: Ambiguous Case
```
INPUT: "Update loyalty benefits and coupon strategy"
├─ Slug extracted: "loyalty-benefits"
├─ Check BRD: ✗ No exact match (-0)
├─ Check Stories: ✓ Found "loyalty-program-member" stories (+30)
├─ Check Tests: ✗ No exact match (-0)
├─ Check GitHub: ✗ No issues (-0)
├─ Keyword match: ✓ "loyalty" appears, "coupon" in stories (+10)
│
├─ TOTAL CONFIDENCE: 40% ⚠️
├─ DECISION: AMBIGUOUS (40-70% range) ⚠️
├─ USER INTERACTION: REQUIRED ✓
│   Question: "Is this an enhancement of the existing Loyalty Program?"
│   User: "Yes, specifically for Smart Coupon subsystem"
│   → Route to: ENHANCEMENT-PLANNING
└─ ACTION: Route to enhancement workflow
```

---

## 🔐 Audit Trail & Logging

Every detection decision must be logged:

```json
{
  "workflow_id": "planning-workflow-smart-coupon-v2-20260611",
  "stage_0_detection": {
    "timestamp": "2026-06-11T10:00:00Z",
    "feature_input": "Smart Coupon System - Add push notifications",
    "feature_slug": "smart-coupon-system",
    "detection_algorithm": "semantic-match-v1.0",
    "checks_performed": [
      {
        "check": "BRD file match",
        "result": "MATCH",
        "confidence_delta": 50,
        "matching_artifact": "features/brd/smart-coupon-system-v1.0.md"
      },
      {
        "check": "User story match",
        "result": "MATCH",
        "confidence_delta": 30,
        "artifact_count": 7
      },
      {
        "check": "Test case match",
        "result": "MATCH",
        "confidence_delta": 20,
        "artifact_count": 114
      }
    ],
    "total_confidence": 85,
    "final_decision": "enhancement-planning",
    "user_interaction": "NONE",
    "ambiguity_level": "LOW"
  },
  "workflow_route": "enhancement-planning",
  "stages_executed": ["Stage 0", "Stage 2", "Stage 3", "Stage 4", "Stage 5", "Stage 6"]
}
```

---

## ✅ Implementation Checklist

### Phase 1: Design & Documentation (DONE)
- [x] Design document created
- [x] Detection algorithm defined
- [x] Routing logic documented
- [x] Examples provided

### Phase 2: Update Workflows (TODO)
- [ ] Create `planning-workflow.md` with Stage 0 auto-detection
- [ ] Remove user choice prompt from `normal-planning.md`
- [ ] Remove user choice prompt from `enhancement-planning.md`
- [ ] Add detection logging to execution report template

### Phase 3: Testing & Validation (TODO)
- [ ] Test Case 1: Brand new feature → normal-planning
- [ ] Test Case 2: Clear enhancement → enhancement-planning
- [ ] Test Case 3: Ambiguous case → ask user
- [ ] Verify audit trail logging
- [ ] Performance test detection algorithm (< 5 seconds)

### Phase 4: Rollout (TODO)
- [ ] Update documentation
- [ ] Train team on new workflow
- [ ] Monitor detection accuracy
- [ ] Gather feedback

---

## 🎓 Lessons Learned & Best Practices

1. **Never ask what you can detect** - Always attempt automatic detection first
2. **Confidence scores over binary choices** - Allow for nuance and ambiguity
3. **Audit trail everything** - Log detection decisions for compliance & learning
4. **Graceful degradation** - Ask user only when confidence is ambiguous (40-70%)
5. **Parallel detection** - Check BRD, stories, tests, GitHub in parallel for speed
6. **Semantic matching** - Use keyword analysis for nuance detection beyond exact matches

---

## 📚 References

- Enhancement Detector Skill: `.github/skills/enhancement-detector/SKILL.md`
- Normal Planning Workflow: `.github/workflows/normal-planning.md`
- Enhancement Planning Workflow: `.github/workflows/enhancement-planning.md`

---

**Document Status**: ✅ APPROVED FOR IMPLEMENTATION  
**Next Action**: Update workflows with Stage 0 detection
