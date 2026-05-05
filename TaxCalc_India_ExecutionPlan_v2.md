# TaxCalc India — Phase-Wise Execution Plan
## FY 2025-26 (AY 2026-27) | Feature Expansion PRD
### New Features: Surcharge, Multi-Income Types, Capital Gains, PDF Export

**Version:** 2.0  
**Date:** May 2026  
**Status:** Execution Plan for AI Coding Model  
**Base Document:** TaxCalc_India_PRD_FY2025-26.md (v1.0)

---

## 0. Overview of Changes

This document provides a phase-wise execution plan to add four major features to the existing TaxCalc India app:

1. **Surcharge Calculation** (10%–37%) for incomes above ₹50 lakh, with marginal relief
2. **Multi-Income Type Support** — Salaried, Freelancer, Business, Consulting with appropriate tax structures
3. **Capital Gains Module** — Stocks, Mutual Funds, Crypto, Property with STCG/LTCG rates
4. **PDF Export** — Client-side generation of full tax summary

**Execution Philosophy:** Each phase is self-contained, testable, and builds on previous phases. An AI coding model should implement one phase at a time, run tests, then proceed.

---

## PHASE 1: Foundation & Data Architecture

### 1.1 Extend the Data Model

**File:** `src/types/tax.ts`

Add new enums and interfaces:

```typescript
// NEW: Income type selection
export type IncomeType = 'salaried' | 'freelancer' | 'business' | 'consulting';

// NEW: Capital gains asset types
export type AssetType = 'equityShares' | 'equityMF' | 'debtMF' | 'crypto' | 'property';
export type GainType = 'STCG' | 'LTCG';

// NEW: Surcharge brackets
export interface SurchargeBracket {
  minIncome: number;
  maxIncome: number;
  oldRegimeRate: number;
  newRegimeRate: number;
}

// NEW: Capital gains transaction
export interface CapitalGainsTransaction {
  id: string;
  assetType: AssetType;
  gainType: GainType;
  saleConsideration: number;
  costOfAcquisition: number;
  holdingPeriodMonths: number;
  sttPaid: boolean;           // For equity shares/MF
  purchaseDate: string;       // YYYY-MM-DD, crucial for debt MF
  saleDate: string;           // YYYY-MM-DD
  // For property
  isResidential: boolean;
  reinvestment54F: number;    // Amount reinvested u/s 54/54F
}

// EXTENDED: Freelancer/Consulting specific
export interface FreelancerIncome {
  grossReceipts: number;
  isSpecifiedProfessional: boolean; // true for consulting/freelancer
  digitalReceiptsPercentage: number; // 0-100
  presumptiveRate: number;    // 50% for 44ADA, 8%/6% for 44AD
  optedPresumptive: boolean;
  actualExpenses: number;     // If not opting presumptive
}

// EXTENDED: Business specific
export interface BusinessIncome {
  turnover: number;
  digitalReceiptsPercentage: number;
  presumptiveRate: number;    // 8% or 6%
  optedPresumptive: boolean;
  actualNetProfit: number;    // If not opting presumptive
}

// EXTENDED: Main taxpayer info
export interface TaxpayerInfo {
  incomeType: IncomeType;
  ageCategory: 'below60' | 'senior' | 'superSenior';
  monthlyInHand?: number;     // Optional for non-salaried
  // ... existing fields
}

// EXTENDED: Tax result
export interface TaxResult {
  oldRegime: RegimeResult;
  newRegime: RegimeResult;
  recommendation: 'old' | 'new';
  savingsAmount: number;
  // NEW:
  capitalGainsTax: CapitalGainsTaxResult;
  totalTaxLiability: TotalTaxResult;  // Income tax + CG tax
}

// NEW: Capital gains tax result
export interface CapitalGainsTaxResult {
  transactions: CapitalGainsTransaction[];
  stcgTax: number;
  ltcgTax: number;
  cryptoTax: number;
  propertyTax: number;
  totalCapitalGainsTax: number;
  surchargeOnCG: number;
  cessOnCG: number;
  totalCGTaxWithCess: number;
  breakdown: CGBreakdownItem[];
}

export interface CGBreakdownItem {
  assetType: AssetType;
  gainType: GainType;
  gainAmount: number;
  taxableAmount: number;
  taxRate: number;
  tax: number;
  notes: string[];
}

// NEW: Surcharge result
export interface SurchargeResult {
  applicable: boolean;
  rate: number;
  amount: number;
  marginalRelief: number;
  netSurcharge: number;
}

// NEW: Grand total
export interface TotalTaxResult {
  incomeTax: number;
  surchargeOnIncome: number;
  cessOnIncome: number;
  capitalGainsTax: number;
  surchargeOnCG: number;
  cessOnCG: number;
  grandTotal: number;
  effectiveTaxRate: number;
}
```

### 1.2 Update State Management

**File:** `src/store/taxStore.ts` (or context)

Add new state slices:
- `incomeType: IncomeType`
- `capitalGainsTransactions: CapitalGainsTransaction[]`
- `freelancerIncome: FreelancerIncome`
- `businessIncome: BusinessIncome`

Add actions:
- `setIncomeType(type: IncomeType)`
- `addCapitalGainsTransaction(tx: CapitalGainsTransaction)`
- `removeCapitalGainsTransaction(id: string)`
- `updateCapitalGainsTransaction(id: string, tx: Partial<CapitalGainsTransaction>)`
- `setFreelancerIncome(data: FreelancerIncome)`
- `setBusinessIncome(data: BusinessIncome)`

### 1.3 Update Wizard Flow

**File:** `src/components/Wizard/FlowConfig.ts`

Modify the flow to branch based on `incomeType`:

```
[STEP 0: Income Type Selection]
  ├─ SALARIED → [Existing 10-step salaried flow]
  ├─ FREELANCER → [Freelancer flow: 6 steps]
  ├─ CONSULTING → [Consulting flow: 6 steps]  
  └─ BUSINESS → [Business flow: 6 steps]
    ↓
[Capital Gains Step] (Common to all, optional)
    ↓
[Review & Calculate]
    ↓
[Results with PDF Export]
```

### 1.4 Create Constants File

**File:** `src/constants/taxRules.ts`

