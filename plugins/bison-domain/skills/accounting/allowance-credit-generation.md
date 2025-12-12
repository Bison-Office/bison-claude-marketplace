# Allowance Credit Request/Memo Generation

Automatic generation of credit items for **Marketing**, **DDR**, and **General Discount** allowances. For volume rebates, see [rebate-credit-generation.md](rebate-credit-generation.md).

## Overview

The system generates credit items at the end of each agreement period (Monthly, Quarterly, Yearly). The output type depends on `ConditionType`:

| ConditionType | Output | Description |
|---------------|--------|-------------|
| **CreditMemo** | Credit Request | Request sent to supplier for payment |
| **DeductedFromPayment** | Credit Memo (Invoice) | Internal credit deducted from supplier payments |

## Key Files

- **Handler**: `repos/dotnet/libs/modules/src/BigHeavy.Modules.Accounting/Application/Common/Commands/GenerateAgreementCreditItems/GenerateCreditItemsForAgreementsCommandHandler.cs`
- **Period Helper**: `repos/dotnet/libs/shared/src/BigHeavy.Contracts/Utils/PeriodHelper.cs`
- **Credit Utils**: `repos/dotnet/libs/shared/src/BigHeavy.Infrastructure.Common/Utils/CreditRequestUtils.cs`

## Generation Flow

### 1. Agreement Selection

Valid agreements must have:
- `IsDeleted = false` and `IsPeriodicCreditGenerationCompleted = false`
- `PeriodType` in [Monthly, Quarterly, Yearly]
- `ConditionType` in [CreditMemo, DeductedFromPayment]
- Active date range (`DateFrom <= today <= DateTo + 1 day`)
- Supplier not permanently inactive
- `AgreedOn > 0` or `AgreedOnFixedAmount > 0`

DDR agreements additionally require `AgreedOnType = Allowance`.

### 2. Feature Flags

Generation is controlled per-supplier:
- `GenerateCreditRequestForAgreement` - enables Credit Request generation
- `GenerateCreditMemoForAgreement` - enables Credit Memo generation

### 3. Period Determination

```
NextPeriodStart = MAX(LastGeneratedPeriodEnd + 1, AgreementStart, GenerateFromDate)
```

Period range calculated using `PeriodHelper.GetPeriodRange()`:
- **Monthly**: First to last day of month
- **Quarterly**: First day of quarter to last day of quarter
- **Yearly**: Jan 1 to Dec 31

Generation only occurs after period ends (`IsPeriodEnded = true`).

### 4. Amount Calculation

```csharp
Amount = (ForCreditRequestsForPeriod - ItemCreditsForPeriod) x AgreedPercentage
```

| CalculationType | Formula |
|-----------------|---------|
| **ItemCost** | `ForCreditRequests x AgreedOn%` |
| Default / ItemCostWithCredit | `(ForCreditRequests - ItemCredits) x AgreedOn%` |

For fixed amount agreements:
```
Amount = AgreedOnFixedAmount
```

Data sources:
- `MonthlySupplierForCreditRequest` - Daily invoice costs
- `MonthlySupplierItemCredits` - Daily pending item credits

## Credit Request Output

For `ConditionType = CreditMemo`:

```csharp
new SupplierAgreementCreditRequest
{
    SupplierId = agreement.VendorId,
    AgreementId = agreement.Id,
    Amount = calculatedAmount,
    PeriodStart = periodStart,
    PeriodEnd = periodEnd,
    Status = CreditRequestStatus.Requested,
}
```

### Deduction of Received Credits

Before creating a credit request, any overlapping credit memos already received for the same agreement/period are deducted:

```
FinalAmount = CalculatedAmount - ProRatedReceivedCredits
```

Overlap is calculated proportionally based on days.

### Email Notification

When `Amount > 0`, an email is sent to the supplier's **Account Receivable** contact with:
- Credit amount and currency
- Agreement type (Marketing/DDR/General Discount)
- Link to supplier portal credit requests page

## Credit Memo Output

For `ConditionType = DeductedFromPayment`:

Creates an `Invoice` with:
```csharp
new Invoice
{
    IsCredit = true,
    PartnerInvoiceAmount = amount,
    InvoiceNumber = "DFP {AgreementType} {PeriodStart:yyyy/MM}",
    Status = amount == 0 ? Paid : Posted,
    AgreementId = agreement.Id,
}
```

And an `InvoiceItem` with:
```csharp
new InvoiceItem
{
    UnitCost = amount,
    ItemQuantity = 1,
    Type = CreditTypeUtils.MapFromAgreementType(agreementType),
    ReasonForCredit = creditType.ToString().ToUpper(),
    SourceOfCredit = "{Period} {AgreementType} allowance",
    CreditPeriodStart = periodStart,
    CreditPeriodEnd = periodEnd,
}
```

Credit types:
- Marketing -> `SupplierCreditType.Marketing`
- DDR -> `SupplierCreditType.DdrAllowance`
- General Discount -> `SupplierCreditType.GeneralDiscount`

A `CreditMemoCreated` domain event is dispatched after creation.

## Period Completion

When the last period of an agreement is processed:
```csharp
agreement.IsPeriodicCreditGenerationCompleted = true
```

This prevents duplicate generation in future runs.

## Example Calculation

**Agreement**: 2% Marketing, Monthly, ItemCost calculation type

**September 2025**:
- ForCreditRequests: $50,000
- ItemCredits: $2,000 (ignored for ItemCost)
- Amount: `$50,000 x 2% = $1,000`

**With Default calculation type**:
- Amount: `($50,000 - $2,000) x 2% = $960`
