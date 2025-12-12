<case_id>19014537831</case_id>

<question>
For the asin B00KKS1ZG0 calling [https://sellingpartnerapi-na.amazon.com/products/pricing/v0/items/{Asin}/offers?ItemCondition=New](https://sellingpartnerapi-na.amazon.com/products/pricing/v0/items/%7BAsin%7D/offers?ItemCondition=New) or receiving ANY_OFFER_ChANGED we can see 2 offers with IsBuyBoxWinner = true and both are not eligible for Prime

Just trying understand, if we're interpreting a response wrongly or is this a bug in the api side and there's other ways to determine if we have buy box for suer? Maybe we can we use BuyBoxPrices array's property LandedPrice to be true buybox winning offer price? Does it ever return multiple LandedPrice objects?

```json
{
  "payload": {
    "ASIN": "B00KKS1ZG0",
    "status": "Success",
    "ItemCondition": "New",
    "Identifier": {
      "MarketplaceId": "ATVPDKIKX0DER",
      "ItemCondition": "New",
      "ASIN": "B00KKS1ZG0"
    },
    "Summary": {
      "LowestPrices": [
        {
          "condition": "new",
          "fulfillmentChannel": "Merchant",
          "LandedPrice": {
            "CurrencyCode": "USD",
            "Amount": 178.95
          },
          "ListingPrice": {
            "CurrencyCode": "USD",
            "Amount": 149
          },
          "Shipping": {
            "CurrencyCode": "USD",
            "Amount": 29.95
          }
        }
      ],
      "BuyBoxPrices": [
        {
          "condition": "New",
          "LandedPrice": {
            "CurrencyCode": "USD",
            "Amount": 178.95
          },
          "ListingPrice": {
            "CurrencyCode": "USD",
            "Amount": 149
          },
          "Shipping": {
            "CurrencyCode": "USD",
            "Amount": 29.95
          }
        }
      ],
      "NumberOfOffers": [
        {
          "condition": "new",
          "fulfillmentChannel": "Merchant",
          "OfferCount": 3
        }
      ],
      "BuyBoxEligibleOffers": [
        {
          "condition": "new",
          "fulfillmentChannel": "Merchant",
          "OfferCount": 3
        }
      ],
      "SalesRankings": [
        {
          "ProductCategoryId": "home_garden_display_on_website",
          "Rank": 6358073
        },
        {
          "ProductCategoryId": "3733851",
          "Rank": 14357
        }
      ],
      "TotalOfferCount": 3
    },
    "Offers": [
      {
        "Shipping": {
          "CurrencyCode": "USD",
          "Amount": 29.95
        },
        "ListingPrice": {
          "CurrencyCode": "USD",
          "Amount": 149
        },
        "ShippingTime": {
          "maximumHours": 240,
          "minimumHours": 216,
          "availabilityType": "NOW"
        },
        "SellerFeedbackRating": {
          "FeedbackCount": 816,
          "SellerPositiveFeedbackRating": 64
        },
        "ShipsFrom": {
          "State": "MI",
          "Country": "US"
        },
        "PrimeInformation": {
          "IsPrime": false,
          "IsNationalPrime": false
        },
        "SubCondition": "new",
        "SellerId": "A2P7DKVUCAEOC0",
        "IsFeaturedMerchant": true,
        "IsBuyBoxWinner": true,
        "IsFulfilledByAmazon": false
      },
      {
        "Shipping": {
          "CurrencyCode": "USD",
          "Amount": 0
        },
        "ListingPrice": {
          "CurrencyCode": "USD",
          "Amount": 194.09
        },
        "ShippingTime": {
          "maximumHours": 144,
          "minimumHours": 120,
          "availabilityType": "NOW"
        },
        "SellerFeedbackRating": {
          "FeedbackCount": 67953,
          "SellerPositiveFeedbackRating": 80
        },
        "ShipsFrom": {
          "State": "IL",
          "Country": "US"
        },
        "PrimeInformation": {
          "IsPrime": false,
          "IsNationalPrime": false
        },
        "SubCondition": "new",
        "SellerId": "A2G88111572J8M",
        "IsFeaturedMerchant": true,
        "IsBuyBoxWinner": false,
        "IsFulfilledByAmazon": false
      },
      {
        "Shipping": {
          "CurrencyCode": "USD",
          "Amount": 0
        },
        "ListingPrice": {
          "CurrencyCode": "USD",
          "Amount": 194.09
        },
        "ShippingTime": {
          "maximumHours": 168,
          "minimumHours": 144,
          "availabilityType": "NOW"
        },
        "SellerFeedbackRating": {
          "FeedbackCount": 67953,
          "SellerPositiveFeedbackRating": 80
        },
        "ShipsFrom": {
          "State": "IL",
          "Country": "US"
        },
        "PrimeInformation": {
          "IsPrime": false,
          "IsNationalPrime": false
        },
        "SubCondition": "new",
        "SellerId": "A2G88111572J8M",
        "IsFeaturedMerchant": true,
        "IsBuyBoxWinner": true,
        "IsFulfilledByAmazon": false
      }
    ],
    "marketplaceId": "ATVPDKIKX0DER"
  }
}
```

API Section: Notifications

Related Seller/Developer ID(s): A2G88111572J8M

Marketplace(s) involved: ATVPDKIKX0DER

Related API operation(s): ANY_OFFER_CHANGED or https://developer-docs.amazon.com/sp-api/lang-es_ES/reference/getitemoffersbatch api call

SP-API application ID(s): amzn1.sp.solution.587a6205-d4a5-4379-8781-ec88935ae016

API Request ID(s): -

Request Timestamp(s): 2025-12-08 13:04 (GMT+2)
</question>

<response>
Hello from Amazon, 

This is Aashay from Amazon Solution Provider Support. 

I understand that you are concerned about the Pricing API and the AOC notification returning two buybox winners- both non-prime. 

Please note that one of these offers is eligible for prime (but may or may not be prime at the time of your call):
https://developer-docs.amazon.com/sp-api/reference/getitemoffers

IsBuyBoxWinner
When true, the offer is currently in the Buy Box. There can be up to two Buy Box winners at any time per ASIN, one that is eligible for Prime and one that is not eligible for Prime.
___

One of the two buy box winners is eligible for prime, but is not prime at the moment. The API and notification does not give information on which offer is eligible for prime, it only gives the current status. 

Also, one offer will have one landing price which will be equal to the product price plus shipping (and points in JP). Buybox is not dependent on only the landing price, but on several other factors, the details of which are not made available to anyone outside the team that has created the BuyBox algorithm.

Also, since the AOCN is also returning the same information, we can conclude that the API is working as designed.

Please create a new case if you need assistance in the future. This case will be closed as the API is working as designed.
</response>