# Planning Standards & Templates

> **Single source of truth** for all planning artifacts including BRDs, User Stories, Functional Test Cases, and brainstorming standards.

---

## 1. Business Requirement Document (BRD) Standard

### BRD Template Structure

```markdown
# Business Requirement Document (BRD)
## [Feature Name]

**Document ID:** BRD-[YYYY-MM-DD]-[slug]
**Status:** Draft / Final
**Version:** 1.0
**Created:** [Date]
**Last Updated:** [Date]

---

## Executive Summary
[2-3 sentence overview of the feature, target users, and business value]

---

## 1. Business Case & Objectives

### Problem Statement
[What problem does this solve? Why is it needed?]

### Business Objectives
- Objective 1: [SMART goal]
- Objective 2: [SMART goal]
- Objective 3: [SMART goal]

### Success Metrics
- Metric 1: [How will we measure success?]
- Metric 2: [Quantifiable outcome]

---

## 2. Scope Definition

### In Scope
- Feature/Function 1
- Feature/Function 2
- Feature/Function 3

### Out of Scope
- [Explicitly list what is NOT included]
- [Why these are excluded]

### Constraints & Dependencies
- Time Constraint: [e.g., Must launch by Q2 2024]
- Technical: [e.g., Must use existing API layer]
- Business: [e.g., Regulatory compliance required]
- External: [e.g., Depends on third-party integration]

---

## 3. Stakeholders & Users

### Stakeholders
| Role | Name | Contact | Responsibility |
|------|------|---------|-----------------|
| [Role] | [Name] | [Email] | [Approval/Input] |

### User Personas
| Persona | Description | Usage Pattern |
|---------|-------------|----------------|
| [Name] | [Who are they?] | [How will they use?] |

---

## 4. Functional Requirements

### User Flows
[Describe 3-5 primary user journeys]

1. **Flow 1: [Name]**
   - User Story: As a [user], I want to [action] so that [benefit]
   - Steps: 1. ... 2. ... 3. ...
   - Key Data: [What data is involved?]

2. **Flow 2: [Name]**
   - User Story: ...

### Feature Details

#### Feature Set 1: [Name]
- **Description**: [What does it do?]
- **Inputs**: [Data required]
- **Outputs**: [Data provided]
- **Business Rules**: [Rules that apply]
  - Rule 1: [e.g., "Users can only see their own data"]
  - Rule 2: [e.g., "Status must follow workflow: Draft → Review → Approved"]

#### Feature Set 2: [Name]
- ...

---

## 5. Non-Functional Requirements

### Performance
- Response Time: [e.g., API calls < 200ms]
- Throughput: [e.g., Support 1000 concurrent users]
- Scalability: [e.g., Auto-scale with load]

### Security
- Authentication: [e.g., JWT-based auth required]
- Authorization: [e.g., Role-based access control]
- Data Protection: [e.g., Encryption at rest and in transit]

### Compliance
- Standards: [e.g., GDPR, SOC2]
- Regulations: [e.g., Data retention policies]

### Accessibility
- Standards: [e.g., WCAG 2.1 AA compliance]
- Device Support: [e.g., Desktop, tablet, mobile]

---

## 6. Assumptions & Constraints

### Assumptions
- [e.g., "Current user base is ~10,000 active users"]
- [e.g., "Data will be available from existing API"]
- [e.g., "Team has React/Node.js expertise"]

### Technical Constraints
- Must use 3-tier API architecture (Domain, Business, Experience layers)
- Frontend must use React 18 + Vite + Tailwind CSS
- Must integrate with existing authentication system

---

## 7. Risks & Mitigation

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|-----------|
| [Risk Description] | High/Med/Low | High/Med/Low | [Mitigation Strategy] |

---

## 8. Success Criteria & Acceptance

### Definition of Done
- [ ] BRD approved by stakeholders
- [ ] User stories created and refined
- [ ] Functional test cases defined
- [ ] Design mockups approved
- [ ] Architecture review completed
- [ ] No high/critical security vulnerabilities
- [ ] Code coverage ≥ 80%
- [ ] Stakeholder sign-off obtained

---

## 9. Appendix

### Terminology
[Define domain-specific terms]

### References
- [Link to related documents]
- [Link to mockups/designs]
- [Link to API specifications]

---

**Approvals:**

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Product Owner | | | |
| Technical Lead | | | |
| Business Analyst | | | |
```

---

## 2. User Story Template Standard

### User Story File Naming
- **Format**: `[slug].md` (kebab-case, no spaces)
- **Location**: `features/user-stories/`
- **Example**: `inventory-search-by-location.md`

### User Story Template