```typescript
// Surcharge brackets FY 2025-26
export const SURCHARGE_BRACKETS = [
  { min: 0, max: 5000000, oldRate: 0, newRate: 0 },
  { min: 5000001, max: 10000000, oldRate: 0.10, newRate: 0.10 },
  { min: 10000001, max: 20000000, oldRate: 0.15, newRate: 0.15 },
  { min: 20000001, max: 50000000, oldRate: 0.25, newRate: 0.25 },
  { min: 50000001, max: Infinity, oldRate: 0.37, newRate: 0.25 },
];

// Capital gains rates FY 2025-26
export const CG_RATES = {
  equitySTCG: 0.20,           // Section 111A
  equityLTCG: 0.125,          // Section 112A
  equityLTCGExemption: 125000, // ₹1.25L exemption
  debtMF_STCG_Slab: true,     // Taxed at slab rates
  debtMF_LTCG_Pre2023: 0.125, // Purchased before 1 Apr 2023
  debtMF_HoldingPeriod: 24,   // months
  crypto: 0.30,               // Section 115BBH - flat for both STCG & LTCG
  propertySTCG_Slab: true,    // Taxed at slab rates
  propertyLTCG: 0.125,        // Section 112
  propertyHoldingPeriod: 24,  // months
};

// Surcharge cap for specified capital gains
export const CG_SURCHARGE_CAP = 0.15; // 15% max on 111A, 112, 112A

// Presumptive taxation
export const PRESUMPTIVE_LIMITS = {
  freelancer_44ADA: {
    normal: 5000000,
    digital: 7500000,         // 95% digital receipts
    rate: 0.50,
  },
  business_44AD: {
    normal: 20000000,
    digital: 30000000,        // 95% digital receipts
    rate: 0.08,
    digitalRate: 0.06,
  },
};

// Cess rate
export const CESS_RATE = 0.04;
```

### 1.5 Deliverables
- [ ] Updated TypeScript interfaces
- [ ] Updated state management
- [ ] Updated wizard flow configuration
- [ ] Constants file with all new tax rules
- [ ] Unit tests for type definitions

---

## PHASE 2: Surcharge & Marginal Relief Engine

### 2.1 Surcharge Calculation Logic

**File:** `src/lib/calculations/surcharge.ts`

```typescript
interface SurchargeInput {
  taxableIncome: number;
  regime: 'old' | 'new';
  capitalGains111A: number;   // STCG on equity
  capitalGains112: number;    // LTCG on property, etc.
  capitalGains112A: number;   // LTCG on equity
  dividendIncome: number;
}

function calculateSurcharge(input: SurchargeInput): SurchargeResult {
  // Step 1: Determine base surcharge rate based on total income
  const { taxableIncome, regime } = input;

  let rate = 0;
  for (const bracket of SURCHARGE_BRACKES) {
    if (taxableIncome > bracket.min && taxableIncome <= bracket.max) {
      rate = regime === 'old' ? bracket.oldRate : bracket.newRate;
      break;
    }
  }

  if (rate === 0) {
    return { applicable: false, rate: 0, amount: 0, marginalRelief: 0, netSurcharge: 0 };
  }

  // Step 2: Calculate tax on normal income (excluding special rate CG)
  const specialRateCG = input.capitalGains111A + input.capitalGains112 + 
                        input.capitalGains112A + input.dividendIncome;
  const normalIncome = taxableIncome - specialRateCG;

  // Step 3: Calculate surcharge on normal income at full rate
  const normalTax = calculateSlabTax(normalIncome, ageCategory, regime);
  const surchargeOnNormal = normalTax * rate;

  // Step 4: Calculate surcharge on special rate CG at capped 15%
  const cgTax111A = input.capitalGains111A * CG_RATES.equitySTCG;
  const cgTax112 = input.capitalGains112 * CG_RATES.propertyLTCG;
  const cgTax112A = Math.max(0, input.capitalGains112A - CG_RATES.equityLTCGExemption) * CG_RATES.equityLTCG;
  const dividendTax = input.dividendIncome * getDividendTaxRate(); // slab rate

  const totalCGTax = cgTax111A + cgTax112 + cgTax112A + dividendTax;
  const surchargeOnCG = totalCGTax * Math.min(rate, CG_SURCHARGE_CAP);

  // Step 5: Total surcharge before marginal relief
  const totalSurcharge = surchargeOnNormal + surchargeOnCG;

  // Step 6: Marginal relief calculation
  // Find the threshold that triggered surcharge
  const threshold = getSurchargeThreshold(taxableIncome);
  const taxAtThreshold = calculateTaxAtThreshold(threshold, regime);
  const taxOnCurrentIncome = calculateTotalTax(taxableIncome, regime);

  // Marginal relief = min(surcharge, excessTaxOverIncome)
  // Where excessTaxOverIncome = (taxOnCurrentIncome + totalSurcharge) - (taxAtThreshold + incomeExcess)
  const incomeExcess = taxableIncome - threshold;
  const totalTaxWithSurcharge = taxOnCurrentIncome + totalSurcharge;
  const taxAtThresholdWithCess = taxAtThreshold * 1.04; // Including cess at threshold

  let marginalRelief = 0;
  if (totalTaxWithSurcharge > taxAtThresholdWithCess + incomeExcess) {
    marginalRelief = totalTaxWithSurcharge - (taxAtThresholdWithCess + incomeExcess);
  }

  const netSurcharge = Math.max(0, totalSurcharge - marginalRelief);

  return {
    applicable: true,
    rate,
    amount: totalSurcharge,
    marginalRelief,
    netSurcharge,
  };
}
```

### 2.2 Marginal Relief Edge Cases

**Marginal Relief applies at four thresholds:**
1. ₹50 lakh → 10% surcharge kicks in
2. ₹1 crore → 15% surcharge kicks in
3. ₹2 crore → 25% surcharge kicks in
4. ₹5 crore → 37% (old) / 25% (new) surcharge kicks in

**Formula for marginal relief at each threshold:**
```
Relief = (Tax on current income + Surcharge + Cess) 
         − (Tax at threshold + Income excess over threshold)
```

If relief > 0, reduce surcharge by that amount.

**Example (Old Regime, ₹51 lakh income):**
- Tax on ₹51L = ₹13,42,500
- Surcharge @10% = ₹1,34,250
- Total before relief = ₹14,76,750
- Tax at ₹50L threshold = ₹13,12,500
- Income excess = ₹1,00,000
- Relief = ₹14,76,750 − (₹13,12,500 + ₹1,00,000) = ₹64,250
- Net surcharge = ₹1,34,250 − ₹64,250 = ₹70,000

### 2.3 Integration with Existing Tax Engine

**File:** `src/lib/calculations/taxEngine.ts`

Modify `calculateOldRegime()` and `calculateNewRegime()`:

