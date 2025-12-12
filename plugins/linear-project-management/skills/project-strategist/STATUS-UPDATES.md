# Status Update Format

## Template

```markdown
YYYY-MM-DD

**Summary**

[1-2 sentence high-level summary + next major goal]

## Focus

**[Category 1]**

* [Key accomplishment]
* [Key accomplishment]

**[Category 2]**

* [Key accomplishment]

## Progress from previous update + Next steps

1. **[Status] Goal** - Details
2. **[Status] Goal** - Details

## Active milestones

* **[X% of N]** Milestone name - brief status
* **[X% of N]** Milestone name - brief status
```

## Status Indicators

| Indicator | Meaning |
|-----------|---------|
| ✅ | Done - Goal fully achieved |
| 📈 | In Progress - Partial completion |
| 🎯 | Next Goal - Target for upcoming period |
| ⚠️ | At Risk - Needs attention |
| ❌ | Blocked - Cannot proceed |

## Example

```markdown
2025-12-11

**Summary**

Strong progress on system stability and B2B pricing. Next goal: moving Sauder and Meridian to new pricing engine.

## Focus

**System Stability & Reliability**

* Resolved critical issue where pricing engine silently stopped processing
* Implemented comprehensive exception handling and SQS logging

**B2B Pricing Improvements**

* Implemented B2B discounts: 2% for ≤$1000, 3% for >$1000
* Split min/max prices into B2B/B2C columns

## Progress from previous update + Next steps

1. 🎯 **Better understanding of missing notifications** - Partial progress. Implemented stale notification handling.
2. ✅ **Further optimizations to reduce price updates** - Out of stock listings no longer updated.
3. 📈 **Amazon API rate limits increase** - Received questionnaire, filling out.
4. 🎯 **Moving Sauder and Meridian** - Planning to complete before holidays.

## Active milestones

* **[96% of 39]** All remaining products (except Sauder & Meridian)
* **[8% of 3]** Sauder & Meridian migration
* **[54% of 6]** Cleanup and join wider team
```

## Workflow

1. Ask user for last update date and content (MCP can't access updates directly)
2. Fetch recent issues:
   ```
   mcp__linear__list_issues:
     project: "Project Name"
     updatedAt: "-P14D"
     limit: 100
   ```
3. Categorize: Completed / In Progress / New / Blocked
4. Draft update, get feedback, iterate
