# User Story Map
## Smart Checkout Assistant (BRD-2026-06-10-smart-checkout)

**Date**: 2026-06-10  
**BRD Version**: 1.0  
**Total Stories**: 11  

---

## 1. Story Overview & Distribution

### By Priority (MoSCoW)

| Priority | Count | Story IDs | Effort (Points) |
|----------|-------|-----------|-----------------|
| **MUST** | 7 | SC-001, SC-002, SC-004, SC-005, SC-007, SC-008, SC-009, SC-010 | 58 |
| **SHOULD** | 3 | SC-003, SC-006, SC-011 | 18 |
| **COULD** | 1 | (None for MVP) | 0 |
| **WON'T** | 0 | (None planned) | 0 |
| **TOTAL** | 11 | | **76 points** |

### By Story Type

| Type | Stories |
|------|---------|
| **Customer Flow** | SC-001, SC-002, SC-003, SC-004, SC-005, SC-006, SC-007 |
| **Support & Operations** | SC-008, SC-009 |
| **Technical Infrastructure** | SC-010, SC-011 |

---

## 2. Story Dependency Graph

```
┌─────────────────────────────────────────────────────────┐
│         CUSTOMER JOURNEY - Linear Flow                  │
└─────────────────────────────────────────────────────────┘

   SC-001 (Launch App)
        ↓
   ┌────┴─────┐
   ↓          ↓
SC-002      SC-003
(Barcode)   (QR Code)
   ↓          ↓
   └────┬─────┘
        ↓
   SC-004 (Cart)
        ↓
   SC-005 (Payment)
        ↓
   ┌────┬─────┐
   ↓    ↓     ↓
SC-006 SC-007 SC-011
(Rcpt) (Exit) (Network)
   ↓    ↓
   └────┴─────┘

┌─────────────────────────────────────────────────────────┐
│         SUPPORT OPERATIONS - Parallel                   │
└─────────────────────────────────────────────────────────┘

SC-008 (Escalation)  ← Depends on SC-002, SC-004
   ↑
   │
SC-009 (Manager Dash) ← Depends on SC-005, SC-007

┌─────────────────────────────────────────────────────────┐
│    TECHNICAL BACKBONE - Must-Have Foundations           │
└─────────────────────────────────────────────────────────┘

SC-010 (POS Integration) ← Required by SC-002, SC-003, SC-004
SC-011 (Network) ← Cross-cutting, all stories benefit
```

---

## 3. Dependency Matrix

| Story | Blocks | Blocked By | Sprint Impact |
|-------|--------|-----------|---------------|
| **SC-001** | SC-002, SC-003, SC-004 | None | **Foundational - Sprint 1** |
| **SC-002** | SC-004, SC-005 | SC-001, SC-010, ASSUMPTION-001 | **Foundational - Sprint 1** |
| **SC-003** | SC-004 | SC-001, SC-002, SC-010 | **Sprint 2 (SHOULD)** |
| **SC-004** | SC-005, SC-006 | SC-002, SC-003 | **Sprint 1** |
| **SC-005** | SC-006, SC-007 | SC-004, ASSUMPTION-003 | **Sprint 1** |
| **SC-006** | None | SC-005 | **Sprint 2 (SHOULD)** |
| **SC-007** | None | SC-005, ASSUMPTION-004 | **Sprint 1 (CRITICAL)** |
| **SC-008** | None | SC-002, SC-004 | **Sprint 1** |
| **SC-009** | None | SC-005, SC-007 | **Sprint 1** |
| **SC-010** | SC-002, SC-003, SC-004 | ASSUMPTION-001 | **Foundational - Sprint 0** |
| **SC-011** | (All) | None | **Foundational - Sprint 0** |

---

## 4. Sprint Planning Recommendations

### Sprint 0 (Weeks 1-2): Technical Foundation
**Focus**: Backend infrastructure, no customer-facing features yet

- **SC-010** (13 pts): POS Integration
- **SC-011** (8 pts): Network Resilience
- **TOTAL**: 21 points

**Outcome**: POS API working, network handling framework in place

### Sprint 1 (Weeks 3-6): MVP Core Features
**Focus**: Customer checkout flow + operations support

**MUST Stories** (40 pts):
- SC-001 (5 pts): App Launch
- SC-002 (8 pts): Barcode Scanning
- SC-004 (5 pts): Cart Management
- SC-005 (8 pts): Payment (Stripe)
- SC-007 (8 pts): Exit Validation
- SC-008 (8 pts): Escalation Handling
- SC-009 (13 pts): Manager Dashboard **[Consider splitting into 2 stories]**

**Total Sprint 1**: ~55 points (aggressive; may defer SC-009 if needed)

### Sprint 2 (Weeks 7-12): Polish & Enhancements
**Focus**: Feature completeness, refinement

- SC-003 (5 pts): QR Scanning
- SC-006 (5 pts): Receipt Generation
- Refinement, testing, bug fixes
- **TOTAL**: 10 pts + refinement

---

## 5. Dependency Resolution & Critical Path

### Critical Path (Longest Sequence)
```
SC-010 (13) → SC-001 (5) → SC-002 (8) → SC-004 (5) → SC-005 (8) → SC-007 (8)
Total: 47 points | Timeline: ~4 weeks
```

