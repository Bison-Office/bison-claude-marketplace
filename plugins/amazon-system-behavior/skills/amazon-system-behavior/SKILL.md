---
name: amazon-system-behavior
description: Documents undocumented Amazon SP-API behaviors from support cases. Use when debugging unexpected API responses, buy box anomalies, or report/API data discrepancies.
---

# Amazon System Behavior

Undocumented SP-API behaviors learned from Amazon support cases.

## Quick Reference

| Behavior | Case | Key Insight |
|----------|------|-------------|
| Multiple buy box winners | [MULTIPLE_HAS_BUY_BOX](amazon-case-history/MULTIPLE_HAS_BUY_BOX.md) | 2 winners allowed per ASIN (Prime-eligible + non-Prime). Both show `IsBuyBoxWinner=true`. |
| Orphaned listings | [UNABLE_TO_DELETE_OFFERS](amazon-case-history/UNABLE_TO_DELETE_OFFERS.md) | Reports return ASINs deleted from catalog. Use `GET_MERCHANT_LISTINGS_INACTIVE_DATA` to identify. |

## Support Portals

- **[Solution Provider Portal](https://solutionproviderportal.amazon.com/)** - SP-API technical questions
- **[Seller Central](https://sellercentral.amazon.com/)** - Account/listing issues

## Adding Cases

1. Create file in `amazon-case-history/` using format: `<case_id>`, `<question>`, `<response>`
2. Add row to Quick Reference table above