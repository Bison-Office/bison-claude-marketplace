# Rebate Credit Request/Memo Generation

Automatic generation of credit items for **Volume Rebate** agreements. For Marketing/DDR/General Discount allowances, see [allowance-credit-generation.md](allowance-credit-generation.md).

## Overview

Rebate credit generation differs from allowances because rebates use **cumulative revenue** across the agreement period with **tiered payouts**. Credits can be triggered:

1. **When a tier threshold is reached** (if `PaymentOnReachingStep = true`)
2. **At agreement end** (final settlement of remaining rebate)

| ConditionType | Output | Description |
|---------------|--------|-------------|
| **CreditMemo** | Credit Request | Request sent to supplier for payment |
| **DeductedFromPayment** | Credit Memo (Invoice) | Internal credit deducted from supplier payments |

## Key Files

- **Handler**: `repos/dotnet/libs/modules/src/BigHeavy.Modules.Accounting/Application/Common/Commands/GenerateAgreementCreditItems/GenerateCreditItemsForAgreementsCommandHandler.cs`
- **Method**: `GetNextRebateCreditPeriodAndAmount()`

## Generation Flow

### 1. Agreement Selection

Valid rebate agreements must have:
- `AgreementType = VolumeRebate`
- `IsDeleted = false` and `IsPeriodicCreditGenerationCompleted = false`
- `ConditionType` in [CreditMemo, DeductedFromPayment]
- Active date range (`DateFrom <= today <= DateTo + 1 day`)
- At least one non-deleted rebate step (`BusinessTermsRebateSteps`)

### 2. Feature Flag

Rebate credit generation requires:
- `GenerateCreditItemsForRebate` feature flag enabled for the supplier

This single flag controls both credit requests and credit memos for rebates.

### 3. Revenue Accumulation

Daily revenue is accumulated from agreement start date:

```csharp
CumulativeRevenue[date] = CumulativeRevenue[date-1] + DailyRevenue
```

**Daily Revenue** is calculated based on `CalculationType`:

| CalculationType | Daily Revenue Formula |
|-----------------|----------------------|
| **ItemCost** | `ForCreditRequest` |
| Default / ItemCostWithCredit | `ForCreditRequest - ItemCredits` |
| ItemCostWithCreditAllowance | `ForCreditRequest - ItemCredits - InvoiceAllowances - OtherAllowanceCredits` |

Data sources:
- `MonthlySupplierForCreditRequest` - Daily invoice costs
- `MonthlySupplierItemCredits` - Daily pending item credits
- `MonthlySupplierInvoiceAllowances` - Daily invoice allowance deductions
- `CreditReceivedByPeriods` - Other allowance credits (prorated daily)

### 4. Dependent Suppliers

If `IncludeDependentVendors = true`:
- Revenue from child suppliers (where `ParentVendorId = agreement.VendorId`) is included
- All data sources aggregate across parent + child supplier IDs

### 5. Trigger Conditions

#### A. Step Threshold Reached (PaymentOnReachingStep = true)

When cumulative revenue crosses a tier threshold:

```csharp
// For each day after last generated period
if (ShouldCalculateStep(rebateType, cumulativeRevenue, step))
{
    var totalRebate = CalculateRebateAmount(cumulativeRevenue);
    var incrementalAmount = totalRebate - previousPeriodsRequestedAmount;
    // Generate credit if incrementalAmount > 0
}
```

**ShouldCalculateStep logic**:
- **Fixed amount**: `cumulativeRevenue >= step.AmountFrom`
- **PaidToTheFirst**: `cumulativeRevenue >= step.AmountFrom`
- **Growth**: `cumulativeRevenue >= step.AmountTo` (must complete the tier)

#### B. Agreement End

When `today >= agreementEnd + 1 day`:

```csharp
agreement.IsPeriodicCreditGenerationCompleted = true;

var finalTotalRebate = CalculateRebateAmount(lastCumulativeRevenue, isLastDayOfAgreement: true);
var remainingAmount = finalTotalRebate - previousPeriodsRequestedAmount;
```

