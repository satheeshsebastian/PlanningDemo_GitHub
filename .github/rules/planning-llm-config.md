# Planning Workflow LLM Configuration
## Skill-to-LLM Role Mapping with Primary & Secondary Options

**Document ID**: LLM-CONFIG-001  
**Version**: 1.0  
**Last Updated**: 2026-06-10  
**Status**: Active  

---

## Overview

This configuration defines which Large Language Models (LLMs) are recommended for each planning skill based on role and capability requirements. Each skill has a **Primary Option** (recommended) and **Secondary Option** (fallback).

The workflow engine should:
1. Check this config at skill execution time
2. Use Primary Option if available
3. Fall back to Secondary Option if Primary unavailable
4. Document actual LLM used in execution report

---

## LLM CAPABILITY MATRIX

### Primary LLM Options

| LLM | Reasoning | Analysis | BDD | Compliance | Long Context | Best For |
|-----|-----------|----------|-----|-----------|--------------|----------|
| **Claude Sonnet 4.6** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 200K tokens | Primary (all-around) |
| **Claude Opus 4.8** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 200K tokens | Complex analysis |
| **GPT-5.5** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | Variable | Fallback option |
| **Claude Haiku 4.5** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | 200K tokens | Fast (not recommended) |

---

## SKILL-SPECIFIC LLM CONFIGURATION

### SKILL 1: BRD Generator

**Role**: Business Analyst / Requirements Analyst  
**Key Capability**: Complex reasoning, BMAD analysis, brainstorming synthesis

```yaml
skill: brd-generator
workflow_step: Stage 2 (BRD Generation)

primary_llm:
  name: Claude Sonnet 4.6
  version: latest
  rationale: |
    - Excellent at BMAD analysis (Brainstorming, Motivation, Acceptance, Definition)
    - Strong reasoning for synthesizing user responses
    - Good at generating comprehensive, well-structured documents
    - Efficient token usage vs. quality output
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.7  # Balanced - some creativity but structured
    max_tokens: 4000  # Full BRD generation
    system_prompt: |
      You are an expert Business Analyst specializing in BMAD analysis.
      Generate comprehensive, structured Business Requirement Documents.
      Follow planning-standards.md template exactly.

secondary_llm:
  name: Claude Opus 4.8
  version: latest
  rationale: |
    - Highest quality reasoning
    - Better for complex or ambiguous requirements
    - More expensive but higher accuracy
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.6  # More structured
    max_tokens: 5000

tertiary_fallback:
  name: GPT-5.5
  version: latest
  note: Use only if Claude models unavailable

token_estimate:
  input_tokens: 2000-3000 (requirement input + context)
  output_tokens: 2000-4000 (BRD generation)
  total_estimate: 4000-7000 tokens
```

---

### SKILL 2: User Story Builder

**Role**: Product Manager / Agile Coach  
**Key Capability**: Decomposition, dependency analysis, INVEST validation

```yaml
skill: user-story-builder
workflow_step: Stage 3 (Story Decomposition)

primary_llm:
  name: Claude Sonnet 4.6
  version: latest
  rationale: |
    - Excellent at breaking down complex requirements
    - Strong dependency analysis
    - Good at INVEST principle validation
    - Efficient for large story sets
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.6  # Lower temp for consistency
    max_tokens: 5000  # Multiple stories
    system_prompt: |
      You are a Product Manager specializing in user story decomposition.
      Follow INVEST principles (Independent, Negotiable, Valuable, Estimable, Small, Testable).
      Each story must include: statement, AC, dependencies, MoSCoW, point breakdown.

secondary_llm:
  name: Claude Opus 4.8
  version: latest
  rationale: |
    - Better for complex dependency graphs
    - More rigorous INVEST validation
    - Higher accuracy for large requirements
  context_window: 200K tokens

token_estimate:
  input_tokens: 3000-5000 (BRD + traceability context)
  output_tokens: 3000-6000 (10+ stories with details)
  total_estimate: 6000-11000 tokens
```

---

### SKILL 3: Functional Test Writer

**Role**: QA Engineer / Test Architect  
**Key Capability**: BDD Gherkin format, test case generation, coverage analysis