```typescript
// After calculating taxAfterRebate, add:
const surchargeInput = {
  taxableIncome,
  regime: 'old', // or 'new'
  capitalGains111A: getSTCGEquity(),
  capitalGains112: getLTCGProperty(),
  capitalGains112A: getLTCGEquity(),
  dividendIncome: getDividendIncome(),
};

const surchargeResult = calculateSurcharge(surchargeInput);
const cess = CESS_RATE * (taxAfterRebate + surchargeResult.netSurcharge);
const totalTax = taxAfterRebate + surchargeResult.netSurcharge + cess;
```

### 2.4 Update Result Interface

Add surcharge details to `RegimeResult`:
```typescript
interface RegimeResult {
  // ... existing fields
  surcharge: SurchargeResult;
  taxBeforeCess: number;  // Renamed from taxAfterRebate for clarity
  cess: number;
  totalTax: number;
}
```

### 2.5 Deliverables
- [ ] Surcharge calculation module with marginal relief
- [ ] Unit tests for all 4 surcharge thresholds in both regimes
- [ ] Unit tests for marginal relief at each threshold
- [ ] Unit tests for CG surcharge cap (15% max)
- [ ] Integration with existing tax engine
- [ ] Updated result display to show surcharge breakdown

---

## PHASE 3: Multi-Income Type Support

### 3.1 Income Type Selection Step (New Step 0)

**File:** `src/components/Wizard/Steps/Step0_IncomeType.tsx`

**Question:** "What best describes your primary source of income?"

**Options (card-based selection with icons):**

| Option | Icon | Description |
|--------|------|-------------|
| Salaried Employee | 💼 | "I receive a monthly salary from an employer" |
| Freelancer | 📝 | "I work independently on projects (design, writing, coding, etc.)" |
| Consultant | 🎯 | "I provide professional services (CA, doctor, lawyer, architect, etc.)" |
| Business Owner | 🏪 | "I run my own business or shop" |

**FAQ:**
- Q: "I have both salary and freelance income. Which do I pick?"
  A: "Pick the one that contributes more than 50% of your total income. You can add capital gains separately later."
- Q: "I'm a freelancer but also have a small business."
  A: "If you're a specified professional (IT, design, medical, legal, etc.), pick Consultant. Otherwise, pick Business Owner."

### 3.2 Freelancer / Consultant Flow (Section 44ADA)

**File:** `src/components/Wizard/Flows/FreelancerFlow.tsx`

**Step F1: Gross Receipts**
- "What were your total gross receipts this year?"
- Currency input (annual)
- Tooltip: "Total money received from clients before any expenses. Check your bank statements."

**Step F2: Digital vs Cash Receipts**
- "What percentage of your receipts came through digital modes?"
- Slider: 0% to 100%
- Auto-calculate: If ≥95%, limit = ₹75L; else limit = ₹50L
- Show warning if receipts exceed limit: "Your receipts exceed the presumptive scheme limit. You'll need to maintain books and get audited."

**Step F3: Presumptive or Actual?**
- "How do you want to calculate your taxable income?"
- Option A: "Simple way — 50% of my receipts is my taxable income (Section 44ADA)"
  - Show: "Taxable income = ₹X (50% of ₹Y)"
  - Note: "You cannot claim any business expenses. But you can claim 80C, 80D, etc."
- Option B: "Detailed way — I'll enter my actual income after expenses"
  - Show fields: Total expenses, actual net profit
  - Warning: "If your actual income is less than 50% of receipts AND exceeds basic exemption limit, tax audit is mandatory."

**Step F4: Deductions (same as salaried 80C, 80D, etc.)**
- Chapter VI-A deductions are allowed even under presumptive taxation

**Step F5: Other Income**
- Interest income, other sources

**Step F6: Review**

**Calculation Logic for 44ADA:**
```typescript
function calculateFreelancerTax(data: FreelancerIncome, regime: 'old' | 'new'): number {
  let taxableBusinessIncome: number;

  if (data.optedPresumptive) {
    taxableBusinessIncome = data.grossReceipts * data.presumptiveRate;
  } else {
    taxableBusinessIncome = data.actualNetProfit;
  }

  // Add other income (interest, etc.)
  const grossTotalIncome = taxableBusinessIncome + otherIncome;

  // Apply standard deduction (NOT available for business income — only salaried)
  // Note: Business/freelancer income does NOT get standard deduction

  // Apply Chapter VI-A deductions (allowed in old regime)
  const deductions = regime === 'old' ? calculateChapterVIA(data) : 0;

  const taxableIncome = Math.max(0, grossTotalIncome - deductions);

  return calculateSlabTax(taxableIncome, ageCategory, regime);
}
```

**Key Rules for 44ADA:**
- Specified professionals: Legal, medical, engineering, architectural, accountancy, technical consultancy, interior decoration, film artists, company secretaries, information technology [^23^]
- Presumptive income: 50% of gross receipts
- Turnover limit: ₹50 lakh (₹75 lakh if ≥95% digital receipts)
- No expense deduction allowed if opting presumptive
- Chapter VI-A deductions (80C, 80D, etc.) ARE allowed
- Must file ITR-4 if presumptive, ITR-3 if regular
- 5-year lock-in: Once opted, must continue for 5 years. Opting out bars re-entry for 5 years.

### 3.3 Business Owner Flow (Section 44AD)

**File:** `src/components/Wizard/Flows/BusinessFlow.tsx`

**Step B1: Turnover**
- "What was your total business turnover this year?"
- Currency input
- Tooltip: "Total sales/invoices. Not profit."

**Step B2: Digital Receipts %**
- Slider 0-100%
- If ≥95%: limit = ₹3Cr, rate = 6%
- If <95%: limit = ₹2Cr, rate = 8%

**Step B3: Presumptive or Actual?**
- Option A: "Simple way — [6%/8%] of turnover is my taxable income"
  - Show calculated taxable income
- Option B: "Detailed way — actual profit/loss"
  - Fields: Cost of goods sold, operating expenses, actual net profit

**Step B4-B6:** Same deduction and review steps as freelancer

**Key Rules for 44AD:** [^28^]
- Applicable to: Resident individuals, HUFs, partnership firms (not LLPs)
- Turnover limit: ₹2Cr (₹3Cr if ≥95% digital)
- Presumptive income: 8% of turnover (6% for digital receipts)
- No expense deduction if presumptive
- Chapter VI-A deductions allowed
- Must file ITR-4
- 5-year lock-in rule applies
- Not applicable to: Transport businesses (44AE), agencies, commission businesses

