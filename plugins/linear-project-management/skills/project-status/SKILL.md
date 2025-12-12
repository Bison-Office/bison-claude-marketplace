---
name: project-status
description: Workflow for creating Linear project status updates. Use when creating weekly/milestone status updates for projects like "Pricing engine 2.0". Guides through reviewing completed stories, current focus, and generating structured updates.
---

# Project Status Update Workflow

## Purpose

This skill provides a structured workflow for creating comprehensive project status updates in Linear. It helps review recent progress, identify current focus areas, assess risks, and generate properly formatted status updates.

## When to Use This Skill

- Creating weekly or milestone project status updates
- User asks to create a status update for a project
- User asks to summarize project progress
- User mentions "project update" or "status update"

## Workflow Stages

### Stage 1: Gather Context

**Actions:**
1. Ask the user which project to update (default: "Pricing engine 2.0")
2. Request the last status update snapshot and date from the user
   - **Note**: Linear MCP does not provide access to project updates directly
   - User must provide the previous update content for comparison
3. Fetch recent issues using `mcp__linear__list_issues`

**Query Recent Issues:**
```
mcp__linear__list_issues:
  project: "Pricing engine 2.0"
  updatedAt: "-P14D"  # Last 14 days
  limit: 100
```

### Stage 2: Analyze Progress

**Review and categorize:**
1. **Completed Stories** - Issues moved to "Done" since last update
2. **In Progress** - Current active work
3. **New Issues Created** - Issues added since last update
4. **Blocked/At Risk** - Any stalled or problematic issues

**Ask clarifying questions:**
- Any context not clear from Linear issues
- Business impact of completed work
- Upcoming priorities or changes
- Known risks or blockers

### Stage 3: Generate Status Update

**Structure the update following this format:**

```markdown
**Summary**

[1-2 sentence high-level summary of progress and next major goal]

## Focus

**[Category 1 - e.g., System Stability & Reliability]**

- [Key accomplishment 1]
- [Key accomplishment 2]
- [Key accomplishment 3]

**[Category 2 - e.g., B2B Pricing Improvements]**

- [Key accomplishment 1]
- [Key accomplishment 2]

**[Category 3]**

- [Key accomplishment 1]

## Progress from previous update + Next steps

[Use status indicators:]
- Done: Previous goal fully completed
- In Progress: Work started, partial completion
- Mitigated: Risk reduced but not eliminated
- Next Goal: New target for upcoming period

1. **[Status] Goal description** - Progress details
   - Sub-items if needed
2. **[Status] Goal description** - Progress details
3. [Continue for each goal from previous update]
4. [Add new goals for next period]

## Active milestones

- **[X% of N]** Milestone name - brief status
- **[X% of N]** Milestone name - brief status
```

### Stage 4: Review & Refine

**Present draft to user:**
1. Show the generated update
2. Ask for feedback or corrections
3. Iterate until user is satisfied

**Final output:**
- Formatted status update ready to paste into Linear
- Clear, concise language
- Quantified progress where possible

## Status Indicators

| Indicator | Meaning | Usage |
|-----------|---------|-------|
| Done | Completed | Goal fully achieved |
| In Progress | Ongoing | Work started, not complete |
| Mitigated | Risk reduced | Problem addressed but not solved |
| Next Goal | New target | Goals for next update period |
| Blocked | Stuck | Cannot proceed, needs resolution |

## Best Practices

1. **Be specific** - Include concrete examples and numbers
2. **Focus on outcomes** - What was achieved, not just what was done
3. **Highlight risks early** - Don't hide problems
4. **Keep it scannable** - Use bullet points and headers
5. **Link to milestones** - Show progress toward larger goals
6. **Be honest about status** - Don't inflate progress

## Example Questions to Ask

- "What was the date of the last status update?"
- "Can you paste the previous update for comparison?"
- "Were there any blockers or issues not captured in Linear?"
- "Are there any upcoming risks or concerns?"
- "What should be the primary focus for the next period?"
- "Any team changes or capacity issues to note?"

## Quick Reference

### Fetch Project Issues
```
mcp__linear__list_issues:
  project: "Pricing engine 2.0"
  updatedAt: "-P7D"
  limit: 100
```

### Get Specific Issue Details
```
mcp__linear__get_issue:
  id: "BIS-XXXX"
```

### Get Project Info
```
mcp__linear__get_project:
  query: "Pricing engine 2.0"
```

## Checklist

- [ ] Got project name from user
- [ ] Received previous update content and date
- [ ] Fetched recent issues from Linear
- [ ] Categorized completed vs in-progress work
- [ ] Asked clarifying questions
- [ ] Generated structured update
- [ ] User reviewed and approved final version
