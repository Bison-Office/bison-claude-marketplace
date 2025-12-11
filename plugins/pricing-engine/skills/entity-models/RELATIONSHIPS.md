# Entity Relationships

## Relationship Diagram

```
ProductGroup (1) <--> (0..n) ProductsGroup <--> (1) Product (via ASIN)
Vendor (1) <--> (0..n) Product (via VendorId)
Vendor (1) <--> (0..1) VendorSettings (via VendorId)
Product (SKU) <--> (0..1) ProductSettings (via SKU)
Product (SKU) <--> (0..1) ProductPriceRange (via SKU)
Recommendation (1) <--> (0..n) FeedPrice (via RecommendationId)
Feed (1) <--> (0..n) FeedPrice (via FeedId)
Feed (1) <--> (0..n) AmazonFeed (via FeedId)
AmazonFeed (AmazonFeedId) <--> (0..n) AmazonFeedResult (via AmazonFeedId)
```

## ASIN-SKU Relationship

One ASIN can have multiple SKUs (different sellers or variations):

```sql
-- Find all SKUs for an ASIN
SELECT sku FROM amazon_listing_offers WHERE asin = 'B001234567';

-- Find ASIN for a SKU
SELECT asin FROM amazon_listing_offers WHERE sku = 'MY-SKU-001';
```

## Product Configuration Chain

Product pricing is determined by multiple related entities:

```
Product (costs) + ProductSettings (margins) + VendorSettings (MAP behavior)
    ↓
ProductPriceRange (min/max prices)
    ↓
Recommendation (calculated price)
    ↓
FeedPrice (sent to Amazon)
```

## Feed Processing Chain

```
Recommendation → FeedPrice → Feed → AmazonFeed → AmazonFeedResult
```

## B2B Table Mapping

| B2C Table | B2B Equivalent |
|-----------|----------------|
| price_history_items | price_history_items_b2b |
| price_history_items_latest | price_history_items_b2b_latest |
| listing_buy_box_averages | listing_buy_box_averages_b2b |
| listing_buy_box_averages_latest | listing_buy_box_averages_b2b_latest |
| recommendations | recommendations_b2b |
| recommendations_latest | recommendation_b2b_latest |

**Note**: `amazon_listing_offers` has both `B2cPrice` and `B2bPrice` columns instead of separate tables.

## State Hash Dependencies

### Recommendation Depends On:

```
PriceHistoryItemLatest.RecommendationStateHash
    → RecommendationLatest.PriceItemHistoryHash

ListingBuyBoxAverageLatest.StateHash
    → RecommendationLatest.BuyBoxAverageHash

ProductPriceRangeLatest.StateHash
    → RecommendationLatest.PriceRangeHash
```

### ProductPriceRange Depends On:

```
ProductLatest.StateHash
    → ProductPriceRangeLatest.ProductCostsHash

ProductSettingsLatest.StateHash
    → ProductPriceRangeLatest.ProductSettingsHash

VendorSettingsLatest.StateHash
    → ProductPriceRangeLatest.VendorSettingsHash
```

### ListingBuyBoxAverage Depends On:

```
PriceHistoryItemLatest.BuyBoxAverageStateHash
    → ListingBuyBoxAverageLatest.PriceHistoryItemHash
```

## RecommendationSync Module Entities

### Feed

**Table**: `feeds`

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| AsinCount | int | Number of ASINs |
| SyncedToAmazonAt | DateTimeOffset? | Amazon upload time |
| SyncedToLegacyPricingEngineAt | DateTimeOffset? | Legacy sync time |
| CreatedAt | DateTimeOffset | Feed creation time |

**Indexes**:
- `(synced_to_amazon_at)` - Find unsynced
- `(synced_to_legacy_pricing_engine_at)` - Find unsynced to legacy

### FeedPrice

**Tables**: `feed_prices` (big), `feed_prices_latest` (snapshot)

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Asin | string | Amazon product ID |
| Sku | string | Seller SKU |
| Price | decimal | Price in feed |
| RecommendationId | long | FK to recommendation |
| MapPrice | decimal? | MAP constraint |
| FeedId | long | FK to feed batch |
| RecommendationHash | string? | Recommendation state |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(feed_id)` - Get prices in feed
- `(recommendation_id)` - Track recommendation
- `(updated_at, asin)` (latest only)
- `(sku) UNIQUE` (latest only)

### AmazonFeed

**Table**: `amazon_feeds`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| FeedId | long | FK to feed |
| AmazonFeedId | string | Amazon-assigned ID |
| CreatedAt | DateTimeOffset | Submission time |
| IsFinished | bool | Processing complete |

**Indexes**:
- `(amazon_feed_id)` - Amazon ID lookup
- `(feed_id)` - Feed lookup
- `(is_finished)` - Find pending

### AmazonFeedResult

**Table**: `amazon_feed_results`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| AmazonFeedId | string | FK to Amazon feed |
| Content | string | JSONB result content |
| ProcessingStatus | string | SUBMITTED, IN_PROGRESS, CANCELLED, DONE |
| ResultFeedDocumentId | string | Amazon document ID |
| CreateAt | DateTimeOffset | Creation timestamp |

**Indexes**:
- `(amazon_feed_id)` - Feed lookup
- `(processing_status)` - Status filtering
