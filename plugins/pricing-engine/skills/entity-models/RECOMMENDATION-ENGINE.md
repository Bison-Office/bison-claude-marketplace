# RecommendationEngine Module Entities

## PriceHistoryItem

**Tables**: `price_history_items` (big), `price_history_items_latest` (snapshot), `_b2b` variants

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Asin | string | Amazon product ID |
| TriggeredAt | DateTimeOffset | Amazon notification time |
| OurPrice | decimal? | Current seller price |
| CompetitivePriceThreshold | decimal? | B2C only |
| LowestCompetitorPrice | decimal? | Lowest competitor price |
| LowestCompetitorName | string? | Competitor identifier |
| BuyBoxPrice | decimal? | Current buy box price |
| WeHaveMultipleOffers | bool | Multi-offer flag |
| WeHaveBuyBox | bool | Buy box ownership |
| BuyBoxWinner | string? | Current buy box owner |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(asin, created_at DESC)` - Time-range queries
- `(created_at)` - Cleanup old records

**State Hashes** (latest only):
- `RecommendationStateHash`: MD5(our_price, buy_box_price, we_have_buy_box, lowest_competitor_price, competitive_price_threshold)
- `BuyBoxAverageStateHash`: MD5(we_have_buy_box)

---

## ListingBuyBoxAverage

**Tables**: `listing_buy_box_averages` (big), `listing_buy_box_averages_latest` (snapshot), `_b2b` variants

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Asin | string | Amazon product ID |
| BuyBoxChanges | List<BuyBoxChange> | Serialized JSONB |
| BuyBoxAverage | double | Percentage 0.0-1.0 |
| HasBuyBox | bool | Current state |
| PriceHistoryItemHash | string? | Last processed hash |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(asin, id DESC)` - Cursor processing
- `(created_at)` - Cleanup
- `(buy_box_average, has_buy_box, updated_at)` (latest only)
- `(asin, price_history_item_hash)` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(buy_box_average >= 0.9)

---

## Recommendation

**Tables**: `recommendations` (big), `recommendations_latest` (snapshot), `_b2b` variants

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Asin | string | Amazon product ID |
| Price | decimal | Recommended price |
| PushPrice | bool | Whether to push to Amazon |
| CompetingType | enum | Competition strategy |
| Summary | string | Explanation |
| MapPrice | decimal? | MAP constraint |
| PriceItemHistoryHash | string? | Price history dependency |
| BuyBoxAverageHash | string? | Buy box dependency |
| PriceRangeHash | string? | Price range dependency |
| B2cRecommendationHash | string? | B2B only, refs B2C |
| Data | JsonDocument? | Additional context JSONB |
| CreatedAt | DateTime | Server timestamp |

**Indexes**:
- `(asin, created_at DESC)` - Time-range queries
- `(created_at)` - Cleanup
- `(asin) UNIQUE` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(price, map_price)

---

## ProductsGroup

**Table**: `products_groups`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| Asin | string | Amazon product ID |
| GroupId | int? | FK to ProductGroup |
| Group | ProductGroup? | Navigation property |

**Indexes**:
- `(asin)`

---

## BulkDiscountTier

**Table**: `bulk_discount_tiers`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| Configurations | List | JSONB tier definitions |
| StateHash | string | MD5(configurations) |
| UpdatedAt | DateTimeOffset | Last update |

**Indexes**:
- `(state_hash)`