```yaml
skill: functional-test-writer
workflow_step: Stage 4 (Test Case Generation)

primary_llm:
  name: Claude Sonnet 4.6
  version: latest
  rationale: |
    - Excellent BDD/Gherkin format generation
    - Strong at generating comprehensive test scenarios
    - Good systematic approach to negative testing
    - Handles both functional and non-functional tests
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.5  # Deterministic test generation
    max_tokens: 6000  # Large test suite
    system_prompt: |
      You are a QA test architect specializing in BDD testing.
      Generate test cases in Given/When/Then format.
      Include functional, negative, and non-functional tests.
      Each test must include: ID, scenario type, AC reference, traceability.

secondary_llm:
  name: Claude Opus 4.8
  version: latest
  rationale: |
    - Highest quality test coverage
    - Better at security/compliance test design
    - More thorough negative test scenarios
  context_window: 200K tokens

token_estimate:
  input_tokens: 3000-4000 (10 stories + AC)
  output_tokens: 4000-8000 (80+ test cases)
  total_estimate: 7000-12000 tokens
```

---

### SKILL 4: Enhancement Detector

**Role**: Requirements Analyst / Change Manager  
**Key Capability**: Semantic matching, impact analysis, decision support

```yaml
skill: enhancement-detector
workflow_step: Stage 1 (Enhancement Detection)

primary_llm:
  name: Claude Sonnet 4.6
  version: latest
  rationale: |
    - Excellent semantic matching beyond exact keywords
    - Strong at impact analysis and comparison
    - Good at providing recommendations
    - Efficient for searching/analyzing existing artifacts
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.7  # Some reasoning for analysis
    max_tokens: 3000  # Analysis + recommendation
    system_prompt: |
      You are a requirements analyst specializing in enhancement detection.
      Perform semantic matching on existing artifacts.
      Provide impact analysis for UPDATE vs CREATE decisions.
      Recommend MINOR vs MAJOR versioning.

secondary_llm:
  name: Claude Opus 4.8
  version: latest
  rationale: |
    - Better nuanced analysis
    - More thorough impact assessment
    - Safer recommendations for large changes
  context_window: 200K tokens

token_estimate:
  input_tokens: 2000-4000 (new requirement + existing artifacts)
  output_tokens: 1000-3000 (analysis + recommendation)
  total_estimate: 3000-7000 tokens
```

---

### SKILL 5: GitHub Issue Uploader

**Role**: DevOps Engineer / Release Manager  
**Key Capability**: Structured data formatting, automation, integration

```yaml
skill: github-issue-uploader
workflow_step: Stage 5 (GitHub Integration)

primary_llm:
  name: Claude Sonnet 4.6
  version: latest
  rationale: |
    - Excellent at structured formatting
    - Good at generating GitHub-compatible content
    - Efficient for JSON generation and automation
    - Lower token overhead
  context_window: 200K tokens
  recommended_settings:
    temperature: 0.3  # Very deterministic
    max_tokens: 2000  # Issue bodies + JSON
    system_prompt: |
      You are a DevOps engineer specializing in GitHub automation.
      Generate structured GitHub issues with complete traceability.
      Create valid JSON for issue mapping.
      Ensure all IDs, links, and labels are correct.

secondary_llm:
  name: Claude Opus 4.8
  version: latest
  rationale: |
    - If more complex formatting needed
    - Better at handling large artifact sets
  context_window: 200K tokens

note: |
  This skill is mostly automation and could also use lighter models
  for pure formatting, but recommend Sonnet for consistency.

token_estimate:
  input_tokens: 2000-3000 (10 stories + test cases)
  output_tokens: 1500-3000 (issue bodies + JSON)
  total_estimate: 3500-6000 tokens
```

---

## WORKFLOW-LEVEL LLM SELECTION RULES

### Rule 1: Skill-Based Primary Selection
Each skill uses its configured **primary_llm** by default:
```
BRD Generator → Claude Sonnet 4.6
Story Splitter → Claude Sonnet 4.6
Test Writer → Claude Sonnet 4.6
Enhancement Detector → Claude Sonnet 4.6
GitHub Uploader → Claude Sonnet 4.6
```

### Rule 2: Complexity-Based Escalation
Use **secondary_llm** (Claude Opus 4.8) if:
- Primary LLM is unavailable
- Requirements rated "Complex" or "Very Complex"
- Large artifact sets (>20 stories, >100 test cases)
- Regulatory/compliance requirements detected
- User explicitly requests higher accuracy

