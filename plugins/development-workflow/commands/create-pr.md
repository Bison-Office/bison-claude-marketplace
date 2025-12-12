---
description: Create a PR and move Linear story to In Review
argument-hint: [BIS-1234]
allowed-tools: Bash(git:*), Bash(gh:*), Read, Glob
---

Create a PR for story $ARGUMENTS and move it to In Review.

## Context
- Current branch: !`git branch --show-current`
- Uncommitted changes: !`git status --short`
- Recent commits on branch: !`git log origin/main..HEAD --oneline 2>/dev/null || echo "No commits ahead of main"`

## Instructions

Use the `development-workflow` skill for PR creation guidelines.

## PR Checklist
- [ ] All changes committed
- [ ] Branch pushed to remote
- [ ] PR title prefixed with story ID (e.g., "BIS-1234: Description")
- [ ] PR description includes summary of changes
- [ ] Linear story moved to "In Review"

## Git Naming Convention
- **PR Title**: `BIS-XXXX: Short description`
- **Branch**: `feature/BIS-XXXX` or `fix/BIS-XXXX`
