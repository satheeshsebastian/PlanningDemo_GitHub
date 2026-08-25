# Stage 6: Execution Report Generation Guide

## Overview

**Stage 6** is a **mandatory, automatic stage** that runs at the end of every planning workflow (both `normal-planning.md` and `enhancement-planning.md`). It generates a comprehensive execution report documenting all workflow activities, metrics, and next steps.

## When to Execute

✅ **Automatic Trigger Points:**
1. After `github-issue-uploader` completes (Stage 5) in normal-planning
2. After GitHub sync completes in enhancement-planning
3. ALWAYS at end of workflow (success or failure)

✅ **No User Interaction Required**
- No approval gate
- No user questions
- No decisions needed

## Implementation Steps

### Step 1: Collect Stage Metadata

Before generating the report, gather data from all completed stages:

```
STAGE 1 DATA:
├─ Start time
├─ End time  
├─ Decision made (CREATE_NEW / ENHANCE_EXISTING / SKIP)
├─ Confidence score
├─ Artifacts found (count)
└─ Next stage route

STAGE 2 DATA:
├─ Start time
├─ End time
├─ BRD files created (paths, sizes)
├─ Requirements extracted (count: FR, NFR)
├─ Assumptions documented (count)
├─ Brainstorming sessions (count, topics)
├─ User approvals (YES/NO)
└─ Quality score

STAGE 3 DATA:
├─ Start time
├─ End time
├─ Stories created (count, point total)
├─ Story files paths
├─ Dependencies mapped (count)
├─ INVEST validation (pass/fail per story)
├─ Story map created (YES)
├─ User approvals (YES/NO)
└─ Quality score

STAGE 4 DATA:
├─ Start time
├─ End time
├─ Test cases generated (total count)
├─ Test coverage (%)
├─ Test files created (count, paths, sizes)
├─ Automation rate (%)
├─ User approvals (YES/NO)
└─ Quality score

STAGE 5 DATA:
├─ Start time
├─ End time
├─ GitHub issues created (count, #s)
├─ Labels applied (count)
├─ Dependencies linked (count)
├─ Traceability map created (YES)
├─ Repository URL
├─ User approvals (YES/NO)
└─ Quality score

ENHANCEMENT DATA (if applicable):
├─ BRD version bump (v1.0 → v1.1)
├─ Modified stories (count)
├─ New stories (count)
├─ Deprecated stories (count)
├─ Existing issues updated (count, #s)
└─ Backward compatibility (YES/NO)
```

AUDIT DATA (mandatory - from `features/audit/ai-signal-log-{RUN_ID}.jsonl`):
```
├─ Total events captured (by type: signal / action / human_gate / error)
├─ Actions by autonomy (auto / confirmed / overridden)
├─ Human interventions (question, response, override, AI original proposal)
├─ Errors and retries
├─ Token usage per stage (from the `llm` block of each event)
└─ Audit integrity result (AUDIT_COMPLETE / AUDIT_INCOMPLETE + gap list)
```

### Step 2: Calculate Aggregate Metrics

Compute totals and rollups:

```
TOTALS:
├─ Total execution duration
├─ Total stages completed
├─ Success rate (100% = all stages passed)
├─ Total artifacts created
├─ Total artifact size
├─ Requirements coverage (%)
├─ Test coverage (%)
├─ INVEST validation rate (%)
├─ GitHub sync success rate (%)
└─ Quality score (average of all stages)
```

### Step 3: Validate Quality Checks

Before finalizing report, run quality checks:

```
✅ QUALITY CHECKS:

1. Artifact Completeness
   └─ Verify all expected files exist:
      ├─ BRD: 1-2 files ✓
      ├─ User Stories: 11+ files ✓
      ├─ Test Cases: 2-5 files ✓
      ├─ GitHub Sync: 1 JSON file ✓
      └─ Story Map: 1 file ✓

2. Coverage Validation
   ├─ Requirement coverage ≥ 100% ✓
   ├─ Test coverage ≥ 100% of AC ✓
   ├─ INVEST validation ≥ 100% ✓
   └─ Traceability ≥ 100% ✓

3. Dependency Integrity
   ├─ Circular dependencies = 0 ✓
   ├─ Missing dependencies = 0 ✓
   └─ Unresolved dependencies = 0 ✓

4. GitHub Consistency
   ├─ Issue count matches story count ✓
   ├─ All labels applied ✓
   ├─ All dependencies linked ✓
   └─ Traceability map complete ✓

5. Documentation Completeness
   ├─ All acceptance criteria present ✓
   ├─ All story points assigned ✓
   ├─ All assumptions linked ✓
   └─ All next steps defined ✓

6. AI Signal & Action Audit (MANDATORY - see .github/rules/ai-audit-standards.md)
   ├─ Signal log exists and is valid JSONL ✓
   ├─ Every executed stage has ≥1 event ✓
   ├─ Every created artifact traced to an action ✓
   ├─ Every human gate captured ✓
   ├─ Token counts present on every LLM call ✓
   └─ No secrets/PII in the log ✓
```

**Quality Gate**: If the audit check returns `AUDIT_INCOMPLETE`, Stage 6 is marked FAILED.
Generate the report anyway, list the gaps, and backfill any reconstructable events
(`"reconstructed": true`) before continuing to Stage 7.

### Step 4: Generate Report from Template

Use `WORKFLOW-EXECUTION-REPORT-TEMPLATE.md` to create report:

```
1. Copy template
2. Replace all {PLACEHOLDERS} with actual values
3. Fill in all Stage 1-6 details
4. Calculate metrics and totals
5. Generate execution timeline
6. Add recommendations based on findings
7. Save to: features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md
```

### Step 5: Validate Report Content

Check report completeness:

```
✓ Report ID (WORKFLOW-RUN-YYYY-MM-DD-{slug})
✓ Run date and times
✓ All 6 stages documented
✓ All metrics calculated
✓ All artifacts referenced
✓ GitHub issues linked
✓ Success criteria checklist
✓ Next steps recommendations
✓ Timeline visualization
✓ Quality metrics table
```

## Report File Naming

```
Features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md

Examples:
├─ PLANNING-WORKFLOW-EXECUTION-2026-06-10.md (first run)
├─ PLANNING-WORKFLOW-EXECUTION-2026-06-11.md (next day run)
├─ PLANNING-WORKFLOW-EXECUTION-2026-06-15.md (enhancement run)
```

## Report Structure

```
PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md
├─ Header (Report ID, date, duration, status)
├─ Workflow Overview (table)
├─ Stage 1: Enhancement Detection (6 subsections)
├─ Stage 2: BRD Generation (6 subsections)
├─ Stage 3: User Stories (6 subsections)
├─ Stage 4: Test Cases (6 subsections)
├─ Stage 5: GitHub Integration (6 subsections)
├─ Stage 6: Report Generation (3 subsections)
├─ Aggregated Metrics (5 subsections)
├─ Success Criteria Achievement
├─ Recommendations & Next Steps
├─ Timeline Visualization
└─ Appendix

Total: ~35-50 KB per report
Sections: 50-60 markdown sections
Tables: 10-15 detailed tables
```

## Key Sections Explained

### 1. Workflow Overview Table
- Single table showing workflow name, stage count, completion status
- Used for quick scanning of workflow health

### 2. Stage Details (6 per stage)
Each stage section includes:
- **Overview**: Timing, status, result summary
- **Input Artifacts**: What stage received as input
- **Processing Details**: LLM config, execution details
- **Output Artifacts**: What stage created (files, counts, sizes)
- **Quality Assessment**: Pass/fail checks
- **Rules Applied**: Which workflow rules were enforced

