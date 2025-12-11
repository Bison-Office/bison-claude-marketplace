---
name: entity-models
description: Complete reference for all entity properties and relationships in the pricing engine. Use when implementing queries, creating migrations, understanding data models, or working with RecommendationEngine, Products, and RecommendationSync modules. Includes property types, indexes, state hashes, and B2B variants.
---

# Entity Models Reference

## Overview

This skill provides detailed property documentation for all major entities in the pricing engine database.

## When to Use This Skill

- Implementing database queries
- Creating EF Core migrations
- Understanding entity relationships
- Working with state hash properties
- Mapping B2C to B2B variants

## Quick Entity Lookup

### RecommendationEngine Module

| Entity | Tables | Key Properties |
|--------|--------|----------------|
| PriceHistoryItem | `price_history_items`, `*_latest`, `*_b2b` | Asin, OurPrice, BuyBoxPrice, WeHaveBuyBox |
| ListingBuyBoxAverage | `listing_buy_box_averages`, `*_latest`, `*_b2b` | Asin, BuyBoxAverage, HasBuyBox |
| Recommendation | `recommendations`, `*_latest`, `*_b2b` | Asin, Price, PushPrice, MapPrice |

### Products Module

| Entity | Tables | Key Properties |
|--------|--------|----------------|
| Product | `products`, `*_latest` | Sku, VendorId, BaseCost, TotalCost |
| ProductPriceRange | `product_price_ranges`, `*_latest` | Sku, MinPrice, MaxPrice |
| ProductSettings | `products_settings`, `*_latest` | Sku, MinMargin, FixedPrice |
| VendorSettings | `vendors_settings`, `*_latest` | VendorId, MapBehavior |

### RecommendationSync Module

| Entity | Tables | Key Properties |
|--------|--------|----------------|
| Feed | `feeds` | AsinCount, SyncedToAmazonAt |
| FeedPrice | `feed_prices`, `*_latest` | Asin, Sku, Price, FeedId |
| AmazonFeed | `amazon_feeds` | FeedId, AmazonFeedId, IsFinished |

## Detailed References

- [RECOMMENDATION-ENGINE.md](RECOMMENDATION-ENGINE.md) - Full entity details for RecommendationEngine module
- [PRODUCTS.md](PRODUCTS.md) - Full entity details for Products module
- [RELATIONSHIPS.md](RELATIONSHIPS.md) - Entity relationships and B2B mapping

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

## State Hash Patterns

Each `*_latest` table has state hashes for change detection:

| Entity | State Hash Contents |
|--------|---------------------|
| PriceHistoryItemLatest | MD5(our_price, buy_box_price, we_have_buy_box, lowest_competitor_price) |
| RecommendationLatest | MD5(price, map_price) |
| ProductLatest | MD5(total_cost, commission_percent, map_price) |
| ProductPriceRangeLatest | MD5(min_price, max_price, price_in_cart_threshold) |
