---
name: development-workflow
description: Provides complete development workflow from Linear story pickup to completion. Covers investigation, planning, implementation, PR creation, and status updates. Use when implementing features, fixing bugs, working on Linear stories, creating PRs, starting development work, or when user asks to "work on", "implement", "build", or "develop" a feature. Also triggers for production bugs and issue investigation.
---

# Development Workflow

## Execution Modes

**Interactive (default)**: Wait for user confirmation before implementing.

**Auto (`--auto` flag)**: Skip confirmations, proceed automatically. Use when user explicitly passes `--auto`.

## Workflow Stages

### Stage 1: Story Pickup

1. Fetch issue: `Linear:get_issue(id: "BIS-XXXX")`
2. Assign and start: `Linear:update_issue(id: "uuid", assignee: "me", state: "In Progress")`
3. Present issue details to user

### Stage 2: Investigation & Planning

1. Explore codebase, identify affected files
2. Read `architecture-patterns` skill for the relevant project
3. Create implementation plan (template below)
4. Post plan as comment: `Linear:create_comment(issueId: "uuid", body: "...")`

**Plan Template:**
```markdown
## Implementation Plan for {BIS-XXXX}: {Title}

### Understanding
[Brief summary]

### Affected Areas
- **Files to modify**: `path/file.cs` - [changes]
- **New files**: `path/new.cs` - [purpose]

### Implementation Steps
1. [Step]
2. [Step]

### Testing Approach
- [Verification method]

### Risks
- [Considerations]
```

### Stage 3: User Confirmation

**Skip if `--auto` mode.**

1. Present plan to user
2. Ask: "Should I proceed with this implementation plan?"
3. **WAIT** for explicit approval
4. If rejected: revise plan, re-confirm

### Stage 4: Implementation

1. Use TodoWrite to track progress
2. Invoke domain skills as needed for specific project that changes will be implemented
3. Implement step by step, following existing patterns
4. Run builds/tests

### Stage 5: Completion

1. Verify all changes complete, tests pass
2. Summarize what was done
3. Ask about committing changes
4. Update status: `Linear:update_issue(id: "uuid", state: "In Review")`

## Git Naming Convention

**CRITICAL: Prefix commits and PR titles with Linear issue ID for automatic linking.**

| Type | Format |
|------|--------|
| Branch | `feature/BIS-XXXX` or `fix/BIS-XXXX` |
| Commit | `BIS-XXXX: Short description` |
| PR Title | `BIS-XXXX: Short description` |

## Deployable Units

For stories with multiple mergeable pieces:
1. Create subtasks in Linear for each piece
2. Create separate branches/PRs for each subtask
3. Each PR should be independently mergeable

## Status Flow

```
Triage/Backlog → In Progress → In Review
                     ↑              ↓
                     └──────────────┘ (changes needed)
```

## Checklist

- [ ] Issue fetched and understood
- [ ] Assigned to me, status "In Progress"
- [ ] Codebase investigated
- [ ] Implementation plan created and posted to Linear
- [ ] User confirmation received (skip if `--auto`)
- [ ] Implementation completed
- [ ] Build/tests pass
- [ ] Status updated (In Review)
