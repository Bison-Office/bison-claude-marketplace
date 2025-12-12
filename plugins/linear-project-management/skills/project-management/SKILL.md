---
name: project-management
description: Guidelines for structuring Linear projects with proper overview documentation and milestone management. Defines standards for project descriptions, scope definitions, stakeholder documentation, milestone tracking, and project updates.
---

# Project Management Guidelines

## Purpose

This skill defines standards for creating and maintaining well-structured projects in Linear. Everything is managed in the **project description** since Linear MCP doesn't expose milestones directly. Users manually sync milestones to Linear's native milestone feature for roadmap visualization.

## When to Use This Skill

- Creating a new project in Linear
- Restructuring an existing project's documentation
- Adding or updating milestones
- Writing project status updates
- Reviewing project documentation completeness
- Onboarding team members to understand project context

## Project Description Structure

The project description contains all project documentation in these sections (in order):

1. **Problem** - Why the project exists
2. **Solution** - High-level approach (optional)
3. **Scope** - What's in/out of scope
4. **Active Milestones** - Current milestones with Definition of Done
5. **Key Project People** - Stakeholders and responsibilities
6. **Project Updates** - Historical status updates (append-only)

---

## 1. Problem

Explain **why** this project exists. What problem are we solving? What goal are we trying to achieve?

**Guidelines:**
- Be specific about the pain points or opportunities
- Quantify the impact where possible
- Explain what happens if we don't solve this

**Example:**
```markdown
# Problem

The legacy pricing system used a "race to the bottom" strategy: always price below the lowest competitor. This approach has two flaws:

1. Lower price doesn't guarantee Buy Box win (Amazon's algorithm considers many factors)
2. Leaves money on the table when we could win Buy Box at higher prices
```

---

## 2. Solution (Optional)

If there's a specific solution in mind, document the high-level approach. Skip for exploratory projects.

**Guidelines:**
- Keep it high-level (implementation details belong in issues)
- Explain the "how" at a conceptual level
- List key principles or strategies

**Example:**
```markdown
# Solution

This engine takes a smarter approach:

1. **Win the Buy Box** - Lower price incrementally until we win, not just until we're cheapest
2. **Maximize margins** - When we've held the Buy Box for a sustained period, test higher prices to find the maximum price that still retains Buy Box
3. **React in real-time** - Process Amazon price notifications to respond to competitor changes immediately
```

---

## 3. Scope

Define what this project **does** and **does not** include.

**Guidelines:**
- Use bullet points for clarity
- Explicitly state what's OUT of scope when relevant
- Include integration points with other systems

**Example:**
```markdown
## Scope

* **B2C & B2B pricing** - Supports both consumer and business customer pricing with separate recommendation engines
* **MAP price compliance** - Enforces Minimum Advertised Price constraints from suppliers
* **Cost integration** - Imports product costs from the legacy system (which acts as the "cost engine") to calculate margins and price floors
```

---

## 4. Active Milestones

List current milestones with their Definition of Done. Each milestone is a heading with optional description and **required** Definition of Done.

### Milestone Structure

```markdown
# Active milestones

### [Milestone Name]

[Optional description explaining the scope and context]

**Definition of Done:** [Clear, measurable completion criteria]
```

### Guidelines

| Element | Required | Description |
|---------|----------|-------------|
| Name | Yes | Clear heading describing the milestone |
| Description | No | Context to understand scope (recommended for complex milestones) |
| Definition of Done | **Yes** | Specific, measurable criteria for completion |

### Definition of Done Guidelines

- Must be specific and measurable
- Must be testable/verifiable
- Written as clear acceptance criteria

**Good Examples:**
```markdown
### All remaining products (except Sauder and Meridian)

All products are optimally being managed via new pricing engine except Sauder and Meridian - which has their own custom settings that we still need to implement.

**Definition of Done:** Products prices are correctly calculated and kept in sync with what is in amazon

### Turn off legacy - full ownership to new pricing engine

**Definition of Done:** Goal is that there no reason for legacy to keep calculating amazon prices and no one has any reason to open legacy for pricing related questions. Includes integrating PowerBI to new postgres database instead of using legacy database
```

**Bad Definition of Done Examples:**
- "Feature is done" (not specific)
- "Works correctly" (not measurable)
- "Stakeholders are happy" (not testable)

### Syncing with Linear Milestones

Since milestones live in the description, **manually create matching milestones** in Linear's native milestone feature for:
- Roadmap visualization
- Timeline tracking
- Issue grouping

---

## 5. Key Project People

Document stakeholders and their responsibilities.

