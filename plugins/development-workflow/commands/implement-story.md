---
description: Start working on linear story
argument-hint: [BIS-1234]
allowed-tools: Bash(git:*), Bash(dotnet:*), Bash(npm:*), Read, Edit, Write, Glob, Grep
---

Implement story $ARGUMENTS.

## Context
- Current branch: !`git branch --show-current`
- Working directory status: !`git status --short`
- Uncommitted changes exist: !`git diff --stat HEAD 2>/dev/null | tail -1 || echo "Clean"`

## Instructions

Use the `development-workflow` skill, starting from **Stage 1 (Story Pickup)**. It orchestrates the implementation process and invokes supporting domain skills as needed.

## Implementation Checklist

```
Implementation Progress:
- [ ] Story fetched and understood
- [ ] Assigned to me, status "In Progress"
- [ ] Codebase investigated
- [ ] Implementation plan created
- [ ] Plan posted to Linear as comment
- [ ] User confirmation received
- [ ] Implementation completed
- [ ] Build/tests pass
```

## Key Steps

1. **Fetch Story**: Get story details from Linear
2. **Assign & Start**: Set assignee to "me", status to "In Progress"
3. **Investigate**: Explore codebase, identify affected files
4. **Plan**: Create implementation plan, post to Linear
5. **Confirm**: Present plan and wait for approval
6. **Implement**: Execute plan step by step
7. **Validate**: Run builds and tests

## Git Convention

When ready to commit:
- **Branch**: `feature/BIS-XXXX` or `fix/BIS-XXXX`
- **Commit**: `BIS-XXXX: Description of change`
