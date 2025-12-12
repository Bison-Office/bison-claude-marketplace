---
description: Full development workflow - Investigate, create story, implement, PR, and code review
argument-hint: [--auto] [describe the issue or feature]
allowed-tools: Bash(git:*), Bash(gh:*), Bash(dotnet:*), Bash(npm:*), Read, Edit, Write, Glob, Grep
---

Full workflow: investigate → create story → implement → PR → review.

$ARGUMENTS

## Context
- Current branch: !`git branch --show-current`
- Working directory status: !`git status --short`
- Recent commits: !`git log --oneline -3 2>/dev/null || echo "No recent commits"`

## Execution Mode

**If `--auto` flag present**: Skip confirmations, proceed automatically through all phases.

**Otherwise (default)**: Ask for user confirmation between major phases.

## Story ID Detection

**If argument is an existing Linear story ID (BIS-XXXX)**: Skip investigation and story creation, proceed directly to implementation.

**Otherwise**: Start from investigation phase.

## Instructions

Use the `development-workflow` skill for the complete development lifecycle. It orchestrates all phases and invokes supporting domain skills as needed.

## Progress Tracking

Copy and update as you proceed:

```
Development Progress:
- [ ] Phase 1: Investigation complete
- [ ] Phase 2: Story created in Linear
- [ ] Phase 3: Implementation plan approved
- [ ] Phase 4: Code implemented
- [ ] Phase 5: Build/tests passing
- [ ] Phase 6: PR created
- [ ] Phase 7: Code review complete
```

## Phase Breakdown

### Phase 1: Investigation
- Understand the issue or feature request
- Explore relevant code areas
- Document findings

### Phase 2: Story Creation
- Create Linear story with clear acceptance criteria
- Include technical notes from investigation

### Phase 3: Planning
- Create implementation plan
- Post plan to Linear as comment
- **Wait for approval** (unless `--auto`)

### Phase 4: Implementation
- Follow the plan step by step
- Use domain skills for specific patterns
- Track progress with TodoWrite

### Phase 5: Validation
- Run builds and tests
- Verify acceptance criteria met

### Phase 6: PR Creation
- Create branch following naming convention
- Push and create PR
- Update Linear status to "In Review"

### Phase 7: Code Review
- Review changes against architectural patterns
- Validate no critical issues
