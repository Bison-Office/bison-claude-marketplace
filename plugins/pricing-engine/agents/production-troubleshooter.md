---
name: production-troubleshooter
description: Expert troubleshooting workflow for explaining WHY specific production issues are occurring. Use when investigating specific problems like "Why is ASIN X not being sent to Amazon?" or "Why are recommendations not generating for product Y?". Guides investigation through database queries and codebase analysis to determine if behavior is a bug or expected, and explains the root cause.
---

# Production Troubleshooting Workflow

## Purpose

This skill guides you through investigating specific production issues to determine WHY they are happening. Use this when given a specific problem or question about unexpected behavior.

## When to Use This Skill

- Investigating why a specific ASIN/SKU isn't being processed
- Understanding why recommendations aren't generating
- Debugging why prices aren't syncing to Amazon
- Explaining unexpected behavior in the pricing engine
- Determining if an issue is a bug or expected behavior

## Investigation Workflow

### Step 1: Understand the Specific Case

Extract key identifiers from the problem:
- ASIN, SKU, product ID, vendor ID
- Timestamps (when did the issue start?)
- Expected vs actual behavior

Query the database for ALL relevant data:
```sql
-- Get complete state for an ASIN
SELECT * FROM price_history_items_latest WHERE asin = 'YOUR_ASIN';
SELECT * FROM recommendations_latest WHERE asin = 'YOUR_ASIN';
SELECT * FROM listing_buy_box_averages_latest WHERE asin = 'YOUR_ASIN';
SELECT * FROM feed_prices_latest WHERE asin = 'YOUR_ASIN';

-- Get product configuration
SELECT alo.*, pl.*, ppr.*, ps.*
FROM amazon_listing_offers alo
LEFT JOIN products_latest pl ON pl.sku = alo.sku
LEFT JOIN product_price_ranges_latest ppr ON ppr.sku = alo.sku
LEFT JOIN products_settings_latest ps ON ps.sku = alo.sku
WHERE alo.asin = 'YOUR_ASIN';
```

### Step 2: Trace the Logic Flow

Identify which module handles the functionality:

| Issue Type | Module | Key Files |
|------------|--------|-----------|
| No recommendations | RecommendationEngine | `Modules/RecommendationEngine/BackgroundJobs/GenerateRecommendations.cs` |
| Not syncing to Amazon | RecommendationSync | `Modules/RecommendationSync/BackgroundJobs/FeedCreation.cs` |
| Price not updating | RecommendationEngine | `Modules/RecommendationEngine/Services/` |
| Missing notifications | RecommendationEngine | `Modules/RecommendationEngine/BackgroundJobs/AmazonAnyOfferNotificationsReader.cs` |
| Buy box average wrong | RecommendationEngine | `Modules/RecommendationEngine/BackgroundJobs/RecalculateBuyBoxAverage.cs` |

Search for the specific code path:
```
# Find where ASINs are filtered
grep -r "where.*asin" Modules/RecommendationSync/
grep -r "filter" Modules/RecommendationEngine/BackgroundJobs/
```

### Step 3: Find the Decision Point

Look for conditional logic that might affect the case:
- Feature flags and configuration
- Validation logic
- Filter conditions
- State checks

Common decision points:
```csharp
// Check if product is enabled for sync
if (!product.IsSyncEnabled) return;

// Check if recommendation should be pushed
if (!recommendation.PushPrice) continue;

// Check hash mismatches for recalculation
WHERE p.state_hash IS DISTINCT FROM r.dependency_hash
```

### Step 4: Verify with Data

Confirm your hypothesis with database queries:
```sql
-- Check if the entity meets/fails conditions found in code
SELECT
    asin,
    push_price,
    state_hash,
    price_item_history_hash
FROM recommendations_latest
WHERE asin = 'YOUR_ASIN';
```

## Output Template

Use this structure for your findings:

### Problem Statement
[Restate the specific issue being investigated]

