---
description: Start code reviewing given story implementation
argument-hint: [BIS-1234]
allowed-tools: Bash(git:*), Read, Glob, Grep
---

Review code changes for story $ARGUMENTS against main branch.

## Context
- Current branch: !`git branch --show-current`
- Merge base: !`git merge-base origin/main HEAD 2>/dev/null || echo "Run 'git fetch origin' first"`

## Instructions

Use the `code-review-patterns` skill for the complete review process. It includes:
- Review structure and methodology
- Checklist for architectural validation
- References to supporting domain skills for technical validation

## Review Focus
- Architectural pattern adherence
- Logical bugs and edge cases
- Security vulnerabilities
- Performance implications
- Data integrity risks

Do NOT focus on cosmetic issues like formatting, naming conventions, or minor refactoring opportunities.