### Parallel Work Opportunities
- SC-011 can start immediately (doesn't block others)
- SC-003, SC-006 can start after SC-002, SC-004 are implemented
- SC-008, SC-009 can start once SC-002, SC-004, SC-005 basics exist

### High-Risk Dependencies
1. **ASSUMPTION-001 (POS API)**: Blocks entire system
   - Mitigation: Validate in Week 1 (POS audit)
   - Fallback: Mock POS API for development

2. **ASSUMPTION-004 (Exit Gate QR)**: Blocks SC-007
   - Mitigation: Site survey in Week 1
   - Fallback: Manual staff verification (scope reduction)

3. **ASSUMPTION-003 (Stripe Account)**: Blocks SC-005
   - Mitigation: Quick setup (1-2 days)
   - Risk: LOW

---

## 6. Story Effort Distribution

```
   13 pts  [SC-009][SC-010]
    8 pts  [SC-002][SC-005][SC-007][SC-008]
    5 pts  [SC-001][SC-003][SC-004][SC-006]
           └─────────────────────────────┘
           
   Total MVP: 76 points across 11 stories
   Average: 6.9 points per story
```

### Effort by Category

| Category | Stories | Total Points | % of Effort |
|----------|---------|--------------|------------|
| Core Customer Flow | SC-001 to SC-007 | 44 | 58% |
| Operations Support | SC-008, SC-009 | 21 | 27% |
| Technical Infrastructure | SC-010, SC-011 | 21 | 27% |

---

## 7. INVEST Validation Summary

### All Stories Pass INVEST
✅ **Independence**: Minimal blocking; most can be developed in parallel (after SC-010)  
✅ **Negotiable**: Details can be refined with team  
✅ **Valuable**: Clear user/business value in each story  
✅ **Estimable**: All stories have point estimates with justifications  
✅ **Small**: All stories fit in 1-3 week sprint (max 13 pts)  
✅ **Testable**: All have clear AC and can be verified by QA  

**No stories need splitting.** (SC-009 at 13 pts is large but self-contained)

---

## 8. Risk Inventory & Mitigations

### By Story

| Story | Risk Level | Key Risk | Mitigation |
|-------|-----------|----------|-----------|
| SC-001 | LOW | Platform fragmentation | Multi-platform QA early |
| SC-002 | HIGH | Barcode library bugs | Use proven (ZXing) library |
| SC-003 | MEDIUM | QR vs Barcode compatibility | Cross-format testing |
| SC-004 | MEDIUM | Cart state complexity | Redux/MobX testing |
| SC-005 | MEDIUM | Stripe integration bugs | Mock testing, SLA compliance |
| SC-006 | LOW | Email/SMS delivery | Use managed services (SendGrid/Twilio) |
| SC-007 | HIGH | Exit gate compatibility | **CRITICAL**: Week 1 site survey |
| SC-008 | MEDIUM | Escalation UX complexity | Iterative design with store staff |
| SC-009 | MEDIUM | Real-time performance | Load testing early (50+ txns) |
| SC-010 | HIGH | POS API incompatibility | **CRITICAL**: Week 1 audit |
| SC-011 | LOW | Network edge cases | Comprehensive offline testing |

---

## 9. Definition of Done (All Stories)

Each story must meet THESE criteria before marking "done":

- [ ] All acceptance criteria implemented and passing
- [ ] Code reviewed and approved by Tech Lead
- [ ] Unit tests written (>85% code coverage)
- [ ] Integration tests passing (with mocked dependencies)
- [ ] Manual QA testing completed and signed off
- [ ] Documentation updated (user guide, API docs as needed)
- [ ] No high/critical bugs open
- [ ] Performance targets verified (latency, concurrency)
- [ ] Security considerations addressed (where applicable)
- [ ] Story marked as "Done" in GitHub issues

---

## 10. Story Map Visual

```
WEEK 1     │ WEEK 2       │ WEEK 3-4          │ WEEK 5-6            │ WEEK 7-12
───────────┼──────────────┼───────────────────┼─────────────────────┼──────────────
Foundation │              │ MVP Core Features │ Advanced Features    │ Polish/UAT
───────────┼──────────────┼───────────────────┼─────────────────────┼──────────────
SC-010     │ SC-010 cont. │ SC-001            │ SC-003 (QR Code)    │ Refinement
POS        │ SC-011       │ SC-002 (Barcode)  │ SC-006 (Receipt)    │ Bug fixes
Integration│ Network      │ SC-004 (Cart)     │ Polish              │ UAT
───────────┼──────────────┼───────────────────┼─────────────────────┼──────────────
           │              │ SC-005 (Payment)  │                     │ Pilot Launch
           │              │ SC-007 (Exit Val) │                     │ Week 12
           │              │ SC-008 (Escalate) │                     │
           │              │ SC-009 (Mgr Dash) │                     │
```

---

## 11. Traceability: Stories to BRD Requirements

| Story | BRD Requirements | Coverage |
|-------|-----------------|----------|
| SC-001 | FR-1.1 | 100% |
| SC-002 | FR-1.2, FR-4.1 | 100% |
| SC-003 | FR-1.3 | 100% |
| SC-004 | FR-1.5, FR-1.6 | 100% |
| SC-005 | FR-1.7, FR-1.8, FR-5.1-5.5 | 100% |
| SC-006 | FR-1.10 | 100% |
| SC-007 | FR-2.1-2.5, FR-1.11 | 100% |
| SC-008 | FR-1.4 | 100% |
| SC-009 | FR-3.1-3.5 | 100% |
| SC-010 | FR-4.1-4.4 | 100% |
| SC-011 | NFR-7 | 100% |
| **TOTAL** | **All FRs & relevant NFRs** | **100%** |

---

## 12. Document Sign-Off

| Role | Name | Date | Status |
|------|------|------|--------|
| **Product Owner** | [Awaiting] | [ ] | [ ] |
| **Tech Lead** | [Awaiting] | [ ] | [ ] |
| **QA Lead** | [Awaiting] | [ ] | [ ] |

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-06-10 | Copilot CLI | Initial story decomposition from BRD |

---

**END OF STORY MAP**
