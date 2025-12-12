# Allowance Calculations

Covers **Marketing**, **DDR**, and **General Discount** allowances. For volume rebates, see [rebate-calculation.md](rebate-calculation.md).

## Data Sources

| View | Source | Description |
|------|--------|-------------|
| MonthlySupplierCosts | Orders -> OrderLines | `Quantity x VendorCost` from **shipped orders** (TrackingSentToBrokerOn set) |
| MonthlySupplierForCreditRequest | Invoices -> InvoiceItems | `ItemQuantity x UnitCost` from **supplier invoices** |
| MonthlySupplierItemCredits | WaitingOnCredit | `InitialAmount` of item credits pending from supplier |
| MonthlySupplierInvoiceAllowances | Invoices | Invoice-level allowance deductions (MarketingAllowanceInDiscount, DdrAllowanceInDiscount) |

## Calculation Type

Determines whether item credits are deducted:

| CalculationType | Item Credit Deduction |
|-----------------|----------------------|
| **ItemCost** | None - use full cost |
| Default / ItemCostWithCredit | Deduct proportional item credits |
| ItemCostWithCreditAllowance | Deduct item credits + other allowances |

## Earned Calculation

### Percentage-Based Agreements

```
Earned = (DailyCost x AgreedPercentage%) - ItemCreditDeduction
```

Where:
- `DailyCost` = From MonthlySupplierCosts (shipped order line costs)
- `ItemCreditDeduction`:
  - **ItemCost type**: `0` (no deduction)
  - **Other types**: `DailyItemCredit x AgreedPercentage%`

**Example:** 2% agreement, $10,000 daily cost, $500 daily item credits
- ItemCost: `$10,000 x 2% = $200`
- Default: `($10,000 x 2%) - ($500 x 2%) = $200 - $10 = $190`

### Fixed Amount Agreements

```
DailyEarned = FixedAmount / TotalDaysInAgreement
```

## ForCreditRequest Calculation

Same formula as Earned, but uses **ForCreditRequest data** (invoice costs) instead of shipped order costs:

```
ForCreditRequest = (DailyInvoiceCost x AgreedPercentage%) - ItemCreditDeduction
```

## Credit Received

### SeparateLineEachInvoice Payment Method

Automatically tracked from invoice allowance fields:
- `Invoice.MarketingAllowanceInDiscount`
- `Invoice.DdrAllowanceInDiscount`
- `Invoice.GeneralDiscountAllowanceInDiscount`

### Other Payment Methods (CreditMemo, Check)

Tracked via `CreditReceivedByPeriods` when credit memos are received.

## Priority Distribution

When distributing invoice discount to allowance types:

| Priority | Agreement Type |
|----------|---------------|
| 1 (first) | DDR |
| 2 | Marketing |
| 3 (last) | General Discount |

Higher priority agreements are satisfied first.

## Key Metrics

| Metric | Calculation |
|--------|-------------|
| Earned | `SUM(DailyCost x Percentage) - ItemCreditDeductions` |
| ForCreditRequest | `SUM(DailyInvoiceCost x Percentage) - ItemCreditDeductions` |
| CreditReceived | Invoice allowances + Credit memos received |
| Accrual | `Earned - CreditReceived - Adjustments - ReceivedInOtherPeriods - PreviousAccruals` |
| Remaining | `Requested - CreditReceived - ReceivedInOtherPeriods` |
