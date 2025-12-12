# Rebate Calculations

Volume rebates are tiered incentives based on cumulative purchasing. For Marketing/DDR/General Discount allowances, see [allowance-calculation.md](allowance-calculation.md).

## Data Sources

Rebates use the same data sources as allowances, with CalculationType determining deductions:

| CalculationType | Revenue Formula |
|-----------------|-----------------|
| **ItemCost** | `Revenue` (no deductions) |
| Default / ItemCostWithCredit | `Revenue - ItemCredits` |
| ItemCostWithCreditAllowance | `Revenue - ItemCredits - OtherAllowances` |

Where:
- `Revenue` = MonthlySupplierForCreditRequest (invoice costs)
- `ItemCredits` = MonthlySupplierItemCredits
- `OtherAllowances` = Other allowance credits received in period

## Rebate Steps

Each agreement has tiers (steps) with thresholds:

| Field | Description |
|-------|-------------|
| AmountFrom | Revenue threshold to reach this tier |
| AmountTo | Upper bound (or unlimited) |
| RebatePercent | Percentage rebate (mutually exclusive with fixed) |
| RebateFixedAmount | Fixed dollar rebate (mutually exclusive with percentage) |

## Rebate Types

### PaidToTheFirst

Current tier's percentage applies to **entire cumulative revenue**:

```
Rebate = TotalRevenue x CurrentTierPercent
```

**Example:** $150,000 revenue, tiers: $0-100K=1%, $100K-200K=2%
- Current tier: 2% (revenue exceeded $100K)
- Rebate: `$150,000 x 2% = $3,000`

For fixed amounts, sum all fixed amounts from tier 0 to current tier.

### Growth

Each tier's percentage applies only to **revenue within that tier's range**:

```
Rebate = SUM(TierRange x TierPercent)
```

**Example:** $150,000 revenue, tiers: $0-100K=1%, $100K-200K=2%
- Tier 1: `$100,000 x 1% = $1,000`
- Tier 2: `($150,000 - $100,000) x 2% = $1,000`
- Total: `$2,000`

## Monthly Calculation Flow

1. **Get monthly revenue** using CalculationType formula
2. **Accumulate** revenue across months within agreement period
3. **Determine current tier** based on accumulated revenue
4. **Calculate expected rebate** (ForCreditRequest) using rebate type formula
5. **Calculate accrual** = ForCreditRequest - CumulativeCreditReceived - CumulativeReceivedInOtherPeriods - PreviousAccruals

## Key Metrics

| Metric | Description |
|--------|-------------|
| RevenueRecognizedForRebate | Monthly revenue after CalculationType deductions |
| AmountAchieved | Cumulative revenue year-to-date |
| AchievedStepValue | Current tier's percentage or fixed amount |
| NextStepTarget | Revenue needed to reach next tier |
| ProgressToNextStep | Percentage progress to next tier |
| ForCreditRequest | Expected rebate based on current tier (cumulative) |
| CreditReceived | Credits received for this period |
| ReceivedInOtherPeriods | Credits received but attributed to other periods |
| Accrual | Monthly accrual = ForCreditRequest - CumulativeCreditReceived - CumulativeReceivedInOtherPeriods - PreviousAccruals |

## Special Features

### Dependent Suppliers

If `IncludeDependentVendors = true`, child supplier revenue is included in parent's rebate calculation.

### Agreement Period

Rebates are typically annual. Revenue accumulates from agreement start date, and tiers reset at agreement end.
