---
name: pricing-algorithm
description: Reference for the buy box optimization pricing algorithm. Use when understanding pricing decisions, debugging price changes, or investigating buy box behavior. Covers the core optimization loop, buy box detection, and edge cases like multiple buy box winners.
---

# Pricing Algorithm Reference

## Overview

The pricing engine uses a **buy box optimization strategy** that continuously adjusts prices to find the optimal ceiling while retaining the buy box. The algorithm oscillates prices to test boundaries.

## Core Algorithm

**Goal:** Maximize price while maintaining buy box ownership.

**Logic Flow:**
1. **Have buy box + 90%+ ownership over 4 hours** → Increase price by 1% (test if ceiling can be higher)
2. **Have buy box + <90% ownership** → Hold current price (stabilize)
3. **Lost buy box** → Decrease price to match competitor or drop 1%
4. **At minimum price without buy box** → Cannot compete, hold position

**Key Files:**
- `BuyBoxOptimizationPricingStrategy.cs` - Core pricing logic
- `AmazonAnyOfferNotificationsReader.cs` - Buy box detection from notifications
- `RecommendationContextCollector.cs` - Context gathering for pricing decisions

## Buy Box Detection

We detect buy box ownership by checking if **our specific offer** has `IsBuyBoxWinner = true`:

```csharp
var weHaveBuyBox = sortedOffersByPrice
    .FirstOrDefault(offer => offer.SellerId == BisonSellerId && offer.IsBuyBoxWinner) is not null;
```

## Edge Cases & Clarifications

See [MULTIPLE-BUYBOX-WINNERS.md](MULTIPLE-BUYBOX-WINNERS.md) for details on Amazon's multiple buy box winner behavior.

## Price Change Triggers

| Condition | Action | Reason |
|-----------|--------|--------|
| `WeHaveBuyBox && BuyBoxPct >= 90%` | +1% | Test higher ceiling |
| `WeHaveBuyBox && BuyBoxPct < 90%` | Hold | Stabilize ownership |
| `!WeHaveBuyBox && Price > Min` | -1% or match competitor | Win buy box back |
| `!WeHaveBuyBox && Price == Min` | Hold | Cannot compete further |
