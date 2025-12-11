---
name: sp-api-reference
description: Reference for Amazon Selling Partner API (SP-API) endpoints used in this project. Use when implementing Amazon API integrations, working with pricing, reports, notifications, feeds, or product fees APIs. Includes endpoint documentation and authentication details.
---

# Amazon SP-API Reference

## Overview

This skill provides reference documentation for Amazon Selling Partner API endpoints used in the pricing engine.

## When to Use This Skill

- Implementing new Amazon API integrations
- Understanding available SP-API endpoints
- Working with pricing, reports, notifications, or feeds
- Debugging API authentication issues

## Implementation Notes

The SP-API client in this project uses:
- AWS SigV4 signing for request authentication
- LWA (Login with Amazon) for OAuth token management

## Pricing API

| Endpoint                | Description                                                                                       |
| ----------------------- | ------------------------------------------------------------------------------------------------- |
| `getPricing`            | Returns pricing information for a seller's offer listings based on seller SKU or ASIN             |
| `getCompetitivePricing` | Returns competitive pricing information for a seller's offer listings based on seller SKU or ASIN |
| `getListingOffers`      | Returns the lowest priced offers for a single SKU listing                                         |
| `getItemOffers`         | Returns the lowest priced offers for a single item based on ASIN                                  |

## Reports API

| Endpoint               | Description                                                                    |
| ---------------------- | ------------------------------------------------------------------------------ |
| `getReports`           | Returns report details for the reports that match the filters that you specify |
| `createReport`         | Creates a report                                                               |
| `getReport`            | Returns report details for the report that you specify                         |
| `cancelReport`         | Cancels the report that you specify                                            |
| `getReportSchedules`   | Returns report schedule details that match the filters that you specify        |
| `createReportSchedule` | Creates a report schedule                                                      |
| `getReportSchedule`    | Returns report schedule details for the report schedule that you specify       |
| `cancelReportSchedule` | Cancels the report schedule that you specify                                   |
| `getReportDocument`    | Returns the information required for retrieving a report document's contents   |

## Notifications API

| Endpoint                 | Description                                                                                              |
| ------------------------ | -------------------------------------------------------------------------------------------------------- |
| `getSubscription`        | Returns information about subscription of the specified notification type and payload version            |
| `createSubscription`     | Creates a subscription for the specified notification type to be delivered to the specified destination  |
| `getSubscriptionById`    | Returns information about a subscription for the specified notification type                             |
| `deleteSubscriptionById` | Deletes the subscription indicated by the subscription identifier and notification type that you specify |
| `getDestinations`        | Returns information about all destinations                                                               |
| `createDestination`      | Creates a destination resource to receive notifications                                                  |
| `getDestination`         | Returns information about the destination that you specify                                               |
| `deleteDestination`      | Deletes the destination that you specify                                                                 |

## Product Fees API

| Endpoint                   | Description                                                                                                                    |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `getMyFeesEstimateForSKU`  | Returns the estimated fees for the item indicated by the specified seller SKU in the marketplace specified in the request body |
| `getMyFeesEstimateForASIN` | Returns the estimated fees for the item indicated by the specified ASIN in the marketplace specified in the request body       |
| `getMyFeesEstimates`       | Returns the estimated fees for a list of products                                                                              |

## Feeds API

| Endpoint             | Description                                                                                         |
| -------------------- | --------------------------------------------------------------------------------------------------- |
| `getFeeds`           | Returns feed details for the feeds that match the filters that you specify                          |
| `createFeed`         | Creates a feed                                                                                      |
| `cancelFeed`         | Cancels the feed that you specify                                                                   |
| `getFeed`            | Returns feed details (including the `resultDocumentId`, if available) for the feed that you specify |
| `createFeedDocument` | Creates a feed document for the feed type that you specify                                          |
| `getFeedDocument`    | Returns the information required for retrieving a feed document's contents                          |

## Authentication Flow

1. **LWA Token**: Obtain access token using Login with Amazon OAuth
2. **SigV4 Signing**: Sign requests using AWS Signature Version 4
3. **Rate Limiting**: Respect API rate limits (varies by endpoint)

## Key Files

- `src/backend/PricingEngine/Abstractions/AmazonApi/` - SP-API client implementation
- `src/backend/PricingEngine/Modules/AmazonReports/` - Report handling module
- `src/backend/PricingEngine/Modules/RecommendationSync/` - Feed upload module

## External Documentation

For detailed request/response schemas, see the official Amazon SP-API documentation:
- https://developer-docs.amazon.com/sp-api/
