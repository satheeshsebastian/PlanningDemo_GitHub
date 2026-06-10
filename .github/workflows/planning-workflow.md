---
name: planning-workflow
description: Master Planning Workflow - Routes requirements to normal or enhancement path
---

# Planning Workflow - Master Orchestration

Transforms requirements into BRDs, user stories, tests, and GitHub issues.

## Workflow Routing

```
USER INPUT
    ↓
Stage 1: enhancement-detector
    ├─ Search: features/brd/ & features/user-stories/
    ├─ Confidence < 40%  → normal-planning.md
    └─ Confidence > 70%  → enhancement-planning.md
```

## Path Selection

| Scenario | Workflow |
|----------|----------|
| **New Feature** | `normal-planning.md` |
| **Enhancement** | `enhancement-planning.md` |

## Skills Reference

See individual skill files for detailed logic:

- `enhancement-detector` → `.github/skills/enhancement-detector/SKILL.md`
- `brd-generator` → `.github/skills/brd-generator/SKILL.md`
- `user-story-builder` → `.github/skills/user-story-builder/SKILL.md`
- `enhancement-modifier` → `.github/skills/enhancement-modifier/SKILL.md`
- `enhancement-story-updater` → `.github/skills/enhancement-story-updater/SKILL.md`
- `functional-test-writer` → `.github/skills/functional-test-writer/SKILL.md`
- `github-issue-uploader` → `.github/skills/github-issue-uploader/SKILL.md`

## File Structure

```
.github/
├── workflows/
│   ├── planning-workflow.md          ← Routing (this file)
│   ├── normal-planning.md            ← New feature path
│   └── enhancement-planning.md       ← Enhancement path
└── skills/
    ├── brd-generator/SKILL.md
    ├── user-story-builder/SKILL.md
    ├── functional-test-writer/SKILL.md
    ├── enhancement-detector/SKILL.md
    ├── enhancement-modifier/SKILL.md
    ├── enhancement-story-updater/SKILL.md
    └── github-issue-uploader/SKILL.md

features/
├── brd/[slug]-v*.md
├── user-stories/[slug-*.md]
├── test-cases/[slug]-test-cases.md
└── github-sync/[slug]-issue-map.json
```

## Documentation

**For new features**: See `normal-planning.md`
**For enhancements**: See `enhancement-planning.md`