### 3. Aggregated Metrics
- **Execution Summary**: Total duration, stage count, success %
- **Artifacts Generated**: Count & size by type
- **Token Usage**: Per stage (if tracked with LLM)
- **Quality Metrics**: Coverage, validation, traceability checks
- **Next Steps**: 1-5 week outlook

## Recommendations Section

Generate context-aware recommendations based on:

```
IF normal-planning:
  └─ Recommend: Code review readiness (stories→GitHub)
  └─ Suggest: Architecture validation
  └─ Advise: Sprint planning prep
  └─ Highlight: Critical assumptions to validate
  └─ Note: QA prep timeline

IF enhancement-planning:
  └─ Recommend: Backward compatibility validation
  └─ Suggest: Migration path testing
  └─ Advise: Customer communication (if breaking changes)
  └─ Highlight: Impact on existing features
  └─ Note: Rollback procedures
```

## Error Handling

If any stage fails:

```
1. Still generate report (document failures)
2. Mark failed stages with ❌ FAILED
3. Include error details in stage section
4. Provide troubleshooting recommendations
5. Save report even if incomplete
6. Alert team to incomplete workflow
```

## Timing Expectations

```
Report Generation Time:
├─ Data collection: <1 min
├─ Quality checks: <1 min
├─ Report generation: <2 min
├─ File writing: <30 sec
└─ TOTAL: ~5 minutes
```

## Checklist for Report Quality

```
✓ All placeholders replaced with real values
✓ All timestamps accurate and formatted consistently
✓ All file paths use forward slashes (GitHub)
✓ All GitHub issue numbers included (#13-#23)
✓ All tables properly formatted (Markdown)
✓ All metrics sum correctly (e.g., story points)
✓ All coverage claims backed by numbers (e.g., "100% AC coverage")
✓ All recommendations are actionable (specific, time-bound)
✓ All next steps have owners (implicit or explicit)
✓ Report is under 50 KB (compression-friendly)
```

## Success Criteria

Report generation is successful when:

```
✅ Report file created at: features/reports/PLANNING-WORKFLOW-EXECUTION-{RUN_DATE}.md
✅ File size: 35-50 KB (normal-planning), 40-60 KB (enhancement-planning)
✅ All 6 stages documented with full details
✅ All metrics calculated and validated
✅ All artifacts referenced with file paths
✅ GitHub integration fully documented
✅ Next steps clearly outlined
✅ Report readable in GitHub web UI (markdown renders)
✅ Timeline visualization present
✅ Quality checks all passing (✓ marks visible)
```

## Integration with Workflow

**How Stage 6 integrates with other stages:**

```
Stage 1 → Stage 2
├─ Enhancement decision routes to normal-planning or enhancement-planning
└─ Stage 6 documents this routing decision

Stage 2 → Stage 3
├─ BRD created; Stage 6 documents content
└─ Stage 6 tracks user approval

Stage 3 → Stage 4
├─ Stories created; Stage 6 documents count & points
└─ Stage 6 validates INVEST criteria

Stage 4 → Stage 5
├─ Tests created; Stage 6 documents coverage
└─ Stage 6 calculates automation %

Stage 5 → Stage 6
├─ Issues created; Stage 6 documents GitHub links
└─ Stage 6 validates traceability complete

Stage 6 → Stage 7
├─ Report + audit log handed to result-analyzer
└─ Audit integrity result gates the analysis

Stage 7 → Stage 8 → Stage 9
├─ Findings → verifier-based scores & rewards → RL policy update
└─ Next steps published for the team and for the next run

[FINAL] → Commit/Push
├─ Report generated; ready for version control
└─ Report + signal log serve as the audit trail for this workflow run
```

## Examples

See `features/reports/PLANNING-WORKFLOW-EXECUTION-2026-06-10.md` for a complete example of a Stage 6 report from a Smart Coupon workflow run.

---

**Last Updated**: 2026-06-10  
**Version**: 1.0 (Stage 6 Guide)  
**Status**: Production Ready