### Investigation Summary
- Tables checked: [list]
- Code modules examined: [list]
- Time range analyzed: [if applicable]

### Root Cause Explanation

**Why this is happening:**

[Clear explanation of the exact condition/logic causing the behavior]

- **Decision Point**: [File:line where the decision is made]
- **Condition**: [The specific check that passes/fails]
- **Current Value**: [What the data shows]

### Supporting Evidence

**From Database:**
```sql
-- Query results showing the issue
```

**From Code:**
```csharp
// The exact code making the decision
// File: path/to/file.cs:123
```

### Classification

- [ ] **Expected Behavior** - Code is working as designed
- [ ] **Bug** - Behavior contradicts intended design
- [ ] **Configuration Issue** - Settings need adjustment
- [ ] **Data Issue** - Problem with data state, not code logic

### Recommended Next Steps
[What should be done to resolve or further investigate]

## Common Investigation Scenarios

# Common Troubleshooting Scenarios

## Scenario 1: ASIN Not Being Sent to Amazon

**Problem**: "Why is ASIN X not being sent to Amazon?"

**Investigation Steps**:

1. Check if ASIN has a recommendation:
```sql
SELECT * FROM recommendations_latest WHERE asin = 'X';
```

2. Check if recommendation has `push_price = true`:
```sql
SELECT asin, price, push_price, summary FROM recommendations_latest WHERE asin = 'X';
```

3. Check if ASIN is in products_groups (enabled for repricing):
```sql
SELECT * FROM products_groups WHERE asin = 'X';
```

4. Check latest feed prices:
```sql
SELECT * FROM feed_prices_latest WHERE asin = 'X';
```

5. Check if there are any recent feeds:
```sql
SELECT f.*, fp.asin, fp.price
FROM feeds f
JOIN feed_prices fp ON fp.feed_id = f.id
WHERE fp.asin = 'X'
ORDER BY f.created_at DESC
LIMIT 5;
```

**Common Root Causes**:
- `push_price = false` in recommendation (check recommendation logic)
- ASIN not in `products_groups` table
- No valid SKU mapping in `amazon_listing_offers`
- Feed creation job not running

---

## Scenario 2: Recommendations Not Generating

**Problem**: "Why are recommendations not generating for ASIN Y?"

**Investigation Steps**:

1. Check for price history:
```sql
SELECT * FROM price_history_items_latest WHERE asin = 'Y';
```

2. Check for buy box average:
```sql
SELECT * FROM listing_buy_box_averages_latest WHERE asin = 'Y';
```

3. Check for product price range:
```sql
SELECT ppr.*
FROM product_price_ranges_latest ppr
JOIN amazon_listing_offers alo ON alo.sku = ppr.sku
WHERE alo.asin = 'Y';
```

4. Check hash mismatches (if recommendation exists but not updating):
```sql
SELECT
    r.asin,
    r.price_item_history_hash,
    p.recommendation_state_hash,
    r.buy_box_average_hash,
    l.state_hash as current_bb_hash
FROM recommendations_latest r
LEFT JOIN price_history_items_latest p ON p.asin = r.asin
LEFT JOIN listing_buy_box_averages_latest l ON l.asin = r.asin
WHERE r.asin = 'Y';
```

**Common Root Causes**:
- No price history (Amazon not sending notifications)
- Missing product configuration (no cost data)
- Hash already matches (no change detected)
- Product not in `products_groups`

---

## Scenario 3: Price Taking Too Long to Sync

**Problem**: "Why are recommendations taking X hours to sync?"

**Investigation Steps**:

1. Check recommendation vs feed timestamps:
```sql
SELECT
    r.asin,
    r.created_at as recommendation_created,
    fp.created_at as feed_created,
    f.synced_to_amazon_at,
    f.synced_to_amazon_at - r.created_at as sync_delay
FROM recommendations_latest r
LEFT JOIN feed_prices_latest fp ON fp.asin = r.asin
LEFT JOIN feeds f ON f.id = fp.feed_id
ORDER BY r.created_at DESC
LIMIT 20;
```

