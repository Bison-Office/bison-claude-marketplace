# Products Module Entities

## Product

**Tables**: `products` (big), `products_latest` (snapshot)

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Sku | string | Seller SKU |
| VendorId | string | Vendor ID |
| BaseCost | decimal | Base cost |
| AdjustmentsPercent | decimal? | Cost adjustment % |
| HandlingFee | decimal? | Additional fee |
| ShippingPrice | decimal? | Shipping cost |
| CommissionPercent | decimal? | Commission % |
| MapPrice | decimal? | Minimum advertised price |
| TotalCost | decimal | Computed field |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(sku, id DESC)` - Cursor processing
- `(created_at)` - Cleanup
- `(updated_at, sku, state_hash)` (latest only)
- `(sku) UNIQUE` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(total_cost, commission_percent, map_price)

---

## ProductPriceRange

**Tables**: `product_price_ranges` (big), `product_price_ranges_latest` (snapshot)

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Sku | string | Seller SKU |
| ProductCostsHash | string? | Product hash dependency |
| ProductSettingsHash | string? | Settings hash dependency |
| VendorSettingsHash | string? | Vendor hash dependency |
| TotalCost | decimal | Product total cost |
| Commissions | decimal? | Commission amount |
| MinPrice | decimal | Minimum allowed price |
| MaxPrice | decimal? | Maximum allowed price |
| PriceInCartThreshold | decimal? | Cart display threshold |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(sku)` - SKU lookup
- `(created_at)` - Cleanup
- `(updated_at, sku, state_hash)` (latest only)
- `(sku) UNIQUE` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(min_price, max_price, price_in_cart_threshold)

---

## ProductSettings

**Tables**: `products_settings` (big), `products_settings_latest` (snapshot)

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| Sku | string | Seller SKU |
| MinMargin | decimal? | Minimum profit margin |
| FixedPrice | decimal? | Override fixed price |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(sku)` - SKU lookup
- `(created_at)` - Cleanup
- `(updated_at, sku, state_hash)` (latest only)
- `(sku) UNIQUE` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(min_margin, fixed_price)

---

## VendorSettings

**Tables**: `vendors_settings` (big), `vendors_settings_latest` (snapshot)

| Property | Type | Notes |
|----------|------|-------|
| Id | long | PK, auto-increment |
| VendorId | string | Vendor ID |
| MapBehavior | enum | Ignore or Respect (default) |
| CreatedAt | DateTimeOffset | Server timestamp |

**Indexes**:
- `(vendor_id)` - Vendor lookup
- `(created_at)` - Cleanup
- `(updated_at, vendor_id, state_hash)` (latest only)
- `(vendor_id) UNIQUE` (latest only)

**State Hash** (latest only):
- `StateHash`: MD5(map_behavior)

---

## Vendor

**Table**: `vendors`

| Property | Type | Notes |
|----------|------|-------|
| Id | string | PK, caller-provided |
| Name | string | Display name |

---

## AmazonListingOffer

**Table**: `amazon_listing_offers`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| Sku | string | Seller SKU |
| Asin | string | Amazon product ID |
| B2cPrice | decimal | B2C offer price |
| B2bPrice | decimal? | B2B offer price |

**Indexes**:
- `(asin) INCLUDE (sku)` - ASIN to SKU lookup
- `(sku) INCLUDE (asin)` - SKU to ASIN lookup

---

## ProductPerformance

**Table**: `product_performances`

| Property | Type | Notes |
|----------|------|-------|
| Id | int | PK, auto-increment |
| ParentAsin | string | Parent ASIN for variants |
| Asin | string | Amazon product ID |
| SessionsTotal | int | Total sessions |
| SessionPercentageTotal | decimal | % of category sessions |
| PageViewsTotal | int | Total page views |
| PageViewsPercentageTotal | decimal | % of category views |
| BuyBoxPercentage | decimal | Buy box % |
| UnitsOrdered | int | Units ordered |
| UnitSessionPercentage | decimal | Units per session |
| OrderedProductSales | decimal | Revenue |
| TotalOrderItems | int | Total items ordered |
| ReportDate | DateOnly | Date of report |
| CreatedAt | DateTime | Import timestamp |
| UpdatedAt | DateTime | Update timestamp |

**Indexes**:
- `(report_date DESC, asin ASC, sessions_total DESC)` - Performance queries