### 3.4 Salaried Flow Modifications

- Keep existing 10-step flow largely unchanged
- Add income type badge in UI
- Ensure standard deduction (₹50K old / ₹75K new) is ONLY applied for salaried

### 3.5 Income-Specific Result Page Updates

**For Freelancer/Consulting:**
- Show: "Taxable Income (44ADA): ₹X (50% of gross receipts)"
- Show: "Gross Receipts: ₹Y"
- Show regime comparison with note: "Business income does not qualify for standard deduction"

**For Business:**
- Show: "Taxable Income (44AD): ₹X ([6%/8%] of turnover)"
- Show: "Turnover: ₹Y"

### 3.6 Deliverables
- [ ] Income type selection step with 4 options
- [ ] Freelancer/Consultant 6-step wizard (44ADA)
- [ ] Business owner 6-step wizard (44AD)
- [ ] Salaried flow preserved with minor updates
- [ ] Presumptive tax calculation engine
- [ ] Warning system for audit requirements
- [ ] Income-type-specific result cards
- [ ] Unit tests for all 4 income types

---

## PHASE 4: Capital Gains Module

### 4.1 Capital Gains Input Step (Common to All Income Types)

**File:** `src/components/Wizard/Steps/CapitalGainsStep.tsx`

**Question:** "Did you sell any investments or property this year?"
**Input:** Yes / No toggle

**If Yes — Transaction Entry Form:**

```
┌─────────────────────────────────────────┐
│  Add Capital Gains Transaction          │
├─────────────────────────────────────────┤
│  Asset Type: [Dropdown]                 │
│    • Listed Equity Shares               │
│    • Equity Mutual Funds                │
│    • Debt Mutual Funds                  │
│    • Cryptocurrency / VDA               │
│    • Real Estate / Property             │
├─────────────────────────────────────────┤
│  Sale Amount (₹)      [________]        │
│  Purchase Amount (₹)  [________]        │
│  Purchase Date        [DD/MM/YYYY]      │
│  Sale Date            [DD/MM/YYYY]      │
├─────────────────────────────────────────┤
│  [Conditional Fields]                   │
│  • STT Paid? [Yes/No] (Equity only)     │
│  • Is Residential Property? (Prop only) │
│  • Reinvestment u/s 54/54F (Prop only)  │
├─────────────────────────────────────────┤
│  [Add Transaction]                      │
└─────────────────────────────────────────┘
```

**Transaction List View:**
- Table showing all added transactions
- Columns: Asset, Gain Type, Gain Amount, Tax, Actions
- "Remove" button per row
- Auto-calculated gain = Sale − Purchase
- Auto-determined STCG/LTCG based on holding period

**Validation Rules:**
- Sale amount > 0
- Purchase amount ≥ 0
- Sale date > Purchase date
- For equity: STT paid mandatory for 111A/112A benefit
- For debt MF purchased before 1 Apr 2023: allow LTCG classification

### 4.2 Capital Gains Calculation Engine

**File:** `src/lib/calculations/capitalGains.ts`

```typescript
function calculateCapitalGainsTax(transactions: CapitalGainsTransaction[]): CapitalGainsTaxResult {
  const breakdown: CGBreakdownItem[] = [];
  let totalSTCG = 0;
  let totalLTCG = 0;
  let totalCryptoTax = 0;
  let totalPropertyTax = 0;

  for (const tx of transactions) {
    const gain = tx.saleConsideration - tx.costOfAcquisition;
    const holdingMonths = tx.holdingPeriodMonths;

    switch (tx.assetType) {
      case 'equityShares':
      case 'equityMF': {
        const isLongTerm = holdingMonths > 12;
        if (isLongTerm) {
          // Section 112A
          const taxableLTCG = Math.max(0, gain - CG_RATES.equityLTCGExemption);
          const tax = taxableLTCG * CG_RATES.equityLTCG;
          breakdown.push({
            assetType: tx.assetType,
            gainType: 'LTCG',
            gainAmount: gain,
            taxableAmount: taxableLTCG,
            taxRate: CG_RATES.equityLTCG * 100,
            tax,
            notes: gain > CG_RATES.equityLTCGExemption 
              ? [`₹${CG_RATES.equityLTCGExemption.toLocaleString()} exemption applied`] 
              : ['Fully exempt under ₹1.25L limit'],
          });
          totalLTCG += tax;
        } else {
          // Section 111A
          const tax = gain * CG_RATES.equitySTCG;
          breakdown.push({
            assetType: tx.assetType,
            gainType: 'STCG',
            gainAmount: gain,
            taxableAmount: gain,
            taxRate: CG_RATES.equitySTCG * 100,
            tax,
            notes: tx.sttPaid ? ['STT paid — concessional rate applies'] : ['STT not paid — taxed at slab rates'],
          });
          totalSTCG += tax;
        }
        break;
      }

      case 'debtMF': {
        const purchaseDate = new Date(tx.purchaseDate);
        const cutoffDate = new Date('2023-04-01');
        const isPre2023 = purchaseDate < cutoffDate;
        const isLongTerm = holdingMonths > CG_RATES.debtMF_HoldingPeriod;

        if (isPre2023 && isLongTerm) {
          // LTCG at 12.5% (no indexation from 23 July 2024)
          const tax = gain * CG_RATES.debtMF_LTCG_Pre2023;
          breakdown.push({
            assetType: 'debtMF',
            gainType: 'LTCG',
            gainAmount: gain,
            taxableAmount: gain,
            taxRate: 12.5,
            tax,
            notes: ['Purchased before 1 Apr 2023', 'No indexation benefit'],
          });
          totalLTCG += tax;
        } else {
          // STCG at slab rates
          breakdown.push({
            assetType: 'debtMF',
            gainType: 'STCG',
            gainAmount: gain,
            taxableAmount: gain,
            taxRate: 0, // Will be calculated based on slab
            tax: 0, // Added to normal income, taxed at slab
            notes: isPre2023 
              ? ['Purchased before 1 Apr 2023 but held ≤24 months'] 
              : ['Purchased on/after 1 Apr 2023 — all gains taxed as STCG at slab rates'],
          });
          // Note: Debt MF STCG is added to normal income, not taxed separately
        }
        break;
      }

      case 'crypto': {
        // Section 115BBH — flat 30% regardless of holding period
        const tax = Math.max(0, gain) * CG_RATES.crypto;
        breakdown.push({
          assetType: 'crypto',
          gainType: gain > 0 ? 'STCG' : 'LTCG', // Display only — same rate
          gainAmount: gain,
          taxableAmount: Math.max(0, gain),
          taxRate: 30,
          tax,
          notes: gain < 0 
            ? ['Crypto losses cannot be set off against any income'] 
            : ['Flat 30% tax on VDA gains', 'No expense deduction except cost of acquisition'],
        });
        totalCryptoTax += tax;
        break;
      }

      case 'property': {
        const isLongTerm = holdingMonths > CG_RATES.propertyHoldingPeriod;
        if (isLongTerm) {
          // Section 112 — 12.5% LTCG (no indexation from 23 July 2024)
          let taxableGain = gain;
          // Section 54/54F exemption
          if (tx.reinvestment54F > 0) {
            taxableGain = Math.max(0, gain - tx.reinvestment54F);
          }
          const tax = taxableGain * CG_RATES.propertyLTCG;
          breakdown.push({
            assetType: 'property',
            gainType: 'LTCG',
            gainAmount: gain,
            taxableAmount: taxableGain,
            taxRate: 12.5,
            tax,
            notes: [
              'No indexation benefit (post 23 July 2024)',
              tx.reinvestment54F > 0 ? `₹${tx.reinvestment54F.toLocaleString()} exempt u/s 54/54F` : '',
            ].filter(Boolean),
          });
          totalPropertyTax += tax;
        } else {
          // STCG at slab rates
          breakdown.push({
            assetType: 'property',
            gainType: 'STCG',
            gainAmount: gain,
            taxableAmount: gain,
            taxRate: 0, // Slab rate
            tax: 0,
            notes: ['Taxed at your applicable income tax slab rate'],
          });
        }
        break;
      }
    }
  }

  // Calculate surcharge on CG (capped at 15%)
  const totalCGTax = totalSTCG + totalLTCG + totalCryptoTax + totalPropertyTax;
  // Surcharge rate determined by total income, but capped at 15% for CG

  return {
    transactions,
    stcgTax: totalSTCG,
    ltcgTax: totalLTCG,
    cryptoTax: totalCryptoTax,
    propertyTax: totalPropertyTax,
    totalCapitalGainsTax: totalCGTax,
    surchargeOnCG: 0, // Calculated separately based on total income
    cessOnCG: 0,
    totalCGTaxWithCess: 0,
    breakdown,
  };
}
```