**Guidelines:**
- Include role/responsibility for each person
- Cover both business and technical stakeholders
- Include milestone-level ownership

**Example:**
```markdown
# Key project people

* **Inga** - key person overseeing project at the high level
* **Karolina** - generating ideas and documenting required information
* **Karolis** - pricing engine correctness, quality - testing
* **Giedrius** - sweet spot for margin and sales

**Milestone level responsibilities**

* **Justas** - Migrated all products to new pricing engine
* **Mantas Valavičius** - turn off legacy amazon price calculation
* **Giedrius** - Pricing solution for product with no competition
* **Vilius** - UI and Automated buy box suppressions statistics in supplier portal
```

---

## 6. Project Updates

Historical record of project status. **Append new updates at the top, never modify historical updates.**

### Update Structure

```markdown
# Project updates

YYYY-MM-DD

**Summary**

[1-2 sentence high-level summary]

## Focus

**[Category 1]**
* [Key accomplishment]
* [Key accomplishment]

**[Category 2]**
* [Key accomplishment]

## Progress from previous update + Next steps

1. **[Status] Item** - Details
2. **[Status] Item** - Details

## Active milestones

* **[X% of N]** Milestone name - brief status
* **[X% of N]** Milestone name - brief status

---

[Previous updates below, unmodified]
```

### Status Indicators

Use these emoji indicators for progress items:

| Indicator | Meaning | Usage |
|-----------|---------|-------|
| ✅ | Done | Goal fully achieved |
| 📈 | In Progress | Work started, partial completion |
| 🎯 | Next Goal | Target for upcoming period |
| ⚠️ | At Risk | Needs attention |
| ❌ | Blocked | Cannot proceed |

### Update Content Guidelines

1. **Summary** - Brief overview of the update period
2. **Focus** - Categorized accomplishments (group by theme)
3. **Progress + Next Steps**:
   - Status on items from previous update
   - Brief next steps (milestones show priority, keep this concise)
   - Side tracks / additional work outside milestones
4. **Active Milestones** - Current progress with percentages

### Example Update

```markdown
2025-12-11

**Summary**

Strong progress on system stability and B2B pricing features improvements. Next goal moving Sauder and Meridian to new pricing engine.

## Focus

**System Stability & Reliability**

* Resolved critical issue where pricing engine silently stopped processing notifications
* Implemented comprehensive exception handling, lock timeouts, SQS logging

**B2B Pricing Improvements**

* Implemented B2B discounts: 2% for prices ≤$1000, 3% for >$1000
* Split min/max prices into B2B/B2C columns for proper margin handling

## Progress from previous update + Next steps

1. 🎯 **Better understanding of missing notifications** - Partial progress. Implemented stale notification handling.
2. ✅ **Further optimizations to reduce price updates** - Out of stock listings are not being updated anymore.
3. 📈 **Amazon API rate limits increase** - Received questionnaire, filling out.
4. 🎯 **Moving Sauder and Meridian** - Planning to complete before holidays.

## Active milestones

* **[96% of 39]** All remaining products (except Sauder & Meridian)
* **[8% of 3]** Sauder & Meridian migration
* **[54% of 6]** Cleanup and join wider team
```

---

## Project Documentation Checklist

### Initial Setup
- [ ] Problem section explains WHY the project exists
- [ ] Solution section (if applicable) explains the approach
- [ ] Scope section defines what's IN and OUT
- [ ] Active milestones listed with Definition of Done for each
- [ ] Key project people documented with responsibilities
- [ ] Milestone ownership assigned

### Ongoing Maintenance
- [ ] New updates appended at top of Project Updates
- [ ] Historical updates never modified
- [ ] Milestones updated as scope changes
- [ ] Linear native milestones synced manually

---

## Quick Reference

### Get Project Details
```
mcp__linear__get_project:
  query: "Project Name"
```

### Update Project Description
```
mcp__linear__update_project:
  id: "[project-id]"
  description: "[full markdown description]"
```

### Create Issue in Project
```
mcp__linear__create_issue:
  title: "Issue title"
  team: "Team Name"
  project: "Project Name"
  description: "..."
```

---

## Limitations

**Linear MCP does not expose:**
- Project milestones (read or write)
- Milestone progress
- Assigning issues to milestones

**Workaround:** All milestone information lives in the project description. Manually sync to Linear's native milestones for roadmap visualization.

---

## Reference Project

Use **Pricing Engine 2.0** as the reference implementation:
- [View Project](https://linear.app/bisonoffice/project/pricing-engine-20-e5321c81e6bd)
