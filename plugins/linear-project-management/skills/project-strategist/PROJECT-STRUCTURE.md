# Project Description Structure

The project description contains all documentation in these sections (in order):

## 1. Problem

Why the project exists. Be specific, quantify impact.

```markdown
# Problem

The legacy pricing system used a "race to the bottom" strategy: always price below the lowest competitor. This approach has two flaws:

1. Lower price doesn't guarantee Buy Box win (Amazon's algorithm considers many factors)
2. Leaves money on the table when we could win Buy Box at higher prices
```

## 2. Solution (Optional)

High-level approach. Skip for exploratory projects.

```markdown
# Solution

This engine takes a smarter approach:

1. **Win the Buy Box** - Lower price incrementally until we win
2. **Maximize margins** - Test higher prices when holding Buy Box
3. **React in real-time** - Process notifications immediately
```

## 3. Scope

What's IN and explicitly what's OUT.

```markdown
## Scope

* **B2C & B2B pricing** - Supports both with separate engines
* **MAP price compliance** - Enforces Minimum Advertised Price
* **Cost integration** - Imports costs from legacy system
```

## 4. Active Milestones

Each milestone MUST have a Definition of Done.

```markdown
# Active milestones

### [Milestone Name]

[Optional description]

**Definition of Done:** [Specific, measurable, testable criteria]
```

**Good Definition of Done:**
- "Products prices correctly calculated and synced with Amazon"
- "No reason for anyone to open legacy for pricing questions"

**Bad Definition of Done:**
- "Feature is done" (not specific)
- "Works correctly" (not measurable)

## 5. Key Project People

Stakeholders with roles and milestone ownership.

```markdown
# Key project people

* **Inga** - key person overseeing project at the high level
* **Karolina** - generating ideas and documenting required information

**Milestone level responsibilities**

* **Justas** - Migrated all products to new pricing engine
* **Mantas** - turn off legacy amazon price calculation
```

## 6. Project Updates

Historical status updates. **Append new at top, never modify old.**

See [STATUS-UPDATES.md](STATUS-UPDATES.md) for format.

---

## Quick Reference

```
linear:get_project:
  query: "Project Name"

linear:update_project:
  id: "[project-id]"
  description: "[full markdown]"

linear:create_project:
  name: "Project Name"
  team: "Team Name"
  description: "[full markdown]"
```