This generates a final credit for any remaining rebate amount.

### 6. Rebate Amount Calculation

**PaidToTheFirst** - Current tier applies to entire cumulative revenue:
```
For each step where revenue >= step.AmountFrom:
    If fixed: totalAmount += step.RebateFixedAmount
    If percentage: totalAmount = step.AmountFrom x step.RebatePercent%

At agreement end: totalAmount = min(revenue, stepEnd) x step.RebatePercent%
```

**Growth** - Each tier applies only to revenue within its range:
```
For each completed step:
    If fixed: totalAmount += step.RebateFixedAmount
    If percentage: totalAmount += (stepEnd - stepStart) x step.RebatePercent%
```

### 7. Incremental Amount

Each credit is **incremental** (not cumulative):
```
IncrementalAmount = CurrentTotalRebate - PreviouslyRequestedAmount
```

This prevents duplicate payments when multiple credits are generated during the agreement.

## Credit Request Output

For `ConditionType = CreditMemo`:

```csharp
new SupplierAgreementCreditRequest
{
    SupplierId = agreement.VendorId,
    AgreementId = agreement.Id,
    Amount = incrementalAmount,
    PeriodStart = lastGeneratedDate + 1 ?? agreementStart,
    PeriodEnd = triggerDate ?? agreementEnd,
    Status = CreditRequestStatus.Requested,
}
```

### Deduction of Received Credits

Before creating a credit request, any overlapping credit memos already received are deducted proportionally.

## Credit Memo Output

For `ConditionType = DeductedFromPayment`:

Creates an `Invoice` with:
```csharp
new Invoice
{
    IsCredit = true,
    PartnerInvoiceAmount = incrementalAmount,
    InvoiceNumber = "DFP Volume Rebate {PeriodStart:yyyy/MM}",
    Status = amount == 0 ? Paid : Posted,
    AgreementId = agreement.Id,
}
```

And an `InvoiceItem` with:
```csharp
new InvoiceItem
{
    UnitCost = incrementalAmount,
    Type = SupplierCreditType.Rebate,
    ReasonForCredit = "REBATE",
    CreditPeriodStart = periodStart,
    CreditPeriodEnd = periodEnd,
}
```

## Example: Step Threshold Trigger

**Agreement**: Volume Rebate, PaidToTheFirst, PaymentOnReachingStep = true

**Steps**:
| Step | AmountFrom | AmountTo | RebatePercent |
|------|------------|----------|---------------|
| 1 | $0 | $100,000 | 1% |
| 2 | $100,000 | $200,000 | 2% |

**Day 45**: Cumulative revenue = $105,000
- Crossed $100,000 threshold (Step 2)
- Total rebate: `$100,000 x 2% = $2,000`
- Previous requested: $0
- Credit generated: **$2,000** for period Day 1 - Day 45

**Day 90 (Agreement End)**: Cumulative revenue = $150,000
- Final calculation: `$150,000 x 2% = $3,000`
- Previous requested: $2,000
- Credit generated: **$1,000** for period Day 46 - Day 90

## Example: Growth Rebate at Agreement End

**Agreement**: Volume Rebate, Growth type

**Steps**: Same as above

**Agreement End**: Cumulative revenue = $150,000
- Step 1: `$100,000 x 1% = $1,000`
- Step 2: `($150,000 - $100,000) x 2% = $1,000`
- Total rebate: **$2,000**

## Other Allowance Credits Deduction

For `ItemCostWithCreditAllowance` calculation type, `CreditReceivedByPeriods` records are converted to daily amounts:

```csharp
// Prorate credit across overlap with agreement period
var proratedTotal = period.Total x overlapDays / totalPeriodDays;
var dailyAmount = proratedTotal / overlapDays;
```

This ensures credits received for other allowances reduce the rebate revenue base.
