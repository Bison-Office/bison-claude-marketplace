# Multiple Buy Box Winners

## Background

Amazon can award the buy box to multiple sellers simultaneously. This was discovered when API responses showed two offers with `IsBuyBoxWinner = true` for the same ASIN.

**Linear Issue:** BIS-3865

## Amazon's Official Response (2025-12-09)

> One of the two buy box winners is eligible for prime, but is not prime at the moment. The API and notification does not give information on which offer is eligible for prime, it only gives the current status. Also, one offer will have one landing price which will be equal to the product price plus shipping (and points in JP). Buybox is not dependent on only the landing price, but on several other factors, the details of which are not made available to anyone outside the team that has created the BuyBox algorithm. Also, since the AOCN is also returning the same information, we can conclude that the API is working as designed.

## Key Insights

1. **Two simultaneous buy box winners are valid** - One Prime-eligible, one not
2. **API shows current Prime status only** - Not Prime eligibility
3. **Buy box algorithm is proprietary** - Not just based on landed price
4. **Both API and AOCN notifications behave the same** - This is by design

## Example Scenario

```
ASIN: B00KKS1ZG0

Offer 1 (Seller A): IsBuyBoxWinner = true, IsPrime = false  (holds non-Prime buy box)
Offer 2 (Seller B): IsBuyBoxWinner = true, IsPrime = false  (Prime-eligible but not currently Prime)
Offer 3 (Seller C): IsBuyBoxWinner = false
```

Both Seller A and Seller B can simultaneously have `IsBuyBoxWinner = true` because:
- Seller A wins the buy box for non-Prime customers
- Seller B is Prime-eligible and wins for Prime customers (even if currently showing `IsPrime = false`)

## How Our Algorithm Handles This

**Our buy box detection:**
```csharp
var weHaveBuyBox = sortedOffersByPrice
    .FirstOrDefault(offer => offer.SellerId == BisonSellerId && offer.IsBuyBoxWinner) is not null;
```

**Why this works correctly:**
- We only check if **our offer** has `IsBuyBoxWinner = true`
- We don't check if we're the *only* winner
- If both we and a competitor have the buy box, `weHaveBuyBox = true` (correct)
- The optimization algorithm continues normally

**The price oscillation is intentional:**
- When we have buy box at 90%+ → increase price 1% (test ceiling)
- If we lose buy box → decrease price (win it back)
- This cycle finds the optimal price while retaining our buy box

## Data Not Currently Parsed

The following fields exist in Amazon notifications but are commented out in our code:

```csharp
// AmazonAnyOfferChangeNotification.cs:203-204
//[JsonPropertyName("PrimeInformation")]
//public PrimeInformation PrimeInformation { get; set; }

//[JsonPropertyName("IsFulfilledByAmazon")]
//public bool IsFulfilledByAmazon { get; set; }
```

These could be parsed in the future if we need to distinguish between Prime and non-Prime competition, but are not required for the current algorithm to function correctly.

## Conclusion

**No code changes required.** The algorithm correctly handles multiple buy box winners because we track our own offer's buy box status independently of other sellers.