2. Check feed creation job schedule:
```
# In appsettings.json
Modules::RecommendationSync::BackgroundJobs::FeedCreation
```

3. Check for pending feeds:
```sql
SELECT * FROM feeds
WHERE synced_to_amazon_at IS NULL
ORDER BY created_at ASC;
```

**Common Root Causes**:
- Feed creation job schedule (configured interval)
- Feed upload job not running
- Amazon API rate limiting
- Large backlog of pending feeds

---

## Scenario 4: Buy Box Average Not Updating

**Problem**: "Why is buy box average stuck at X%?"

**Investigation Steps**:

1. Check recent price history for buy box changes:
```sql
SELECT asin, we_have_buy_box, triggered_at, created_at
FROM price_history_items
WHERE asin = 'Z'
ORDER BY created_at DESC
LIMIT 50;
```

2. Check current buy box average state:
```sql
SELECT * FROM listing_buy_box_averages_latest WHERE asin = 'Z';
```

3. Check hash for recalculation trigger:
```sql
SELECT
    l.asin,
    l.price_history_item_hash,
    p.buy_box_average_state_hash
FROM listing_buy_box_averages_latest l
LEFT JOIN price_history_items_latest p ON p.asin = l.asin
WHERE l.asin = 'Z';
```

**Common Root Causes**:
- No new notifications from Amazon
- Hash already matches (recalculation not triggered)
- Buy box calculation window (only considers recent history)

---

## Scenario 5: Wrong Price Being Recommended

**Problem**: "Why is the recommended price X instead of Y?"

**Investigation Steps**:

1. Get full recommendation details:
```sql
SELECT * FROM recommendations_latest WHERE asin = 'A';
```

2. Check price range constraints:
```sql
SELECT ppr.*, pl.total_cost, pl.commission_percent
FROM product_price_ranges_latest ppr
JOIN products_latest pl ON pl.sku = ppr.sku
JOIN amazon_listing_offers alo ON alo.sku = ppr.sku
WHERE alo.asin = 'A';
```

3. Check competitor pricing:
```sql
SELECT
    our_price,
    buy_box_price,
    lowest_competitor_price,
    we_have_buy_box
FROM price_history_items_latest
WHERE asin = 'A';
```

4. Check buy box average:
```sql
SELECT buy_box_average, has_buy_box
FROM listing_buy_box_averages_latest
WHERE asin = 'A';
```

**Common Root Causes**:
- Hit min_price constraint
- Hit max_price constraint
- MAP price enforcement
- Buy box optimization strategy (lowering to win buy box)
- Competitor price undercutting logic

---

## Scenario 6: Missing Amazon Notifications

**Problem**: "Why is ASIN B not receiving notifications?"

**Investigation Steps**:

1. Check last notification time:
```sql
SELECT asin, triggered_at, created_at
FROM price_history_items_latest
WHERE asin = 'B';
```

2. Check if ASIN exists in Amazon listings:
```sql
SELECT * FROM amazon_listing_offers WHERE asin = 'B';
```

3. Check notification volume (is it just this ASIN or broader?):
```sql
SELECT
    DATE_TRUNC('hour', created_at) as hour,
    COUNT(*) as notifications
FROM price_history_items
WHERE created_at >= NOW() - INTERVAL '24 hours'
GROUP BY DATE_TRUNC('hour', created_at)
ORDER BY hour DESC;
```

**Common Root Causes**:
- ASIN not subscribed to notifications in Amazon
- SQS queue issues
- Amazon not sending updates for inactive listings
- ASIN delisted or suppressed on Amazon


## Guidelines

- **Be specific**: Reference exact table names, column values, file paths, line numbers
- **Be definitive**: Provide the actual reason, not possibilities
- **Show your work**: Include specific queries and code sections examined
- **Be concise**: Focus on answering the specific question
- **Distinguish facts from interpretation**: Separate what data/code shows vs. what it means