**Detection Triggers**:
```yaml
escalate_to_secondary_if:
  - complexity_level: "Complex" or "Very Complex"
  - story_count: "> 20"
  - test_case_count: "> 100"
  - requirement_size: "> 10K tokens"
  - has_compliance_requirements: true
  - user_preference: "accuracy_over_cost"
```

### Rule 3: Token Budget Awareness
Total workflow token estimate:
```
Stage 1 (Enhancement Detection):  3K-7K tokens
Stage 2 (BRD Generation):         4K-7K tokens
Stage 3 (Story Decomposition):    6K-11K tokens
Stage 4 (Test Generation):        7K-12K tokens
Stage 5 (GitHub Upload):          3.5K-6K tokens
────────────────────────────────
TOTAL (5 stages):                 24K-43K tokens (per full run)
```

**Token Budget Rules**:
- If total estimated tokens < 30K: Use primary LLM (Sonnet 4.6)
- If 30K-50K: Use secondary LLM (Opus 4.8) for critical stages
- If > 50K: Split stages, use lighter models where possible

### Rule 4: Consistency Within Workflow Run
**Important**: Once an LLM is selected for a workflow run, maintain consistency:
- If Stage 2 uses Opus 4.8, recommend using Opus 4.8 for Stages 3-5
- Document which LLM versions used in execution report
- Don't switch between Claude and GPT mid-workflow

---

## LLM SELECTION DECISION TREE

```
Start Workflow Run
  │
  ├─ Check: Is this an Enhancement Detection (Stage 1)?
  │   └─ YES: Use Enhancement Detector config
  │   └─ NO: Continue
  │
  ├─ Check: Is this a full workflow or single stage?
  │   └─ FULL: Calculate total token estimate
  │   └─ SINGLE: Use skill config
  │
  ├─ For FULL workflow:
  │   ├─ Token estimate < 30K?
  │   │   └─ YES: Use Sonnet 4.6 for all stages
  │   │   └─ NO: Continue
  │   │
  │   ├─ Has complexity_level: "Complex/Very Complex"?
  │   │   └─ YES: Use Opus 4.8 (higher quality)
  │   │   └─ NO: Continue
  │   │
  │   ├─ Has compliance requirements?
  │   │   └─ YES: Use Opus 4.8 (safer)
  │   │   └─ NO: Use Sonnet 4.6 (efficient)
  │   │
  │   └─ User preference?
  │       └─ "accuracy": Use Opus 4.8
  │       └─ "speed": Use Sonnet 4.6
  │       └─ "balanced": Use Sonnet 4.6
  │
  └─ Document choice in execution report
     └─ Include: LLM name, version, rationale, estimated tokens
```

---

## EXECUTION REPORT LLM TRACKING

Every workflow execution must capture:

```json
{
  "execution_id": "EXE-2026-06-10-001",
  "workflow_name": "planning-workflow",
  "brd_name": "smart-checkout-assistant",
  "start_time": "2026-06-10T11:00:00Z",
  "end_time": "2026-06-10T12:30:00Z",
  "stages": [
    {
      "stage_number": 2,
      "stage_name": "BRD Generation",
      "skill": "brd-generator",
      "llm_used": {
        "model": "Claude Sonnet 4.6",
        "version": "4.6-20250305",
        "selection_reason": "Primary option for business analysis",
        "escalation_reason": null,
        "fallback_used": false
      },
      "tokens": {
        "input_tokens": 2847,
        "output_tokens": 3205,
        "total_tokens": 6052
      },
      "execution_metrics": {
        "duration_seconds": 45,
        "quality_score": 0.92,
        "approval_required": true,
        "approval_status": "approved"
      },
      "artifacts": {
        "input": [
          {
            "type": "requirement_document",
            "name": "smart-checkout-requirements.docx",
            "size_bytes": 45000
          }
        ],
        "output": [
          {
            "type": "brd",
            "name": "smart-checkout-assistant-v1.0.md",
            "size_bytes": 12800,
            "document_id": "BRD-2026-06-10-smart-checkout"
          },
          {
            "type": "assumptions",
            "name": "smart-checkout-assistant-assumptions.md",
            "size_bytes": 2500,
            "count": 5
          }
        ]
      },
      "decisions": [
        "Identified 5 critical assumptions from requirement",
        "Requested clarification on 3 vague requirements (SPEC-IT validation)",
        "Approved by product owner after revision"
      ],
      "key_metrics": {
        "requirements_count": 47,
        "assumptions_count": 5,
        "risks_identified": 8,
        "success_criteria": 12
      }
    },
    {
      "stage_number": 3,
      "stage_name": "User Story Decomposition",
      "skill": "user-story-builder",
      "llm_used": {
        "model": "Claude Sonnet 4.6",
        "version": "4.6-20250305",
        "selection_reason": "Primary option maintained for consistency",
        "escalation_reason": null,
        "fallback_used": false
      },
      "tokens": {
        "input_tokens": 4205,
        "output_tokens": 5120,
        "total_tokens": 9325
      },
      "execution_metrics": {
        "duration_seconds": 52,
        "quality_score": 0.89,
        "approval_required": true,
        "approval_status": "approved"
      },
      "artifacts": {
        "input": [
          {
            "type": "brd",
            "name": "smart-checkout-assistant-v1.0.md",
            "requirements_count": 47
          }
        ],
        "output": [
          {
            "type": "user_stories",
            "count": 10,
            "total_size_bytes": 39200,
            "files": [
              "customer-can-authenticate.md",
              "customer-can-scan-items.md",
              "..."
            ]
          },
          {
            "type": "story_map",
            "name": "story-map-smart-checkout.md",
            "size_bytes": 9100
          },
          {
            "type": "traceability",
            "name": "story-traceability.json",
            "size_bytes": 4500,
            "requirement_to_story_mappings": 47
          }
        ]
      },
      "decisions": [
        "Split 47 requirements into 10 user stories",
        "Identified 3 high-dependency stories needing reordering",
        "Applied MoSCoW: 4 MUST, 4 SHOULD, 2 COULD",
        "Validated all stories pass INVEST criteria"
      ],
      "key_metrics": {
        "stories_count": 10,
        "total_story_points": 80,
        "moscow_distribution": {
          "must": 4,
          "should": 4,
          "could": 2
        },
        "dependency_graph_edges": 12,
        "assumptions_referenced": 5
      }
    },
    {
      "stage_number": 4,
      "stage_name": "Functional Test Generation",
      "skill": "functional-test-writer",
      "llm_used": {
        "model": "Claude Sonnet 4.6",
        "version": "4.6-20250305",
        "selection_reason": "Primary option maintained for consistency",
        "escalation_reason": null,
        "fallback_used": false
      },
      "tokens": {
        "input_tokens": 3800,
        "output_tokens": 6520,
        "total_tokens": 10320
      },
      "execution_metrics": {
        "duration_seconds": 58,
        "quality_score": 0.91,
        "approval_required": false,
        "coverage_percent": 98.5
      },
      "artifacts": {
        "input": [
          {
            "type": "user_stories",
            "count": 10,
            "acceptance_criteria_count": 32
          }
        ],
        "output": [
          {
            "type": "test_cases",
            "total_count": 82,
            "breakdown": {
              "functional": 62,
              "negative": 15,
              "nonfunctional": 5
            },
            "files": [
              "functional-test-cases.md"
            ]
          }
        ]
      },
      "decisions": [
        "Generated 82 test cases from 32 acceptance criteria",
        "Added 15 negative tests for security (SQL injection, XSS)",
        "Identified 5 non-functional tests for performance",
        "Coverage: 98.5% of AC",
        "Flagged 3 performance tests for automation priority"
      ],
      "key_metrics": {
        "test_cases_total": 82,
        "coverage_percent": 98.5,
        "critical_tests": 28,
        "high_tests": 34,
        "medium_tests": 15,
        "low_tests": 5,
        "automated_tests": 60,
        "manual_tests": 22
      }
    },
    {
      "stage_number": 5,
      "stage_name": "GitHub Issue Upload",
      "skill": "github-issue-uploader",
      "llm_used": {
        "model": "Claude Sonnet 4.6",
        "version": "4.6-20250305",
        "selection_reason": "Primary option for structured formatting",
        "escalation_reason": null,
        "fallback_used": false
      },
      "tokens": {
        "input_tokens": 2650,
        "output_tokens": 2800,
        "total_tokens": 5450
      },
      "execution_metrics": {
        "duration_seconds": 120,
        "quality_score": 1.0,
        "api_calls": 13,
        "github_validation_passed": true
      },
      "artifacts": {
        "input": [
          {
            "type": "user_stories",
            "count": 10
          },
          {
            "type": "test_cases",
            "count": 82
          }
        ],
        "output": [
          {
            "type": "github_issues",
            "count": 13,
            "breakdown": {
              "story_issues": 10,
              "test_suite_issues": 3
            },
            "urls": [
              "https://github.com/org/repo/issues/123",
              "..."
            ]
          },
          {
            "type": "issue_map",
            "name": "smart-checkout-issue-map.json",
            "size_bytes": 8400,
            "requirement_to_issue_mappings": 47
          }
        ]
      },
      "decisions": [
        "Created 10 story issues with full traceability",
        "Created 3 test suite grouping issues",
        "Applied 5 milestones (Phase 1: Core, Phase 2: Enhancements)",
        "Assigned to 3 team members based on role",
        "Linked all dependencies between issues"
      ],
      "key_metrics": {
        "issues_created": 13,
        "milestones_created": 2,
        "labels_applied": 47,
        "dependencies_linked": 12,
        "team_assignments": 3,
        "traceability_mappings": 47
      }
    }
  ],
  "summary": {
    "total_duration_seconds": 275,
    "total_tokens_used": 31147,
    "average_quality_score": 0.915,
    "llm_distribution": {
      "Claude Sonnet 4.6": {
        "stages": [2, 3, 4, 5],
        "total_tokens": 31147,
        "cost_estimate_usd": 0.94
      },
      "Claude Opus 4.8": {
        "stages": [],
        "total_tokens": 0
      }
    },
    "artifacts_generated": {
      "brd": 1,
      "assumptions_docs": 1,
      "user_stories": 10,
      "story_maps": 1,
      "traceability_files": 1,
      "test_suites": 1,
      "github_issues": 13,
      "issue_maps": 1
    },
    "key_decisions": [
      "Maintained Sonnet 4.6 throughout for consistency",
      "All quality scores above 0.89",
      "Two approval gates passed successfully",
      "Complete end-to-end traceability achieved (Req→Story→Test→Issue)",
      "Total of 47 requirements satisfied with 10 stories and 82 test cases"
    ],
    "recommendations": [
      "Consider Opus 4.8 for future complex enhancements",
      "Current token usage (31K) within efficient range",
      "Quality output suitable for production deployment"
    ]
  }
}
```