```markdown
# User Story: [Feature Name]

**Story Slug:** `[slug-name]`  
**Status:** Draft / Approved / In Development / Done  
**Priority:** P0 / P1 / P2 / P3  
**Story Points:** [e.g., 5, 8, 13]  
**Epic:** [If part of larger feature]

---

## Feature Overview
[1-2 sentence description of what this story delivers]

---

## User Story Statement

**As a** [user role/persona]  
**I want to** [specific action/capability]  
**So that** [business value/benefit]

---

## Story Scope

### What's Included
- [Feature aspect 1]
- [Feature aspect 2]
- [Feature aspect 3]

### What's Out of Scope
- [Explicitly excluded items]
- [Why these are excluded]

---

## Key Features / Acceptance Criteria

### Feature 1: [Name]
**Description**: [What should it do?]

**Given** [Initial state]  
**When** [User action]  
**Then** [Expected outcome]

---

## Technical Requirements

### Frontend
- **Components**: [List React components needed]
- **Pages**: [List pages/routes affected]
- **State Management**: [React Query, Context, etc.]

### Backend API
- **New Endpoints**: 
  - `GET /api/experience/[resource]` - [Description]
  - `POST /api/business/[resource]` - [Description]

---

## Out of Scope
- [Explicitly list what is NOT included]

---

## Value & Impact
[How does this benefit the business and user?]

---

## Dependencies
- **Blocks**: [What features depend on this?]
- **Blocked By**: [What must be done first?]
- **Related Stories**: [Cross-references to other stories]

---

## Approval

| Role | Name | Date | Sign-off |
|------|------|------|----------|
| Product Owner | | | ☐ Approved |
| Technical Lead | | | ☐ Approved |
```

---

## 3. Functional Test Case Standard

### Test Case Template

```markdown
# Functional Test Cases
## Story: [User Story Title]

**Story Slug:** `[slug]`  
**Test Suite ID:** TS-[YYYY-MM-DD]-[slug]  

## Setup & Teardown

### Pre-requisites
- [ ] User is logged in with [specific role]
- [ ] Test data exists: [What data is needed?]

---

## Test Cases

### TC-001: [Test Case Name]

**Story Reference**: [User Story title]  
**Feature**: [Which feature does this test?]  
**Priority**: P0 / P1 / P2 / P3  
**Type**: Functional / Regression / Smoke  
**Manual/Automated**: Manual / Automated  

**Preconditions**
- [State 1]
- [State 2]

**Test Steps**

| # | Step | Expected Result |
|---|------|-----------------|
| 1 | [Action 1] | [What should happen?] |
| 2 | [Action 2] | [What should happen?] |

**Given/When/Then Format**
```
Given I am on the [Page Name] page
When I click on [element]
Then I see [expected element]
```

**Pass Criteria**
- [ ] All steps executed successfully
- [ ] All expected results verified
```

---

## 4. Brainstorming Standards

### Gap Analysis Questions - Categories

The agent should generate **4-6 targeted questions** addressing these categories:

1. **Scope & Boundaries**
2. **User Roles & Permissions**
3. **Integration & Dependencies**
4. **Performance & Scale**
5. **Compliance & Security**
6. **Success Metrics & Value**

---

## 5. Enhancement Detection Standards

### Enhancement Identification Checklist

When analyzing for enhancements, check:

```markdown
## Enhancement Detection Analysis

### Existing Artifacts Found
- [ ] BRD exists: `[path]`
- [ ] User stories exist: [count] stories found
- [ ] Test cases exist: [count] test cases found

### Relationship to Existing Feature
- **Original Feature**: [Name/Story]
- **Enhancement Type**: [New capability / Extension / Improvement]

### Recommended Action
- [ ] **Create New**: Separate feature
- [ ] **Update Existing**: Merge with existing artifacts
```

---

## 6. GitHub Integration Standards

### Issue Template for User Stories

```markdown
---
name: User Story
about: Planning workflow generated user story
labels: requirement, user-story
---

# User Story: [Title]

**Story Slug:** `[slug]`
**Priority:** P0 / P1 / P2 / P3

## User Story Statement
As a **[role]**, I want to **[action]** so that **[benefit]**.

## Acceptance Criteria

- [ ] **Given** [state] **When** [action] **Then** [outcome]
- [ ] **Given** [state] **When** [action] **Then** [outcome]

## Out of Scope
- [Item 1]
- [Item 2]

## Dependencies
- Blocks: [linked issues]
- Blocked By: [linked issues]
```

### Label Conventions
- `requirement` - From BRD generation
- `user-story` - Individual story
- `test-case` - Functional test case
- `enhancement` - Feature modification
- `priority-[0-3]` - Priority level
- `epic-[name]` - Epic grouping

---

## Compliance & Review Checklist

All planning artifacts MUST pass this review:

- [ ] **Completeness**: All required sections present
- [ ] **Clarity**: Language is clear and unambiguous
- [ ] **Consistency**: Terminology used consistently
- [ ] **Traceability**: Acceptance criteria are testable
- [ ] **Scope**: Boundaries clearly defined (in/out)
- [ ] **Technical Alignment**: Follows architecture standards
- [ ] **Business Alignment**: Supports business objectives
- [ ] **Approval**: Signed off by appropriate stakeholders