### 4.3 Capital Gains Tax Rate Reference Table

| Asset | Holding Period (STCG) | Holding Period (LTCG) | STCG Rate | LTCG Rate | Notes |
|-------|----------------------|----------------------|-----------|-----------|-------|
| Listed Equity Shares (STT paid) | ≤12 months | >12 months | 20% (Sec 111A) | 12.5% (Sec 112A) | ₹1.25L exemption on LTCG. No indexation. |
| Equity Mutual Funds (STT paid) | ≤12 months | >12 months | 20% (Sec 111A) | 12.5% (Sec 112A) | Same as above |
| Debt Mutual Funds (bought before 1 Apr 2023) | ≤24 months | >24 months | Slab rate | 12.5% | No indexation. Bought on/after 1 Apr 2023 → ALL gains at slab |
| Cryptocurrency / VDA | Any | Any | 30% (Sec 115BBH) | 30% (Sec 115BBH) | No loss set-off. Only COA deductible. No slab benefit. |
| Real Estate Property | ≤24 months | >24 months | Slab rate | 12.5% (Sec 112) | No indexation. 54/54F exemptions available. |

### 4.4 Special Rules

**Equity LTCG (Section 112A):**
- Exemption: First ₹1,25,000 of LTCG is tax-free [^34^]
- Only gains above ₹1.25L taxed at 12.5%
- Rebate u/s 87A NOT available for 112A gains [^32^]
- Grandfathering clause: For shares bought before 31 Jan 2018, COA = higher of (actual price, FMV on 31 Jan 2018)

**Crypto (Section 115BBH):**
- Flat 30% on ALL gains, no distinction between STCG/LTCG [^29^][^30^]
- Losses CANNOT be set off against any income, including other crypto gains [^30^]
- Only cost of acquisition deductible (no expenses, no fees)
- 1% TDS on sale consideration > ₹10,000
- Surcharge capped at 15% on crypto gains

**Debt Mutual Funds:**
- Purchased on/after 1 Apr 2023: ALL gains treated as STCG at slab rates regardless of holding period [^27^]
- Purchased before 1 Apr 2023: LTCG at 12.5% if held >24 months, else slab rate
- No indexation benefit

**Property:**
- LTCG: 12.5% without indexation (from 23 July 2024) [^34^]
- Section 54: Exemption if LTCG reinvested in another residential property
- Section 54F: Exemption if sale proceeds reinvested in residential property (non-residential asset sold)

### 4.5 Integration with Main Tax Engine

The capital gains tax is calculated SEPARATELY from normal income tax, then combined:

```typescript
function calculateTotalTax(data: FullTaxData): TotalTaxResult {
  // 1. Calculate normal income tax (salary/business)
  const oldRegimeResult = calculateOldRegime(data);
  const newRegimeResult = calculateNewRegime(data);

  // 2. Calculate capital gains tax
  const cgResult = calculateCapitalGainsTax(data.capitalGainsTransactions);

  // 3. Calculate surcharge on combined income
  const totalIncomeOld = oldRegimeResult.taxableIncome + cgResult.totalCapitalGainsTax;
  const totalIncomeNew = newRegimeResult.taxableIncome + cgResult.totalCapitalGainsTax;

  // 4. Surcharge calculation with CG cap
  const oldSurcharge = calculateSurcharge({
    taxableIncome: totalIncomeOld,
    regime: 'old',
    capitalGains111A: getSTCGEquity(data),
    capitalGains112: getLTCGProperty(data),
    capitalGains112A: getLTCGEquity(data),
    dividendIncome: data.dividendIncome || 0,
  });

  // 5. Cess on everything
  const oldCess = CESS_RATE * (oldRegimeResult.taxAfterRebate + oldSurcharge.netSurcharge + cgResult.totalCapitalGainsTax);

  // 6. Grand total
  const oldTotal = oldRegimeResult.taxAfterRebate + oldSurcharge.netSurcharge + cgResult.totalCapitalGainsTax + oldCess;

  return {
    incomeTax: oldRegimeResult.taxAfterRebate,
    surchargeOnIncome: oldSurcharge.netSurcharge,
    cessOnIncome: oldCess,
    capitalGainsTax: cgResult.totalCapitalGainsTax,
    surchargeOnCG: 0, // Included in combined surcharge
    cessOnCG: 0, // Included in combined cess
    grandTotal: oldTotal,
    effectiveTaxRate: (oldTotal / data.grossTotalIncome) * 100,
  };
}
```

