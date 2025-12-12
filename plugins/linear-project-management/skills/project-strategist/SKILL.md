---
name: project-strategist
description: Reviews and improves Linear projects interactively. Use when starting a new project, reviewing project health, improving documentation, creating status updates, or bringing an unstructured project up to professional standards.
---

# Linear Project Strategy

## Quick Start

1. Fetch project: `linear:get_project` with project name
2. Fetch recent issues: `linear:list_issues` with `project` and `updatedAt: "-P30D"`
3. Assess against checklist below
4. Work interactively to improve

## Core Principle: Never Assume

- Ambiguous requirements → Ask clarifying questions
- Implicit assumptions → Surface and confirm
- Missing information → Document as "To Be Clarified"

## Assessment Checklist

| Section | Check |
|---------|-------|
| Problem/Why | Specific? Quantified impact? |
| Solution | High-level approach clear? |
| Scope | In/out defined? |
| Active Milestones | Each has Definition of Done? |
| Key People | Roles and ownership clear? |
| Project Updates | Regular updates being added? |

## Workflows

**Reviewing existing project:**
1. Gather current state (project + recent issues)
2. Assess against checklist
3. Identify gaps (one topic at a time)
4. Challenge and get user input
5. Update with user approval

**Creating new project:**
1. Ask: Purpose → Stakeholders → Success criteria → Constraints → Scope
2. Draft structure following [PROJECT-STRUCTURE.md](PROJECT-STRUCTURE.md)
3. Review with user, iterate
4. Create via `linear:create_project`

**Creating status update:**
1. Get last update date/content from user
2. Fetch recent issues
3. Categorize: Completed / In Progress / Blocked
4. Generate update following [STATUS-UPDATES.md](STATUS-UPDATES.md)

## Reference Files

- **[PROJECT-STRUCTURE.md](PROJECT-STRUCTURE.md)**: Project description template and section guidelines
- **[STATUS-UPDATES.md](STATUS-UPDATES.md)**: Status update format and indicators

## MCP Limitations

1. Linear MCP does not expose milestones (read or write). All milestone info lives in project description. Users manually sync to Linear's native milestones.
2. Linear MCP does not expose native project updates (read or write). All update info and history lives in project description. Users manually sync to Linear's native project updates.