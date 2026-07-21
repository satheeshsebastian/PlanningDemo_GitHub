# Master Planning Workflow - Intelligent Feature Planning

**Version**: 2.0  
**Date**: June 11, 2026  
**Status**: Active - Smart Auto-Routing Enabled

---

## 🎯 Overview

The Master Planning Workflow intelligently detects whether a user requirement is for:
1. **New Feature** → Routes to `new-feature-planning` workflow
2. **Enhancement** → Routes to `enhancement-planning` workflow
3. **Ambiguous** → Asks user for clarification

**Key Principle**: Never ask users to choose what the system can automatically detect.

---

## 🔄 Complete Workflow Flow

**User requirement → Stage 0: Auto-Detection → Route to appropriate path → Execute Stages 1-6**

---

## 📋 Stage 0: Automatic Enhancement Detection

**Responsibility**: Route requirements to appropriate workflow path

**Input**: Raw feature requirements from user

**Skill**: `.github/skills/enhancement-detector/SKILL.md`

**Logic Summary**:
- Extracts feature slug from requirements
- Searches for existing artifacts (BRD, stories, tests, GitHub issues)
- Calculates confidence score (0-100%)
- Routes based on thresholds:
  - **≥ 70% confidence** → `enhancement-planning` workflow
  - **< 40% confidence** → `new-feature-planning` workflow  
  - **40-70% confidence** → Asks user for clarification

**Output**: Detection result logged to execution report with confidence score and rationale

---

## 📋 Stages 1-6: Execute Chosen Workflow Path

### Path A: New Feature Planning (Confidence < 40%)
Auto-routed when no existing artifacts found for this feature

| Stage | Skill | Input | Output | Approval |
|-------|-------|-------|--------|----------|
| 1 | brd-generator | Feature requirements + user brainstorming | BRD v1.0, Assumptions | ✅ Required |
| 2 | user-story-builder | BRD v1.0, Assumptions | 7-11 stories, Story Map, Traceability | ✅ Required |
| 3 | functional-test-writer | User stories + AC | 100+ BDD test cases | ❌ None |
| 4 | github-issue-uploader | Stories, Tests, Traceability | GitHub issues + Issue map | ❌ None |
| 5 | (auto-generated) | Metadata from all stages | Execution Report | ❌ None |

**Total Duration**: ~6-8 hours (includes approvals)

### Path B: Enhancement Planning (Confidence ≥ 70%)
Auto-routed when existing artifacts found for this feature

| Stage | Skill | Input | Output | Approval |
|-------|-------|-------|--------|----------|
| 1 | enhancement-modifier | Existing BRD + enhancement requirements | BRD v[N+1], Updated Assumptions | ❌ None |
| 2 | enhancement-story-updater | Updated BRD + existing stories + changelog | Modified/new stories, Updated Story Map | ✅ For new stories |
| 3 | functional-test-writer | Modified/new stories + AC | New/updated test cases | ❌ None |
| 4 | github-issue-uploader | Updated stories, tests, traceability | Updated/new GitHub issues + Issue map | ❌ None |
| 5 | (auto-generated) | Metadata from all stages | Execution Report | ❌ None |

**Total Duration**: ~4-6 hours (fewer approvals)

---

## 📚 Related Documents

- `WORKFLOW-AUTO-DETECTION-DESIGN.md` - Detailed design & algorithm
- `new-feature-planning.md` - New feature workflow
- `enhancement-planning.md` - Enhancement workflow
- Enhancement Detector Skill: `.github/skills/enhancement-detector/SKILL.md`

---

**Status**: ✅ LIVE - Auto-Detection Enabled  
**Last Updated**: June 11, 2026  
**Implemented By**: Planning Workflow v2.0