### 4.6 Deliverables
- [ ] Capital gains transaction input form
- [ ] Transaction list with edit/delete
- [ ] Capital gains calculation engine for all 5 asset types
- [ ] STCG/LTCG auto-classification based on dates
- [ ] Special handling for debt MF purchase date cutoff (1 Apr 2023)
- [ ] Crypto flat 30% with no loss set-off
- [ ] Property 54/54F exemption fields
- [ ] Integration with surcharge engine (15% cap on CG)
- [ ] Unit tests for each asset type and edge case

---

## PHASE 5: PDF Export

### 5.1 PDF Generation Strategy

**Library Choice:** `html2pdf.js` or `jspdf` + `html2canvas`

**Recommendation:** Use `html2pdf.js` (wrapper around `html2canvas` + `jspdf`) because:
- Converts React components directly to PDF
- Maintains styling from CSS
- Pure client-side, no server needed
- Good support for tables and charts

**Alternative:** `pdfmake` for more control over layout, but requires rebuilding the layout in JSON.

**Installation:**
```bash
npm install html2pdf.js
```

### 5.2 PDF Content Structure

**Page 1: Cover**
- App logo + "TaxCalc India Tax Summary"
- FY 2025-26 | Assessment Year 2026-27
- Generated on: [Date]
- Disclaimer: "For informational purposes only. Not tax advice."

**Page 2: Executive Summary**
- Recommended Regime: [Old / New]
- Total Tax Savings: ₹X
- Total Tax Liability: ₹Y
- Effective Tax Rate: Z%
- Income Type: [Salaried / Freelancer / Business / Consulting]

**Page 3: Regime Comparison Table**
- Full side-by-side comparison (same as result page table)
- All line items: Gross, exemptions, deductions, taxable income, tax, rebate, surcharge, cess, total

**Page 4: Slab Breakdown**
- Old Regime slab table with amounts
- New Regime slab table with amounts
- Visual bar chart (rendered as image via html2canvas)

**Page 5: Capital Gains Summary (if applicable)**
- Transaction-wise breakdown table
- Asset type, gain type, gain amount, tax rate, tax payable
- Total capital gains tax

**Page 6: Surcharge Details (if applicable)**
- Surcharge rate applied
- Marginal relief calculation
- Net surcharge amount

**Page 7: Personalized Insights**
- All suggestion cards
- Educational explanations

**Page 8: Input Summary**
- All user inputs organized by category
- "This is what you told us" section for record-keeping

### 5.3 PDF Component

**File:** `src/components/PDFExport/TaxSummaryPDF.tsx`

```typescript
import html2pdf from 'html2pdf.js';

export function generateTaxPDF(result: TaxResult, inputs: TaxpayerData) {
  const element = document.getElementById('tax-summary-pdf-content');

  const opt = {
    margin: [15, 15, 15, 15], // top, left, bottom, right (mm)
    filename: `TaxCalc_India_FY2025-26_${new Date().toISOString().split('T')[0]}.pdf`,
    image: { type: 'jpeg', quality: 0.98 },
    html2canvas: { 
      scale: 2, 
      useCORS: true,
      logging: false,
    },
    jsPDF: { 
      unit: 'mm', 
      format: 'a4', 
      orientation: 'portrait',
      compress: true,
    },
    pagebreak: { mode: ['css', 'legacy'] },
  };

  return html2pdf().set(opt).from(element).save();
}
```

### 5.4 Hidden PDF Template

**File:** `src/components/PDFExport/PDFTemplate.tsx`

Create a hidden div that contains all the PDF content with print-friendly styling:
- Use `@media print` styles
- Ensure charts are rendered as images before PDF generation
- Hide interactive elements (buttons, toggles)
- Show all collapsed sections expanded

```tsx
<div id="tax-summary-pdf-content" className="hidden-pdf-template">
  {/* All PDF content here */}
</div>

<style>{`
  .hidden-pdf-template {
    position: absolute;
    left: -9999px;
    width: 210mm;
    background: white;
    padding: 20mm;
  }
  @media print {
    .hidden-pdf-template { position: static; left: auto; }
  }
`}</style>
```

### 5.5 PDF Trigger Button

**Location:** Result page, top-right corner

```tsx
<button 
  onClick={() => generateTaxPDF(result, inputs)}
  className="pdf-export-btn"
  disabled={isGenerating}
>
  {isGenerating ? 'Generating PDF...' : '📄 Download Tax Summary'}
</button>
```

### 5.6 PDF Styling Requirements

- Font: System fonts or embed Inter via base64
- Colors: Grayscale-friendly (primary blue → dark gray)
- Tables: Clear borders, alternating row backgrounds
- Page breaks: Avoid breaking tables across pages
- Header/Footer: Page numbers, generation date
- Charts: Convert Recharts to images using `html2canvas` before PDF generation

### 5.7 Deliverables
- [ ] PDF template component with all 8 pages
- [ ] PDF generation utility function
- [ ] Download button on result page
- [ ] Print-friendly CSS
- [ ] Chart-to-image conversion before PDF generation
- [ ] Filename: `TaxCalc_India_FY2025-26_YYYY-MM-DD.pdf`
- [ ] Test on Chrome, Safari, Firefox

---

## PHASE 6: UI/UX Updates

### 6.1 Landing Page Updates

- Update headline: "Find Out Your Total Tax Liability — Income, Business & Capital Gains"
- Add income type icons to preview section
- Mention: "Now with capital gains, surcharge, and PDF export"

### 6.2 Wizard Updates

**Progress Indicator:**
- For salaried: 11 steps (10 + capital gains)
- For freelancer/consulting: 7 steps (6 + capital gains)
- For business: 7 steps (6 + capital gains)
- Capital gains step is optional (skip allowed)

**Live Preview Panel Updates:**
- Add "Capital Gains Tax" line item
- Add "Surcharge" line item (if applicable)
- Show "Grand Total" instead of just "Total Tax"
- Add income-type-specific labels

### 6.3 Result Page Updates

**New Sections:**

