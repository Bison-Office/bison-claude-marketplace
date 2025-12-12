---
name: accounting
description: Calculates supplier allowances (Marketing, DDR, General Discount) and volume rebates. Generates credit requests and credit memos. Use when working with CalculationType (ItemCost, ItemCostWithCredit, ItemCostWithCreditAllowance), ForCreditRequest, Earned, Accrual metrics, rebate tiers/steps, PaidToTheFirst, Growth rebate types, PaymentOnReachingStep, or ConditionType (CreditMemo, DeductedFromPayment).
---

# Supplier Allowances & Rebates

## CalculationType (Shared Across All Calculations)

| CalculationType | Revenue Formula |
|-----------------|-----------------|
| **ItemCost** | Revenue (no deductions) |
| Default / ItemCostWithCredit | Revenue - ItemCredits |
| ItemCostWithCreditAllowance | Revenue - ItemCredits - OtherAllowances |

## Documentation

**Allowance Calculations**: See [allowance-calculation.md](allowance-calculation.md)
- Marketing, DDR, General Discount percentage/fixed agreements
- Earned, ForCreditRequest, CreditReceived, Accrual metrics
- Priority distribution (DDR > Marketing > General Discount)

**Rebate Calculations**: See [rebate-calculation.md](rebate-calculation.md)
- PaidToTheFirst vs Growth rebate types
- Step thresholds (AmountFrom, AmountTo, RebatePercent)
- AmountAchieved, ProgressToNextStep metrics

**Allowance Credit Generation**: See [allowance-credit-generation.md](allowance-credit-generation.md)
- PeriodType: Monthly, Quarterly, Yearly
- ConditionType routing (CreditMemo vs DeductedFromPayment)
- Feature flags: GenerateCreditRequestForAgreement, GenerateCreditMemoForAgreement

**Rebate Credit Generation**: See [rebate-credit-generation.md](rebate-credit-generation.md)
- PaymentOnReachingStep trigger logic
- End-of-agreement settlements
- Feature flag: GenerateCreditItemsForRebate