---

## CONFIGURATION USAGE RULES

### For Workflow Engine Developers
1. **Load Config**: Read this file at workflow start
2. **Determine Skill**: Based on current stage
3. **Get Primary LLM**: From skill config
4. **Check Escalation Triggers**: Apply decision rules
5. **Use Secondary if Needed**: Fallback to secondary_llm
6. **Document Everything**: Capture in execution report

### For Skill Developers
1. **Accept LLM Parameter**: Receive model name at skill invocation
2. **Set System Prompt**: Use recommended_settings.system_prompt
3. **Optimize Temperature**: Follow recommended_settings.temperature
4. **Track Tokens**: Count input/output tokens
5. **Report Usage**: Return token counts to workflow

### For Operations/Monitoring
1. **Review Execution Reports**: Check LLM choices and token usage
2. **Monitor Costs**: Track token spend across workflow runs
3. **Identify Escalations**: Find stages using secondary LLMs
4. **Optimize**: Adjust thresholds if needed
5. **Archive**: Save reports for compliance/audit

---

## FUTURE EXTENSIONS

### Support for New Models
When new LLMs available, add to config:
```yaml
new_model:
  name: Claude Opus 5.0
  capabilities: [reasoning, analysis, bdd, compliance, long_context]
  recommended_for: [complex_requirements, regulatory]
  token_limit: 200K
```

### Custom LLM Per Organization
Allow overrides per org/team:
```yaml
overrides:
  organization: "Capgemini"
  brd_generator: "Claude Opus 4.8"  # Always use highest quality
  budget_limit_tokens_per_run: 50000
```

### Token Budget Management
Track token spend across runs:
```yaml
budget:
  daily_limit: 500000 tokens
  monthly_limit: 10000000 tokens
  per_workflow_limit: 50000 tokens
  alert_threshold: 80%
```

---

## VERSION HISTORY

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-06-10 | Initial config with 5 skills, 2 LLM tiers |
| TBD | TBD | Support for additional models |
| TBD | TBD | Per-organization overrides |
| TBD | TBD | Token budget management |

---

**Status**: Active - Use for all workflow executions  
**Next Review**: 2026-09-10 or when new LLMs available  
**Owner**: Planning Workflow Team  