1. **Grand Total Tax Card**
   ```
   ┌─────────────────────────────────────────┐
   │  TOTAL TAX LIABILITY                    │
   │  ₹X,XX,XXX                              │
   │                                         │
   │  Income Tax:          ₹X                │
   │  Capital Gains Tax:   ₹Y                │
   │  Surcharge:           ₹Z                │
   │  Cess (4%):           ₹W                │
   │  ─────────────────────────────────────  │
   │  GRAND TOTAL          ₹T                │
   └─────────────────────────────────────────┘
   ```

2. **Surcharge Detail Card** (only if applicable)
   - Show: "Surcharge @X% on income tax"
   - Show: "Marginal relief: ₹Y"
   - Show: "Net surcharge: ₹Z"
   - Explanation tooltip: "Surcharge is an additional tax on high incomes. Marginal relief ensures you don't pay more tax than the extra income you earned."

3. **Capital Gains Breakdown Card** (only if applicable)
   - Table of all transactions
   - Summary: "You owe ₹X in capital gains tax"
   - Note for crypto: "Crypto losses cannot offset other gains"

4. **Regime Comparison Update**
   - Include capital gains in comparison
   - Note: "Capital gains tax is the same in both regimes for most assets"
   - Exception: "Debt MF STCG and property STCG may differ based on slab rates"

### 6.4 New Components

**File:** `src/components/Result/SurchargeCard.tsx`
**File:** `src/components/Result/CapitalGainsCard.tsx`
**File:** `src/components/Result/GrandTotalCard.tsx`
**File:** `src/components/Wizard/Steps/IncomeTypeStep.tsx`
**File:** `src/components/Wizard/Steps/CapitalGainsStep.tsx`
**File:** `src/components/Wizard/Flows/FreelancerFlow.tsx`
**File:** `src/components/Wizard/Flows/BusinessFlow.tsx`

### 6.5 Responsive Considerations

- Capital gains transaction table: horizontal scroll on mobile
- PDF export button: sticky bottom on mobile
- Surcharge details: collapsible card on mobile
- Multi-income type: single-column card selection on mobile

### 6.6 Deliverables
- [ ] Updated landing page
- [ ] Income type selection step
- [ ] Capital gains input step
- [ ] Updated live preview with CG and surcharge
- [ ] Surcharge detail card on results
- [ ] Capital gains breakdown card on results
- [ ] Grand total card on results
- [ ] PDF export button and flow
- [ ] Mobile-responsive updates

---

## PHASE 7: Testing & Validation

### 7.1 Test Cases for Surcharge

| Income (Old) | CG (111A/112/112A) | Expected Surcharge | Marginal Relief |
|-------------|---------------------|-------------------|-----------------|
| ₹51,00,000 | ₹0 | 10% | ₹64,250 |
| ₹1,01,00,000 | ₹0 | 15% | Yes |
| ₹2,01,00,000 | ₹0 | 25% | Yes |
| ₹5,01,00,000 | ₹0 | 37% | Yes |
| ₹5,01,00,000 | ₹50,00,000 | 37% on normal, 15% on CG | Yes |
| ₹50,00,000 | ₹0 | 0% | No |

### 7.2 Test Cases for Capital Gains

| Asset | Purchase | Sale | Gain | Expected Tax | Notes |
|-------|----------|------|------|-------------|-------|
| Equity Shares | ₹1L, 2 years ago | ₹3L | ₹2L | ₹9,375 | 112A: ₹2L − ₹1.25L = ₹75K × 12.5% |
| Equity Shares | ₹1L, 6 months ago | ₹3L | ₹2L | ₹40,000 | 111A: ₹2L × 20% |
| Debt MF (pre-2023) | ₹1L, 3 years ago | ₹2L | ₹1L | ₹12,500 | LTCG 12.5% |
| Debt MF (post-2023) | ₹1L, 3 years ago | ₹2L | ₹1L | Slab rate | All gains STCG |
| Crypto | ₹1L | ₹3L | ₹2L | ₹60,000 | Flat 30% |
| Crypto loss | ₹3L | ₹1L | −₹2L | ₹0 | Loss cannot offset |
| Property (>24mo) | ₹50L, 5 years ago | ₹1Cr | ₹50L | ₹6.25L | LTCG 12.5% |

### 7.3 Test Cases for Multi-Income Types

| Income Type | Receipts | Digital % | Presumptive | Expected Taxable Income |
|------------|----------|-----------|-------------|------------------------|
| Freelancer | ₹40L | 100% | Yes | ₹20L (50%) |
| Consultant | ₹80L | 90% | No | Actual profit (audit req) |
| Business | ₹2.5Cr | 100% | Yes | ₹15L (6%) |
| Business | ₹2.5Cr | 80% | Yes | ₹20L (8%) |
| Business | ₹3.5Cr | 100% | No | Actual profit (audit req) |

### 7.4 Integration Tests

- [ ] Salaried + Capital gains + Surcharge
- [ ] Freelancer + Capital gains
- [ ] Business + No capital gains + No surcharge
- [ ] Zero tax liability (both regimes)
- [ ] Marginal relief at all 4 thresholds
- [ ] CG surcharge cap at 15%
- [ ] PDF generation with all sections
- [ ] PDF generation with minimal data

### 7.5 Cross-Browser PDF Tests

| Browser | PDF Generation | Charts | Tables |
|---------|---------------|--------|--------|
| Chrome | ✓ | ✓ | ✓ |
| Firefox | ✓ | ✓ | ✓ |
| Safari | ✓ | ✓ | ✓ |
| Edge | ✓ | ✓ | ✓ |
| Mobile Chrome | ✓ | ✓ | ✓ |
| Mobile Safari | ✓ | ✓ | ✓ |

### 7.6 Performance Tests

- [ ] PDF generation < 3 seconds for full report
- [ ] Tax calculation < 100ms
- [ ] Bundle size increase < 150KB (html2pdf.js)

---

## PHASE 8: Final Integration & Polish

### 8.1 State Migration

If existing users have in-progress calculations:
- Default `incomeType` to `'salaried'` for backward compatibility
- Initialize new fields with defaults
- Show "New features available" banner

### 8.2 Content Updates

**Updated Disclaimers:**
- "This calculator now includes surcharge for incomes above ₹50 lakh."
- "Capital gains tax is calculated separately and added to your income tax."
- "Business and freelancer calculations use presumptive taxation (Sections 44AD/44ADA)."
- "For complex business situations, consult a Chartered Accountant."

**New FAQ Entries:**
- Q: "Why is my surcharge different in old vs new regime?"
  A: "The old regime has a 37% surcharge above ₹5 crore, while the new regime caps it at 25%."

