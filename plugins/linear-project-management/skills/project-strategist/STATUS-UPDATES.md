# Status Update Format

Updates should be **short and shareable** - suitable for use outside Linear (Slack, email, stakeholder updates).

## Template

```markdown
YYYY-MM-DD

**Summary**

[1-2 sentence high-level summary + next major goal]

## What we accomplished

* [Key accomplishment - no issue numbers]
* [Key accomplishment]

## Next steps

1. [Status] **Goal** - Brief detail
2. [Status] **Goal** - Brief detail

## Current milestone

**[~X%]** Milestone name - one line status
```

## Guidelines

- **No issue/story numbers** - Updates should be readable without Linear context
- **Keep it concise** - 5-10 bullet points max across all sections
- **Focus on outcomes** - What was achieved, not what was worked on
- **Shareable** - Written for stakeholders, not just the dev team

## Status Indicators

| Indicator | Meaning |
|-----------|---------|
| ✅ | Done |
| 📈 | In Progress |
| 🎯 | Next Goal |
| ⚠️ | At Risk |
| ❌ | Blocked |

## Example

```markdown
2025-12-11

**Summary**

Strong progress on system stability and B2B pricing. Next goal: moving Sauder and Meridian to new pricing engine.

## What we accomplished

* Resolved critical issue where pricing engine silently stopped processing
* Implemented B2B discounts: 2% for ≤$1000, 3% for >$1000
* Out of stock listings no longer trigger unnecessary price updates

## Next steps

1. 📈 **Amazon API rate limits** - Filling out questionnaire
2. 🎯 **Sauder & Meridian migration** - Target before holidays

## Current milestone

**[~90%]** Product migration - Sauder & Meridian remaining
```

## Workflow

1. Ask user for last update date and content (MCP can't access updates directly)
2. Fetch recent issues to understand accomplishments
3. Summarize outcomes (not tasks) without issue numbers
4. Draft update, get feedback, iterate