- Q: "Can I offset my crypto losses against stock gains?"
  A: "No. Crypto/VDA losses cannot be set off against any income, including other crypto gains or stock gains."

- Q: "I'm a freelancer. Should I use 44ADA or actual income?"
  A: "If your actual expenses are less than 50% of receipts, 44ADA is simpler and usually better. If your expenses are higher, actual income may save tax but requires bookkeeping and audit."

### 8.3 Analytics Events (Privacy-Preserving)

New events to track:
- `income_type_selected` (type)
- `capital_gains_added` (asset_type, count)
- `surcharge_applied` (rate)
- `pdf_exported`
- `presumptive_scheme_used` (44AD/44ADA)

### 8.4 Accessibility

- [ ] Capital gains table: screen-reader friendly with aria-labels
- [ ] PDF export button: keyboard accessible
- [ ] Income type cards: focusable and selectable via keyboard
- [ ] Surcharge info: aria-live region for dynamic updates

### 8.5 Deliverables
- [ ] Backward compatibility for existing users
- [ ] Updated disclaimers and FAQs
- [ ] Analytics events
- [ ] Accessibility audit
- [ ] Final end-to-end testing
- [ ] Deployment checklist

---

## Appendix A: Complete Tax Rule Reference (Updated)

### A.1 Surcharge Rates FY 2025-26

| Income Range | Old Regime | New Regime |
|-------------|------------|------------|
| ≤ ₹50L | Nil | Nil |
| ₹50L – ₹1Cr | 10% | 10% |
| ₹1Cr – ₹2Cr | 15% | 15% |
| ₹2Cr – ₹5Cr | 25% | 25% |
| > ₹5Cr | 37% | 25% |

**Special Cap:** Surcharge on capital gains u/s 111A, 112, 112A and dividend income is capped at 15% regardless of total income. [^22^]

### A.2 Capital Gains Summary

| Asset | STCG Holding | LTCG Holding | STCG Rate | LTCG Rate | Key Rules |
|-------|-------------|-------------|-----------|-----------|-----------|
| Equity (STT paid) | ≤12 mo | >12 mo | 20% (111A) | 12.5% (112A) | ₹1.25L exemption. No 87A rebate. |
| Equity MF (STT paid) | ≤12 mo | >12 mo | 20% (111A) | 12.5% (112A) | Same as above |
| Debt MF (pre-1Apr23) | ≤24 mo | >24 mo | Slab | 12.5% | No indexation |
| Debt MF (post-1Apr23) | Any | N/A | Slab | N/A | All gains = STCG |
| Crypto/VDA | Any | Any | 30% | 30% | No loss set-off. Only COA. |
| Property | ≤24 mo | >24 mo | Slab | 12.5% | No indexation. 54/54F available. |

### A.3 Presumptive Taxation Summary

| Section | Who | Limit (Normal) | Limit (95% Digital) | Rate | Digital Rate |
|---------|-----|---------------|---------------------|------|-------------|
| 44ADA | Specified Professionals | ₹50L | ₹75L | 50% | 50% |
| 44AD | Resident Businesses | ₹2Cr | ₹3Cr | 8% | 6% |

**Specified Professionals for 44ADA:** Legal, medical, engineering, architectural, accountancy, technical consultancy, interior decoration, film artists, company secretaries, information technology. [^23^]

### A.4 Marginal Relief Formula

```
Relief = (Tax at current income + Surcharge + Cess) 
         − (Tax at threshold + Income excess over threshold)

Net Surcharge = Calculated Surcharge − Relief (if positive)
```

Applied at: ₹50L, ₹1Cr, ₹2Cr, ₹5Cr thresholds.

---

## Appendix B: File Structure (Post-Implementation)

```
src/
├── components/
│   ├── Wizard/
│   │   ├── Steps/
│   │   │   ├── Step0_IncomeType.tsx        [NEW]
│   │   │   ├── Step1_Age.tsx
│   │   │   ├── Step2_Salary.tsx
│   │   │   ├── ...existing steps...
│   │   │   ├── CapitalGainsStep.tsx        [NEW]
│   │   │   └── ReviewStep.tsx
│   │   ├── Flows/
│   │   │   ├── SalariedFlow.tsx
│   │   │   ├── FreelancerFlow.tsx          [NEW]
│   │   │   └── BusinessFlow.tsx            [NEW]
│   │   ├── ProgressIndicator.tsx
│   │   └── WizardContainer.tsx
│   ├── Result/
│   │   ├── RegimeRecommendation.tsx
│   │   ├── SideBySideComparison.tsx
│   │   ├── SlabBreakdownChart.tsx
│   │   ├── SurchargeCard.tsx               [NEW]
│   │   ├── CapitalGainsCard.tsx            [NEW]
│   │   ├── GrandTotalCard.tsx              [NEW]
│   │   ├── PersonalizedInsights.tsx
│   │   └── PracticalSuggestions.tsx
│   ├── PDFExport/
│   │   ├── PDFTemplate.tsx                 [NEW]
│   │   └── generateTaxPDF.ts               [NEW]
│   └── LandingPage/
├── lib/
│   ├── calculations/
│   │   ├── taxEngine.ts                    [MODIFIED]
│   │   ├── surcharge.ts                    [NEW]
│   │   ├── capitalGains.ts                 [NEW]
│   │   ├── presumptiveTax.ts               [NEW]
│   │   └── slabCalculator.ts
│   └── utils/
├── constants/
│   └── taxRules.ts                         [MODIFIED]
├── types/
│   └── tax.ts                              [MODIFIED]
├── store/
│   └── taxStore.ts                         [MODIFIED]
└── App.tsx
```

---

## Appendix C: Execution Order for AI Coding Model

**Recommended Order:**
1. Implement Phase 1 (types, constants, state, flow config)
2. Implement Phase 2 (surcharge engine + tests)
3. Implement Phase 4 (capital gains engine + tests) — can be done in parallel with Phase 2
4. Implement Phase 3 (multi-income type wizards)
5. Implement Phase 5 (PDF export)
6. Implement Phase 6 (UI components)
7. Run Phase 7 (all tests)
8. Phase 8 (polish, integration, deployment)

**Parallel Workstreams:**
- Phase 2 and Phase 4 are independent — can be developed in parallel
- Phase 5 (PDF) depends on Phase 6 (UI) for the template
- Phase 3 depends on Phase 1

**Testing Strategy:**
- Write unit tests BEFORE implementing each phase (TDD approach)
- Each phase must pass all tests before proceeding
- Integration tests after Phase 6

---

*End of Execution Plan*
