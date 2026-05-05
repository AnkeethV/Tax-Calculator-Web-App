# TaxCalc India — Product Requirements Document
## FY 2025-26 (AY 2026-27) | Old vs New Tax Regime Comparison Calculator

**Version:** 1.0  
**Date:** May 2026  
**Status:** Complete PRD for Full-Stack Development  
**Scope:** Salaried individuals only. No surcharge, no capital gains, no freelance income.

---

## 1. Executive Summary

TaxCalc India is a privacy-first, browser-based tax calculator that helps salaried individuals determine which tax regime (Old vs New) saves them more money for FY 2025-26. Unlike existing calculators that demand CTC or gross salary figures most people don't track, TaxCalc starts from the one number everyone knows: **monthly in-hand salary** (what lands in the bank).

The app uses a step-by-step conversational wizard, asking one plain-language question at a time. A live preview panel updates in real-time on the right side, showing a detailed tax breakdown, slab-wise comparison, and projected savings. The result is a clear, human-readable recommendation: *"Pick the New Regime. You save ₹X."*

---

## 2. Product Vision & Goals

### Primary Goal
Eliminate decision paralysis for ~5 crore salaried taxpayers who must choose between Old and New regimes every financial year.

### Success Metrics
- User completes wizard in under 3 minutes
- Tax math accuracy: 100% for all covered scenarios
- Zero external data transmission (pure client-side computation)
- Result clarity: user can explain their tax situation to a friend after viewing results

### Non-Goals (Explicitly Out of Scope)
- Surcharge calculation (income > ₹50 lakh)
- Capital gains taxation
- Freelance / business income
- Agricultural income
- Tax filing / ITR generation
- PDF report download
- User accounts or data persistence

---

## 3. Target User Personas

### Persona A: First Job Fresher (22 years)
- Knows monthly in-hand: ₹45,000
- Has no idea about CTC, gross salary, or 80C
- Pays rent, has health insurance via employer
- Needs: simplicity, zero jargon, reassurance

### Persona B: Mid-Level Professional (35 years)
- Monthly in-hand: ₹1,20,000
- Has home loan, invests in ELSS, pays health insurance for family
- Knows some tax terms but confused about regime choice
- Needs: accurate comparison, actionable suggestions

### Persona C: Senior Citizen (65 years)
- Monthly pension/in-hand: ₹60,000
- Has FD interest, senior citizen health insurance
- Needs: age-appropriate slabs, clear 80TTB benefit explanation

---

## 4. Core Value Proposition

1. **Starts from in-hand salary** — no need to know CTC or gross
2. **Plain-language questions** — "How much rent do you pay?" not "Enter 80GG deduction"
3. **Real-time live preview** — detailed tax computation updates as you type
4. **Contextual FAQ** — every step has a collapsible FAQ for common doubts
5. **Clear recommendation** — one definitive answer with exact savings amount
6. **Educational results** — explains *why* in personalized plain language
7. **Privacy-first** — nothing leaves the browser

---

## 5. Design Principles

- **Clean & Minimal:** White backgrounds, ample whitespace, single-column wizard with right-side preview on desktop
- **Trustworthy:** Subtle shadows, rounded corners, calming blue/green accent colors, no flashing ads
- **Progressive Disclosure:** One question at a time. No overwhelming forms.
- **Mobile-First Responsive:** Works flawlessly on phones; preview panel becomes bottom sheet or toggle on mobile
- **Accessibility:** WCAG 2.1 AA compliant, keyboard navigable, screen-reader friendly
- **Typography:** Inter or system-ui font family. 16px base. Clear hierarchy.

### Color Palette
- Primary: `#2563EB` (Trust Blue)
- Success/Old Regime: `#059669` (Emerald)
- Info/New Regime: `#7C3AED` (Violet)
- Background: `#FAFAFA` (Off-white)
- Surface: `#FFFFFF` (White)
- Text Primary: `#111827` (Gray-900)
- Text Secondary: `#6B7280` (Gray-500)
- Border: `#E5E7EB` (Gray-200)
- Savings Highlight: `#D1FAE5` (Green-100) with `#065F46` text

---

## 6. Privacy & Security

- **100% Client-Side:** All calculations happen in the browser via JavaScript/TypeScript
- **No Backend:** No API calls, no database, no user tracking
- **No Cookies:** Except essential session preference (regime choice display)
- **LocalStorage (Optional):** If implemented, store only current wizard progress. Clear on session end. No PII.
- **No Third-Party Scripts:** No Google Analytics, no Facebook Pixel, no ad scripts
- **HTTPS Only:** Enforced via CSP headers

---

## 7. Tax Rules Reference — FY 2025-26 (AY 2026-27)

### 7.1 New Tax Regime (Default Regime — Section 115BAC)

**Income Tax Slabs (Same for all ages):**

| Income Range (₹) | Tax Rate |
|------------------|----------|
| Up to 4,00,000   | Nil      |
| 4,00,001 – 8,00,000 | 5%    |
| 8,00,001 – 12,00,000 | 10%  |
| 12,00,001 – 16,00,000 | 15% |
| 16,00,001 – 20,00,000 | 20% |
| 20,00,001 – 24,00,000 | 25% |
| Above 24,00,000  | 30%      |

**Key Features:**
- Basic Exemption Limit: ₹4,00,000
- Standard Deduction: ₹75,000 (salaried employees & pensioners)
- Rebate u/s 87A: Up to ₹60,000 (taxable income ≤ ₹12,00,000 → zero tax)
- Marginal Relief: Available for income slightly above ₹12 lakh. Tax payable = min(calculated tax, income excess over ₹12 lakh)
- **Deductions NOT Allowed:** 80C, 80D, HRA, LTA, 80TTA, 80TTB, home loan interest (self-occupied), professional tax
- **Deductions Allowed:** Employer NPS contribution u/s 80CCD(2) up to 14% of salary (Basic + DA)
- Health & Education Cess: 4% on tax liability
- Surcharge: Out of scope for this app

**Effective Zero-Tax Income:** Up to ₹12,75,000 (₹12,00,000 + ₹75,000 standard deduction)

### 7.2 Old Tax Regime (Optional)

**Income Tax Slabs — Below 60 years:**

| Income Range (₹) | Tax Rate |
|------------------|----------|
| Up to 2,50,000   | Nil      |
| 2,50,001 – 5,00,000 | 5%    |
| 5,00,001 – 10,00,000 | 20%  |
| Above 10,00,000  | 30%      |

**Senior Citizens (60–80 years):**

| Income Range (₹) | Tax Rate |
|------------------|----------|
| Up to 3,00,000   | Nil      |
| 3,00,001 – 5,00,000 | 5%    |
| 5,00,001 – 10,00,000 | 20%  |
| Above 10,00,000  | 30%      |

**Super Senior Citizens (80+ years):**

| Income Range (₹) | Tax Rate |
|------------------|----------|
| Up to 5,00,000   | Nil      |
| 5,00,001 – 10,00,000 | 20%  |
| Above 10,00,000  | 30%      |

**Key Features:**
- Standard Deduction: ₹50,000
- Rebate u/s 87A: Up to ₹12,500 (taxable income ≤ ₹5,00,000 → zero tax)
- Health & Education Cess: 4% on tax liability
- Surcharge: Out of scope

### 7.3 Allowable Deductions & Exemptions (Old Regime Only)

| Deduction | Section | Limit | Details |
|-----------|---------|-------|---------|
| 80C (Combined) | 80C, 80CCC, 80CCD(1) | ₹1,50,000 | ELSS, PPF, EPF/VPF, LIC, NSC, SSY, SCSS, tuition fees, home loan principal, 5-year FD, NPS employee contribution (within 80CCE limit) |
| NPS Additional | 80CCD(1B) | ₹50,000 | Over and above 80C limit. Employee contribution to NPS. |
| Employer NPS | 80CCD(2) | 10% of (Basic+DA) | Employer contribution to NPS. |
| Health Insurance | 80D | Self+Family: ₹25,000 (₹50,000 if senior citizen); Parents: additional ₹25,000 (₹50,000 if senior citizen) | Includes ₹5,000 preventive health checkup within limit. |
| Home Loan Interest | 24(b) | ₹2,00,000 | Self-occupied property. Let-out: no limit (loss set-off restricted). |
| Additional Home Loan (First-time) | 80EE | ₹50,000 | Loan sanctioned 01.04.2016–31.03.2017; Property value ≤ ₹50L; Loan ≤ ₹35L. First-time buyer. |
| Additional Home Loan (Affordable) | 80EEA | ₹1,50,000 | Loan sanctioned 01.04.2019–31.03.2022; Stamp duty value ≤ ₹45L. Not eligible if claiming 80EE. |
| Savings Interest | 80TTA | ₹10,000 | Individuals & HUF below 60 years. Savings accounts only. |
| Senior Citizen Interest | 80TTB | ₹50,000 | Resident senior citizens (60+). Covers savings + FD + RD + post office interest. |
| HRA Exemption | 10(13A) | Least of three | See formula below. |
| LTA Exemption | 10(5) | Actual travel cost | 2 journeys in block of 4 years (current block: 2022–2025). Domestic travel only. |
| Professional Tax | 16(iii) | Actual paid (max ₹2,500/year) | Deducted from gross salary. |
| Children Education Allowance | 10(14) | ₹100/month/child (max 2) | For FY 2025-26. Budget 2026 changes apply from FY 2026-27. |
| Hostel Allowance | 10(14) | ₹300/month/child (max 2) | For FY 2025-26. Budget 2026 changes apply from FY 2026-27. |

### 7.4 HRA Exemption Formula (Old Regime)

Exempt amount = **Least of:**
1. Actual HRA received from employer
2. 50% of [Basic + DA] for metro cities OR 40% of [Basic + DA] for non-metro cities
3. Rent paid – 10% of [Basic + DA]

**Metro Cities (8 cities):** Delhi, Mumbai, Chennai, Kolkata, Bengaluru, Hyderabad, Pune, Ahmedabad

### 7.5 Marginal Relief (New Regime)

If taxable income > ₹12,00,000:
- Calculate tax on full income (T1)
- Calculate excess over ₹12 lakh (E = Income – 12,00,000)
- If T1 > E, then tax payable = E (after rebate adjustment)
- Rebate u/s 87A = T1 – E
- This relief applies up to approximately ₹12,75,000 income

**Example:** Income = ₹12,15,000
- Tax on ₹12,15,000 = ₹62,250
- Excess over ₹12L = ₹15,000
- Since ₹62,250 > ₹15,000, rebate = ₹62,250 – ₹15,000 = ₹47,250
- Tax payable = ₹15,000 + cess

### 7.6 Age Classification

| Category | Age |
|----------|-----|
| Individual (General) | Below 60 years |
| Senior Citizen | 60 to 80 years |
| Super Senior Citizen | Above 80 years |

Age is determined as of any date in the relevant financial year (i.e., turned 60 on or before 31st March 2026 for FY 2025-26).

---

## 8. App Architecture & Tech Stack

### Recommended Stack
- **Framework:** React 18+ (Next.js 14+ App Router recommended for SEO)
- **Language:** TypeScript (strict mode)
- **State Management:** Zustand or React Context + useReducer
- **Styling:** Tailwind CSS
- **UI Components:** shadcn/ui or Radix UI primitives
- **Animation:** Framer Motion (for step transitions)
- **Charts:** Recharts (for slab visualization)
- **Icons:** Lucide React
- **Build Output:** Static export (fully client-side, deployable to Vercel/Netlify/Cloudflare Pages)

### Component Architecture
```
App
├── LandingPage
├── WizardContainer
│   ├── ProgressIndicator
│   ├── StepRenderer (dynamic step component)
│   ├── LivePreviewPanel
│   └── StepFAQ (collapsible)
├── ResultPage
│   ├── RegimeRecommendation
│   ├── SideBySideComparison
│   ├── SlabBreakdownChart
│   ├── PersonalizedInsights
│   └── PracticalSuggestions
└── SharedComponents
    ├── CurrencyInput
    ├── RadioGroup
    ├── ToggleSwitch
    └── InfoTooltip
```

---

## 9. Screen-by-Screen Flow

### Flow Diagram

```
[LANDING PAGE]
    ↓ (Click "Find Out Now")
[STEP 0: Age & Basic Info]
    ↓
[STEP 1: Monthly In-Hand Salary]
    ↓
[STEP 2: Do You Know Your Gross Salary?]
    ├─ YES → [STEP 2A: Enter Gross Components]
    └─ NO  → [STEP 2B: Estimate From Payslip]
    ↓
[STEP 3: Rent & HRA]
    ↓
[STEP 4: Investments (80C)]
    ↓
[STEP 5: Health Insurance (80D)]
    ↓
[STEP 6: Home Loan]
    ↓
[STEP 7: NPS Contributions]
    ↓
[STEP 8: Other Income & Deductions]
    ↓
[STEP 9: Review & Calculate]
    ↓
[RESULTS PAGE]
```

---

## 10. Detailed Screen Specifications

---

### SCREEN: Landing Page

**URL:** `/`
**Purpose:** Convert visitors into users. Establish trust. Preview the result.

#### Layout
- Full viewport height (100vh) on desktop, scrollable on mobile
- Two-column layout on desktop (60/40 split), single column on mobile

#### Left Column (60%)
1. **Nav Bar:** Logo "TaxCalc India" + "FY 2025-26" badge + "Privacy First" badge
2. **Hero Headline:** "Find Out Which Tax Regime Saves You More Money"
   - Subheadline: "Answer 8 simple questions. Get a clear recommendation. Zero jargon."
3. **Trust Signals Row:**
   - "✓ 100% Accurate Tax Math"
   - "✓ Nothing Leaves Your Browser"
   - "✓ Based on Latest Budget 2025 Rules"
4. **Preview Card (Result Mockup):**
   - A stylized card showing what the result looks like:
     - "Pick the New Regime"
     - "You save ₹47,820 per year"
     - Mini bar chart: Old vs New
   - Label: "Your result will look like this →"
5. **CTA Button:** "Start Free Calculation" (large, primary color, pulsing subtle shadow)
6. **Secondary Text:** "Takes 2 minutes • No signup • No spam"

#### Right Column (40%)
- Abstract illustration or animated SVG showing:
  - Person looking at two paths (Old vs New)
  - Money symbols flowing toward the cheaper path
  - Clean, minimal, no clutter

#### Footer (Full Width)
- "For FY 2025-26 (AY 2026-27) • Salaried Individuals Only"
- "Not tax advice. Consult a CA for complex situations."

#### Interactions
- CTA scrolls smoothly to wizard or navigates to `/calculate`
- All animations are CSS-based, no heavy JS

---

### SCREEN: Wizard Container

**URL:** `/calculate`
**Purpose:** Host the step-by-step wizard with live preview.

#### Layout (Desktop)
```
┌─────────────────────────────────────────┬─────────────────────┐
│  Progress Dots                          │                     │
│  ● ● ○ ○ ○ ○ ○ ○ ○                     │   LIVE PREVIEW      │
│                                         │   PANEL             │
│  ┌─────────────────────────────────┐   │   (Sticky)          │
│  │                                 │   │                     │
│  │   CURRENT STEP QUESTION         │   │   Tax Estimate      │
│  │                                 │   │   Old: ₹X           │
│  │   [Input Field]                 │   │   New: ₹Y           │
│  │                                 │   │   Difference        │
│  │   [Continue] [Back]             │   │                     │
│  │                                 │   │   Slab Table        │
│  └─────────────────────────────────┘   │   (Updates Live)    │
│                                         │                     │
│  ┌─────────────────────────────────┐   │                     │
│  │  💡 FAQ (Collapsible)           │   │                     │
│  │  Common doubts for this step    │   │                     │
│  └─────────────────────────────────┘   │                     │
└─────────────────────────────────────────┴─────────────────────┘
```

#### Layout (Mobile)
- Progress dots at top
- Question card in center
- **Toggle Button:** "Show Tax Preview" (opens bottom sheet)
- FAQ below question card

#### Progress Indicator
- 10 dots representing 10 steps
- Current step: filled primary color
- Completed steps: filled with checkmark
- Upcoming steps: outlined gray
- Below dots: "Step X of 10 — [Step Name]"
- On mobile: "X of 10" text only, dots hidden if space constrained

#### Navigation Rules
- **Continue:** Validates current step → advances to next
- **Back:** Returns to previous step, preserves input
- **Skip:** Available only for optional steps (marked clearly)
- Keyboard: Enter key submits step; Tab navigates fields

---

### STEP 0: Age & Basic Info

**Question:** "How old are you?"
**Input:** Radio buttons / Segmented control
- "Below 60"
- "60 to 80"
- "Above 80"

**Why we ask:** "Tax slabs change based on your age. Senior citizens get higher tax-free income under the old regime."

**Validation:** Required. One selection mandatory.

**FAQ:**
- Q: "What if I turn 60 this year?"
  A: "If you turn 60 on or before 31st March 2026, you qualify as a senior citizen for FY 2025-26."
- Q: "Does age matter in the new regime?"
  A: "No. The new regime has the same slabs for everyone regardless of age."

**Live Preview Update:** None yet (insufficient data).

---

### STEP 1: Monthly In-Hand Salary

**Question:** "What amount lands in your bank account every month?"
**Subtext:** "This is your take-home salary after all deductions. Just one number."
**Input:** Currency input field (₹)
- Placeholder: "e.g., 75,000"
- Format: Indian number format (₹75,000)
- Allow decimals: No (round to nearest rupee)

**Validation:**
- Required
- Minimum: ₹1,000 (error: "Please enter a realistic monthly salary")
- Maximum: ₹50,00,000 (error: "Please enter an amount below ₹50 lakh per month")
- Regex: `/^\d+$/`

**FAQ:**
- Q: "What if my salary varies every month?"
  A: "Enter your average monthly in-hand. If you receive a fixed salary, enter that amount."
- Q: "Should I include my bonus?"
  A: "No, we'll ask about bonuses separately. Enter only your regular monthly salary."
- Q: "What about reimbursements?"
  A: "Don't include reimbursements like phone bills or fuel. Only your fixed monthly salary."

**Live Preview Update:** Shows estimated annual in-hand (monthly × 12). No tax estimate yet.

---

### STEP 2: Do You Know Your Gross Salary?

**Question:** "Do you know your gross annual salary or CTC?"
**Subtext:** "Don't worry if you don't. We can work it out together."
**Input:** Radio buttons
- "Yes, I know my gross salary"
- "No, but I have my payslip"
- "No, I only know my in-hand"

**Branching Logic:**
- Option 1 → Step 2A
- Option 2 → Step 2B
- Option 3 → Step 2C

**FAQ:**
- Q: "What's the difference between gross and in-hand?"
  A: "Gross salary is what your company calculates before deductions like PF, tax, and professional tax. In-hand is what you actually receive."
- Q: "Where do I find my gross salary?"
  A: "Check your offer letter, Form 16, or the top section of your monthly payslip."

---

### STEP 2A: Enter Gross Components (If user knows gross)

**Question:** "Enter your annual salary components"
**Subtext:** "These should match your Form 16 or payslip."

**Input Fields (all currency, annual amounts):**
1. Basic Salary + Dearness Allowance (DA)
   - Required
   - Min: ₹12,000 | Max: ₹5,00,00,000
   - Tooltip: "This is the foundation of your salary. PF and HRA are usually calculated from this."

2. House Rent Allowance (HRA) received
   - Optional (default: 0)
   - Min: 0 | Max: ₹1,00,00,000
   - Tooltip: "The HRA component in your salary structure, not the rent you pay."

3. Special Allowance / Other taxable allowances
   - Optional (default: 0)
   - Min: 0 | Max: ₹5,00,00,000

4. Annual Bonus / Performance Pay
   - Optional (default: 0)
   - Min: 0 | Max: ₹5,00,00,000

5. Leave Travel Allowance (LTA) received
   - Optional (default: 0)
   - Min: 0 | Max: ₹10,00,000
   - Tooltip: "Only if LTA is part of your salary structure."

**Auto-calculation:**
- Display "Estimated Gross Salary: ₹X" as a live sum below inputs
- Formula: Basic + HRA + Special + Bonus + LTA

**Validation:**
- Basic must be > 0
- Sum of all components must be ≥ (monthly in-hand × 12)
- If sum < (monthly in-hand × 12), show warning: "Your gross salary seems lower than your in-hand. Please double-check your numbers."

**FAQ:**
- Q: "What if I don't have DA?"
  A: "Enter 0 for DA. Just make sure your Basic is accurate."
- Q: "What about conveyance allowance?"
  A: "Include it under Special Allowance. Most conveyance allowances are taxable now."

**Live Preview Update:** Shows Gross Salary. Still no tax estimate (need deductions).

---

### STEP 2B: Estimate From Payslip (If user has payslip)

**Question:** "Let's read your payslip together"
**Subtext:** "Enter the monthly amounts you see on your salary slip."

**Input Fields (monthly amounts):**
1. Basic Salary shown on payslip
   - Required
   - Tooltip: "Usually the first line item on your payslip"

2. HRA shown on payslip
   - Optional (default: 0)

3. Any other fixed monthly allowances (special, conveyance, etc.)
   - Optional (default: 0)

4. Monthly deductions you see:
   - Employee PF contribution
   - Professional Tax
   - Income Tax (TDS)
   - Any other deductions

5. Annual bonus (if any)
   - Optional

**Auto-calculation:**
- Estimated Gross = (Basic + HRA + Other allowances) × 12 + Bonus
- Estimated In-Hand = Gross – (PF × 12) – (PT × 12) – (TDS × 12) – Other deductions
- Show comparison: "Your estimated in-hand: ₹X/month | You entered: ₹Y/month"
- If variance > 10%, show: "These numbers don't match your in-hand salary. Please check your inputs."

**FAQ:**
- Q: "My payslip shows 'CTC' not gross"
  A: "CTC includes employer PF and other costs. For tax calculation, we need your gross salary (what's taxable). Use the components above."
- Q: "What if my company doesn't give payslips?"
  A: "Try checking your bank statement for regular credits, or ask your HR for a salary breakup."

---

### STEP 2C: Reverse Estimate (If user only knows in-hand)

**Question:** "Let's estimate your salary structure"
**Subtext:** "We'll ask a few questions to reverse-calculate your gross salary. This won't be perfect, but it'll be close."

**Input Fields:**
1. "Does your company deduct PF from your salary?"
   - Yes / No
   - If Yes: "How much PF is deducted per month?" (currency input)
   - Auto-calculate Basic: PF ÷ 0.12 (since PF is 12% of Basic)
   - Show: "Estimated Basic Salary: ₹X"

2. "Do you get HRA as part of your salary?"
   - Yes / Not sure / No
   - If Yes or Not sure: "What's your monthly rent?" (currency input)
   - If Not sure: Estimate HRA as 40% of estimated Basic

3. "Do you pay professional tax?"
   - Yes / No
   - If Yes: "How much per month?" (currency input)
   - Show state-wise max reference: "Max ₹2,500/year in most states"

4. "Any other monthly deductions you see in your bank statement?"
   - Text area or list of common deductions with amounts

**Auto-calculation:**
- Estimated Gross = In-hand + PF + PT + other deductions + estimated TDS
- TDS estimation: rough calculation based on in-hand × 12
- Show confidence level: "High / Medium / Low" based on data completeness

**FAQ:**
- Q: "How accurate is this estimate?"
  A: "Within 5-10% if you answer accurately. For precise results, check your Form 16 after the financial year ends."
- Q: "What if the estimate is wrong?"
  A: "You can always go back and adjust your inputs. The final result shows you exactly how we calculated everything."

---

### STEP 3: Rent & HRA

**Question:** "Do you live in a rented house?"
**Input:** Yes / No toggle

**If Yes:**
1. "How much rent do you pay per month?" (currency)
   - Min: ₹500 | Max: ₹10,00,000
   - Validation: Must be > 0

2. "Which city do you live in?"
   - Searchable dropdown with all Indian cities
   - Auto-flag metro cities: Delhi, Mumbai, Chennai, Kolkata, Bengaluru, Hyderabad, Pune, Ahmedabad
   - Show: "Metro city" or "Non-metro city" badge

3. "Do you receive HRA from your employer?"
   - Yes / No
   - If Yes and not already captured: "How much HRA per month?" (currency)

**HRA Exemption Calculation (Old Regime Only):**
```
 exemption = min(
   actual_hra_received,
   metro ? 0.5 * basic : 0.4 * basic,
   max(0, rent_paid - 0.1 * basic)
 )
```
- If user doesn't receive HRA → exemption = 0
- If user lives in own house → exemption = 0

**FAQ:**
- Q: "I live with my parents and pay them rent. Does that count?"
  A: "Yes, if you have a rent agreement and rent receipts. Your parents will need to declare this as income."
- Q: "My rent is ₹25,000 but my HRA is only ₹15,000"
  A: "Your exemption is limited to your HRA amount. The extra rent doesn't give additional tax benefit."
- Q: "I don't get HRA. Can I claim anything?"
  A: "If you're salaried and don't get HRA, you can claim deduction u/s 80GG, but it requires you to not own a house and has several conditions. This app focuses on the common HRA exemption."
- Q: "What if my landlord doesn't give receipts?"
  A: "For HRA exemption, you need rent receipts as proof. If your rent exceeds ₹1 lakh/year, you also need your landlord's PAN."

**Live Preview Update:**
- Shows HRA exemption amount (Old Regime)
- Shows "HRA exemption not available in New Regime" note
- Updates taxable income estimate for Old Regime

---

### STEP 4: Investments (80C)

**Question:** "Do you invest in any tax-saving schemes?"
**Subtext:** "These reduce your taxable income under the old regime. We'll add them up automatically."

**Input Fields (all optional, annual amounts):**
1. "Employee Provident Fund (EPF) — your contribution"
   - Currency input
   - Auto-fill if captured in Step 2 (PF × 12)
   - Tooltip: "This is automatically deducted from your salary. Most salaried people already have this."

2. "Public Provident Fund (PPF)"
   - Currency input
   - Max: ₹1,50,000

3. "ELSS Mutual Funds (Tax Saver)"
   - Currency input

4. "Life Insurance Premium (LIC / Term Plan / ULIP)"
   - Currency input
   - Tooltip: "Only premiums where the sum assured is at least 10 times the annual premium qualify fully."

5. "National Savings Certificate (NSC) / Senior Citizen Savings Scheme (SCSS)"
   - Currency input

6. "Sukanya Samriddhi Yojana (SSY)"
   - Currency input

7. "Tuition Fees for Children"
   - Currency input
   - Tooltip: "For full-time education in India. Max 2 children."

8. "Home Loan Principal Repayment"
   - Currency input
   - Tooltip: "The principal portion of your EMI, not the interest."

9. "5-Year Tax-Saving Fixed Deposit"
   - Currency input

10. "Any other 80C investments"
    - Currency input

**Auto-calculation:**
- Sum all inputs
- Display: "Total 80C Investments: ₹X"
- If > ₹1,50,000: "Capped at ₹1,50,000 for tax benefit"
- Effective 80C deduction = min(total, 1,50,000)

**FAQ:**
- Q: "What is 80C?"
  A: "Section 80C is a basket of investments and expenses that reduce your taxable income by up to ₹1.5 lakh per year."
- Q: "I don't invest in anything. Is that bad?"
  A: "Not bad, but you might pay more tax under the old regime. The new regime might be better for you."
- Q: "Does my PF count toward 80C?"
  A: "Yes! Your employee PF contribution is part of 80C. Most people already have ₹30,000–₹50,000 covered through PF alone."
- Q: "What's the difference between EPF and PPF?"
  A: "EPF is through your employer. PPF is a personal account you open at a bank or post office. Both qualify for 80C."

**Live Preview Update:**
- Shows 80C deduction amount
- Shows remaining 80C limit: "₹X more can be invested for full benefit"
- Updates Old Regime taxable income

---

### STEP 5: Health Insurance (80D)

**Question:** "Do you have health insurance?"
**Subtext:** "Health insurance premiums give you tax benefits under the old regime."

**Input Fields:**
1. "Health insurance for yourself, spouse & children"
   - Currency input
   - Max: ₹1,00,000
   - Auto-calculate max based on age:
     - If age < 60: max benefit ₹25,000
     - If age ≥ 60: max benefit ₹50,000

2. "Health insurance for your parents"
   - Currency input
   - Follow-up: "Are your parents senior citizens (60+ years)?" (Yes/No)
   - If Yes: max benefit ₹50,000
   - If No: max benefit ₹25,000

3. "Preventive health checkup expenses"
   - Currency input
   - Max: ₹5,000
   - Tooltip: "This is included within the above limits, not over and above."

**Auto-calculation:**
- Self/family deduction = min(premium, age-based limit)
- Parents deduction = min(premium, parents-age-based limit)
- Total 80D = self_deduction + parents_deduction
- Note: Preventive checkup capped within respective limits

**FAQ:**
- Q: "My company provides health insurance. Does that count?"
  A: "Only if you pay the premium yourself. If your employer pays, you can't claim the deduction."
- Q: "What if I only have a ₹5,000 policy?"
  A: "You can claim the full ₹5,000. Every bit helps reduce your tax."
- Q: "Does dental treatment count?"
  A: "No, only health insurance premiums and preventive checkups qualify for 80D."
- Q: "I pay for my in-laws' insurance. Can I claim?"
  A: "No, 80D covers only self, spouse, children, and parents. In-laws are not covered."

**Live Preview Update:**
- Shows 80D deduction amount
- Updates Old Regime taxable income

---

### STEP 6: Home Loan

**Question:** "Do you have a home loan?"
**Input:** Yes / No toggle

**If Yes:**
1. "Is the house self-occupied or rented out?"
   - Self-occupied / Rented out / Under construction

2. "Annual home loan interest paid"
   - Currency input
   - Tooltip: "Check your loan statement or interest certificate from the bank."

3. "Is this your first home purchase?"
   - Yes / No

4. "When was your loan sanctioned?"
   - Before April 2016 / April 2016 – March 2017 / April 2017 – March 2019 / April 2019 – March 2022 / After March 2022

5. "Property value (approximate)"
   - Currency input

6. "Loan amount"
   - Currency input

**Calculation Logic:**
- **Self-occupied (Old Regime):**
  - Section 24(b): min(interest_paid, 2,00,000)
  - If first home + loan sanctioned 2016-17 + property ≤ ₹50L + loan ≤ ₹35L:
    - Additional 80EE: min(interest_paid - 24b_deduction, 50,000)
  - If first home + loan sanctioned 2019-22 + stamp duty ≤ ₹45L + not claiming 80EE:
    - Additional 80EEA: min(interest_paid - 24b_deduction, 1,50,000)
  - Total home loan benefit = 24b + 80EE + 80EEA

- **Self-occupied (New Regime):**
  - No benefit for interest on self-occupied property

- **Rented out:**
  - Old Regime: Full interest allowed (no ₹2L cap)
  - New Regime: Full interest allowed (no ₹2L cap) — BUT loss cannot be set off against salary income
  - For simplicity in this app: Show note that rental income must be declared separately

- **Under construction:**
  - No current year benefit. Pre-construction interest can be claimed in 5 equal installments after construction completes.
  - Show informational note only.

**FAQ:**
- Q: "What's the difference between principal and interest?"
  A: "Principal reduces your loan balance. Interest is the bank's charge. For tax, principal gives 80C benefit, interest gives Section 24 benefit."
- Q: "I pay ₹4 lakh interest. Can I claim all of it?"
  A: "For self-occupied property, interest deduction is capped at ₹2 lakh under Section 24. The rest doesn't give tax benefit."
- Q: "My house is under construction"
  A: "You can't claim interest deduction now. But after construction is complete, you can claim pre-construction interest in 5 equal yearly installments."
- Q: "Joint loan with spouse — how do we claim?"
  A: "Both co-owners can claim deductions proportionally. This app assumes single ownership for simplicity."

**Live Preview Update:**
- Shows home loan interest deduction (Old Regime)
- Shows "Not applicable in New Regime" for self-occupied
- Updates taxable income

---

### STEP 7: NPS Contributions

**Question:** "Do you contribute to the National Pension System (NPS)?"
**Subtext:** "NPS gives you extra tax benefits beyond 80C."

**Input Fields:**
1. "Your monthly NPS contribution (employee)"
   - Currency input
   - Tooltip: "This is deducted from your salary if your company has NPS."

2. "Does your employer also contribute to NPS?"
   - Yes / No
   - If Yes: "Employer's monthly NPS contribution"
   - Currency input

**Calculation Logic:**
- **Old Regime:**
  - Employee contribution (80CCD(1)): Included in 80C basket (max ₹1.5L combined)
  - Additional employee contribution (80CCD(1B)): ₹50,000 over and above 80C
  - Employer contribution (80CCD(2)): Up to 10% of (Basic + DA)

- **New Regime:**
  - Employee contribution: NO DEDUCTION
  - Employer contribution (80CCD(2)): Up to 14% of (Basic + DA) — ALLOWED

**Auto-calculation:**
- Annual employee contribution = monthly × 12
- Annual employer contribution = monthly × 12
- Show: "Employee contribution: ₹X (part of 80C) + ₹Y additional (80CCD1B)"
- Show: "Employer contribution: ₹Z (deductible in both regimes, different limits)"

**FAQ:**
- Q: "What's NPS?"
  A: "National Pension System is a retirement savings scheme. You contribute during working years and get a pension after 60."
- Q: "Is NPS better than PPF?"
  A: "NPS has market-linked returns and extra ₹50,000 tax benefit. PPF has guaranteed returns. Both are good for different goals."
- Q: "My company doesn't offer NPS. Can I open it myself?"
  A: "Yes, through eNPS. But only the ₹50,000 additional benefit (80CCD1B) applies, not the employer contribution benefit."

**Live Preview Update:**
- Shows NPS deductions separately for Old and New regimes
- Updates taxable income for both regimes

---

### STEP 8: Other Income & Deductions

**Question:** "Any other income or deductions to consider?"
**Subtext:** "These are less common but can make a difference."

**Input Fields (all optional):**
1. "Interest from savings accounts (all banks combined)"
   - Currency input
   - Old Regime: Deduction up to ₹10,000 (80TTA) for non-seniors, ₹50,000 (80TTB) for seniors
   - New Regime: Fully taxable, no deduction

2. "Interest from Fixed Deposits (FDs)"
   - Currency input
   - Old Regime: Fully taxable (no deduction except 80TTB for seniors)
   - New Regime: Fully taxable
   - Tooltip: "This is added to your taxable income. TDS may have been deducted by the bank."

3. "Professional Tax paid"
   - Currency input
   - Max: ₹2,500
   - Old Regime: Deductible from salary income
   - New Regime: NOT deductible
   - Auto-fill if captured earlier

4. "Leave Travel Allowance (LTA) claimed this year"
   - Currency input
   - Max: As per actual travel cost
   - Old Regime: Exempt if domestic travel, 2 journeys in 4-year block
   - New Regime: NOT exempt
   - Tooltip: "Only the travel fare (air/rail/bus) within India. Not hotel or food."

5. "Children Education Allowance received"
   - Number of children: dropdown (0, 1, 2+)
   - Old Regime: ₹100/month/child (max 2) = ₹1,200/year/child
   - New Regime: Taxable

6. "Hostel Expenditure Allowance received"
   - Number of children: dropdown (0, 1, 2+)
   - Old Regime: ₹300/month/child (max 2) = ₹3,600/year/child
   - New Regime: Taxable

7. "Donations to charitable institutions (80G)"
   - Currency input
   - Old Regime: 50% or 100% deduction depending on institution
   - New Regime: NOT allowed
   - Simplified: Assume 50% deduction for calculation

**FAQ:**
- Q: "My bank deducted TDS on FD interest. Do I still pay tax?"
  A: "TDS is just advance tax. If your total income is below the taxable limit, you can claim a refund. Otherwise, you pay the balance tax."
- Q: "What's the difference between 80TTA and 80TTB?"
  A: "80TTA gives ₹10,000 deduction on savings interest for people below 60. 80TTB gives ₹50,000 for senior citizens and covers FD interest too."
- Q: "I didn't claim LTA this year. Can I claim it next year?"
  A: "LTA works in 4-year blocks. You can carry forward one unclaimed journey to the next block."

**Live Preview Update:**
- Shows Other Income added
- Shows applicable deductions for Old Regime
- Shows "Not applicable in New Regime" where relevant

---

### STEP 9: Review & Calculate

**Question:** "Ready to see your result?"
**Subtext:** "Here's a summary of what you told us. You can go back and edit if needed."

**Layout:**
- Collapsible summary cards for each section
- Edit button on each card (navigates back to that step)
- "Calculate My Tax" primary CTA button
- "Go Back" secondary button

**Summary Cards:**
1. Basic Info: Age category
2. Salary: Gross ₹X | In-hand ₹Y/month
3. HRA: Rent ₹X/month | City | Exemption ₹Y
4. 80C Investments: ₹X (capped at ₹1.5L)
5. Health Insurance: ₹X (80D)
6. Home Loan: Interest ₹X | Deduction ₹Y
7. NPS: Employee ₹X | Employer ₹Y
8. Other: Interest income ₹X | Professional tax ₹Y | LTA ₹Z

**Validation:**
- All required fields must be complete
- At least gross salary or estimated gross must be available
- If estimated gross has low confidence, show warning: "Your result is based on estimates. For precise numbers, check your Form 16."

**FAQ:**
- Q: "What if I made a mistake?"
  A: "Click the edit button on any section to go back and correct it."
- Q: "When will I get the exact tax amount?"
  A: "This gives you a very close estimate. The exact amount depends on your final Form 16 and any last-minute investments."

---

## 11. Calculation Engine

### 11.1 Data Model (TypeScript Interfaces)

```typescript
interface TaxpayerInfo {
  ageCategory: 'below60' | 'senior' | 'superSenior'; // <60, 60-80, 80+
  monthlyInHand: number;
  knowsGross: 'yes' | 'payslip' | 'no';
}

interface SalaryComponents {
  basic: number;           // Annual
  da: number;              // Annual
  hraReceived: number;     // Annual
  specialAllowance: number;// Annual
  ltaReceived: number;     // Annual
  bonus: number;           // Annual
  otherAllowances: number; // Annual
}

interface Deductions {
  // 80C basket
  epfEmployee: number;
  ppf: number;
  elss: number;
  lic: number;
  nsc: number;
  ssy: number;
  tuitionFees: number;
  homeLoanPrincipal: number;
  taxSaverFD: number;
  other80C: number;

  // 80CCD
  npsEmployee: number;     // 80CCD(1) - part of 80C
  npsAdditional: number;   // 80CCD(1B) - ₹50K extra
  npsEmployer: number;     // 80CCD(2)

  // 80D
  healthInsuranceSelf: number;
  healthInsuranceParents: number;
  parentsSeniorCitizen: boolean;
  preventiveCheckup: number;

  // Home Loan
  homeLoanInterest: number;
  homeLoanType: 'selfOccupied' | 'rented' | 'underConstruction';
  isFirstHome: boolean;
  loanSanctionDate: 'before2016' | '2016-17' | '2017-19' | '2019-22' | 'after2022';
  propertyValue: number;
  loanAmount: number;

  // Other
  savingsInterest: number;
  fdInterest: number;
  professionalTax: number;
  ltaClaimed: number;
  childrenEducation: number; // Number of children (0-2)
  childrenHostel: number;    // Number of children (0-2)
  donations80G: number;
}

interface TaxResult {
  oldRegime: RegimeResult;
  newRegime: RegimeResult;
  recommendation: 'old' | 'new';
  savingsAmount: number;
}

interface RegimeResult {
  grossSalary: number;
  exemptAllowances: number;
  standardDeduction: number;
  incomeFromHouseProperty: number;
  incomeFromOtherSources: number;
  grossTotalIncome: number;
  deductions: number;
  taxableIncome: number;
  taxBeforeRebate: number;
  rebate87A: number;
  taxAfterRebate: number;
  cess: number;
  totalTax: number;
  effectiveTaxRate: number;
  slabBreakdown: SlabEntry[];
}

interface SlabEntry {
  slabRange: string;
  rate: number;
  amountInSlab: number;
  tax: number;
}
```

### 11.2 Old Regime Calculation Algorithm

```
function calculateOldRegime(data): RegimeResult {
  // 1. Gross Salary
  grossSalary = data.basic + data.da + data.hraReceived + 
                data.specialAllowance + data.ltaReceived + 
                data.bonus + data.otherAllowances;

  // 2. Exempt Allowances
  // HRA Exemption
  hraExemption = 0;
  if (data.rentPaid > 0 && data.hraReceived > 0) {
    basicPlusDA = data.basic + data.da;
    metroMultiplier = data.isMetro ? 0.5 : 0.4;
    hraExemption = min(
      data.hraReceived,
      metroMultiplier * basicPlusDA,
      max(0, data.rentPaid * 12 - 0.1 * basicPlusDA)
    );
  }

  // LTA Exemption (simplified — assume claimed if entered)
  ltaExemption = min(data.ltaClaimed, data.ltaReceived);

  // Children allowances
  childrenEduExemption = min(data.childrenEducation, 2) * 1200;
  childrenHostelExemption = min(data.childrenHostel, 2) * 3600;

  // Professional Tax
  ptDeduction = min(data.professionalTax, 2500);

  exemptAllowances = hraExemption + ltaExemption + 
                     childrenEduExemption + childrenHostelExemption + 
                     ptDeduction;

  // 3. Standard Deduction
  standardDeduction = 50000;

  // 4. Income from House Property
  incomeFromHouseProperty = 0;
  if (data.homeLoanType === 'selfOccupied') {
    // Loss up to 2 lakh
    interestDeduction24 = min(data.homeLoanInterest, 200000);
    incomeFromHouseProperty = -interestDeduction24;
  } else if (data.homeLoanType === 'rented') {
    // Simplified: show note that rental income needs to be added
    // For this app, assume user only wants interest benefit
    incomeFromHouseProperty = -data.homeLoanInterest;
  }
  // Under construction: 0

  // 5. Income from Other Sources
  savingsInterestTaxable = max(0, data.savingsInterest - 10000); // 80TTA
  if (data.ageCategory !== 'below60') {
    // Senior citizen: 80TTB applies
    savingsInterestTaxable = max(0, data.savingsInterest - 50000);
    fdInterestTaxable = max(0, data.fdInterest - max(0, 50000 - data.savingsInterest));
  } else {
    fdInterestTaxable = data.fdInterest; // No deduction for FD for non-seniors
  }
  incomeFromOtherSources = savingsInterestTaxable + fdInterestTaxable;

  // 6. Gross Total Income
  grossTotalIncome = grossSalary - exemptAllowances - standardDeduction + 
                     incomeFromHouseProperty + incomeFromOtherSources;

  // 7. Chapter VI-A Deductions
  // 80C + 80CCC + 80CCD(1) combined
  total80C = data.epfEmployee + data.ppf + data.elss + data.lic + 
             data.nsc + data.ssy + data.tuitionFees + 
             data.homeLoanPrincipal + data.taxSaverFD + data.other80C +
             data.npsEmployee; // 80CCD(1) part of 80C
  deduction80C = min(total80C, 150000);

  // 80CCD(1B) — additional NPS
  deduction80CCD1B = min(data.npsAdditional, 50000);

  // 80CCD(2) — employer NPS
  basicPlusDA = data.basic + data.da;
  maxEmployerNPS = 0.10 * basicPlusDA;
  deduction80CCD2 = min(data.npsEmployer, maxEmployerNPS);

  // 80D — Health Insurance
  selfLimit = (data.ageCategory === 'below60') ? 25000 : 50000;
  parentsLimit = data.parentsSeniorCitizen ? 50000 : 25000;

  // Include preventive checkup within limits
  totalSelfHealth = data.healthInsuranceSelf + data.preventiveCheckup;
  deduction80DSelf = min(totalSelfHealth, selfLimit);
  deduction80DParents = min(data.healthInsuranceParents, parentsLimit);
  deduction80D = deduction80DSelf + deduction80DParents;

  // 80EE — Additional home loan interest (first-time buyers)
  deduction80EE = 0;
  if (data.isFirstHome && data.loanSanctionDate === '2016-17' && 
      data.propertyValue <= 5000000 && data.loanAmount <= 3500000) {
    remainingInterest = max(0, data.homeLoanInterest - 200000);
    deduction80EE = min(remainingInterest, 50000);
  }

  // 80EEA — Affordable housing
  deduction80EEA = 0;
  if (data.isFirstHome && data.loanSanctionDate === '2019-22' && 
      data.propertyValue <= 4500000 && deduction80EE === 0) {
    remainingInterest = max(0, data.homeLoanInterest - 200000);
    deduction80EEA = min(remainingInterest, 150000);
  }

  // 80G — Donations (simplified 50%)
  deduction80G = data.donations80G * 0.5;

  totalDeductions = deduction80C + deduction80CCD1B + deduction80CCD2 + 
                    deduction80D + deduction80EE + deduction80EEA + deduction80G;

  // 8. Total Taxable Income
  taxableIncome = max(0, grossTotalIncome - totalDeductions);

  // 9. Tax Calculation based on age slabs
  taxBeforeRebate = calculateSlabTax(taxableIncome, data.ageCategory, 'old');

  // 10. Rebate u/s 87A
  rebate87A = 0;
  if (taxableIncome <= 500000) {
    rebate87A = min(taxBeforeRebate, 12500);
  }

  taxAfterRebate = max(0, taxBeforeRebate - rebate87A);

  // 11. Cess (No surcharge — out of scope)
  cess = 0.04 * taxAfterRebate;
  totalTax = taxAfterRebate + cess;

  // 12. Effective Tax Rate
  effectiveTaxRate = grossSalary > 0 ? (totalTax / grossSalary) * 100 : 0;

  return {
    grossSalary,
    exemptAllowances,
    standardDeduction,
    incomeFromHouseProperty,
    incomeFromOtherSources,
    grossTotalIncome,
    deductions: totalDeductions,
    taxableIncome,
    taxBeforeRebate,
    rebate87A,
    taxAfterRebate,
    cess,
    totalTax,
    effectiveTaxRate,
    slabBreakdown: generateSlabBreakdown(taxableIncome, data.ageCategory, 'old')
  };
}
```

### 11.3 New Regime Calculation Algorithm

```
function calculateNewRegime(data): RegimeResult {
  // 1. Gross Salary (same as old)
  grossSalary = data.basic + data.da + data.hraReceived + 
                data.specialAllowance + data.ltaReceived + 
                data.bonus + data.otherAllowances;

  // 2. Standard Deduction
  standardDeduction = 75000;

  // 3. Exemptions (NONE except employer NPS)
  exemptAllowances = 0;

  // 4. Income from House Property
  // Self-occupied: NO interest deduction
  // Rented out: Interest allowed but loss cannot be set off
  incomeFromHouseProperty = 0;
  if (data.homeLoanType === 'rented') {
    // Show note: loss cannot be set off in new regime
    incomeFromHouseProperty = 0; // Simplified for salaried app
  }

  // 5. Income from Other Sources
  // NO 80TTA or 80TTB deduction
  incomeFromOtherSources = data.savingsInterest + data.fdInterest;

  // 6. Gross Total Income
  grossTotalIncome = grossSalary - standardDeduction + 
                     incomeFromHouseProperty + incomeFromOtherSources;

  // 7. Deductions (very limited)
  // Only 80CCD(2) — employer NPS
  basicPlusDA = data.basic + data.da;
  maxEmployerNPS = 0.14 * basicPlusDA;
  deduction80CCD2 = min(data.npsEmployer, maxEmployerNPS);

  totalDeductions = deduction80CCD2;

  // 8. Taxable Income
  taxableIncome = max(0, grossTotalIncome - totalDeductions);

  // 9. Tax Calculation (same slabs for all ages)
  taxBeforeRebate = calculateSlabTax(taxableIncome, 'below60', 'new');

  // 10. Rebate u/s 87A + Marginal Relief
  rebate87A = 0;
  if (taxableIncome <= 1200000) {
    rebate87A = min(taxBeforeRebate, 60000);
  } else {
    // Marginal relief
    excessOver12L = taxableIncome - 1200000;
    if (taxBeforeRebate > excessOver12L) {
      rebate87A = taxBeforeRebate - excessOver12L;
    }
  }

  taxAfterRebate = max(0, taxBeforeRebate - rebate87A);

  // 11. Cess
  cess = 0.04 * taxAfterRebate;
  totalTax = taxAfterRebate + cess;

  // 12. Effective Tax Rate
  effectiveTaxRate = grossSalary > 0 ? (totalTax / grossSalary) * 100 : 0;

  return {
    grossSalary,
    exemptAllowances,
    standardDeduction,
    incomeFromHouseProperty,
    incomeFromOtherSources,
    grossTotalIncome,
    deductions: totalDeductions,
    taxableIncome,
    taxBeforeRebate,
    rebate87A,
    taxAfterRebate,
    cess,
    totalTax,
    effectiveTaxRate,
    slabBreakdown: generateSlabBreakdown(taxableIncome, 'below60', 'new')
  };
}
```

### 11.4 Slab Tax Calculation Function

```
function calculateSlabTax(income, ageCategory, regime): number {
  if (regime === 'new') {
    slabs = [
      { limit: 400000, rate: 0 },
      { limit: 800000, rate: 0.05 },
      { limit: 1200000, rate: 0.10 },
      { limit: 1600000, rate: 0.15 },
      { limit: 2000000, rate: 0.20 },
      { limit: 2400000, rate: 0.25 },
      { limit: Infinity, rate: 0.30 }
    ];
  } else {
    if (ageCategory === 'below60') {
      slabs = [
        { limit: 250000, rate: 0 },
        { limit: 500000, rate: 0.05 },
        { limit: 1000000, rate: 0.20 },
        { limit: Infinity, rate: 0.30 }
      ];
    } else if (ageCategory === 'senior') {
      slabs = [
        { limit: 300000, rate: 0 },
        { limit: 500000, rate: 0.05 },
        { limit: 1000000, rate: 0.20 },
        { limit: Infinity, rate: 0.30 }
      ];
    } else { // superSenior
      slabs = [
        { limit: 500000, rate: 0 },
        { limit: 1000000, rate: 0.20 },
        { limit: Infinity, rate: 0.30 }
      ];
    }
  }

  tax = 0;
  previousLimit = 0;
  for each slab in slabs:
    if (income > slab.limit) {
      tax += (slab.limit - previousLimit) * slab.rate;
      previousLimit = slab.limit;
    } else {
      tax += (income - previousLimit) * slab.rate;
      break;
    }
  return tax;
}
```

### 11.5 Slab Breakdown Generation

```
function generateSlabBreakdown(income, ageCategory, regime): SlabEntry[] {
  // Returns array of {slabRange, rate, amountInSlab, tax}
  // For visualization in the live preview and results
}
```

---

## 12. Input Validations & Edge Cases

### 12.1 Validation Matrix

| Field | Required | Min | Max | Format | Error Message |
|-------|----------|-----|-----|--------|---------------|
| Age | Yes | — | — | Radio | "Please select your age group" |
| Monthly In-Hand | Yes | 1,000 | 50,00,000 | Integer | "Enter a realistic monthly salary" |
| Basic Salary | Yes | 0 | 5,00,00,000 | Integer | "Basic salary is required for accurate calculation" |
| HRA Received | No | 0 | 1,00,00,000 | Integer | — |
| Rent Paid | No | 0 | 10,00,000 | Integer | — |
| 80C Investments | No | 0 | 1,50,000+ | Integer | — |
| Health Insurance | No | 0 | 1,00,000 | Integer | — |
| Home Loan Interest | No | 0 | 50,00,000 | Integer | — |
| NPS Contribution | No | 0 | 10,00,000 | Integer | — |
| Professional Tax | No | 0 | 2,500 | Integer | "Max professional tax is ₹2,500/year" |

### 12.2 Edge Cases & Handling

1. **Zero Tax Liability (Both Regimes)**
   - Show: "Great news! You don't owe any income tax this year."
   - Still show comparison for educational purposes

2. **Negative Taxable Income**
   - Clamp to 0. Show note: "Your deductions fully cover your income."

3. **HRA > Basic Salary**
   - Allow input but show warning: "HRA is usually 40-50% of basic. Please verify."

4. **Rent < 10% of Basic**
   - HRA exemption will be 0. Show explanatory note.

5. **User enters gross < in-hand**
   - Show error: "Gross salary cannot be less than in-hand salary. Please check your inputs."

6. **Senior Citizen with 80TTB**
   - Auto-apply 80TTB instead of 80TTA
   - Show combined interest (savings + FD) capped at ₹50,000

7. **Home Loan + 80EE + 80EEA Eligibility**
   - Check all conditions before applying
   - If 80EE applies, 80EEA cannot apply
   - Show which section is being applied and why

8. **Marginal Relief Zone (New Regime, ₹12L–₹12.75L)**
   - Detect automatically
   - Show special note: "Marginal relief applies — your tax is limited to the amount above ₹12 lakh."

9. **Employer NPS > 14% (New) or > 10% (Old)**
   - Cap deduction at statutory limit
   - Show: "Employer contributes ₹X. Tax benefit capped at ₹Y (14%/10% of Basic+DA)."

10. **No Deductions at All**
    - Likely new regime is better. Highlight this clearly.

11. **Very High Income (>₹50L)**
    - Show disclaimer: "Surcharge applies on income above ₹50 lakh. This app doesn't calculate surcharge. Your actual tax may be higher."

12. **Both Regimes Give Same Tax**
    - Show: "Both regimes result in the same tax. Choose based on convenience."
    - Suggest: "New regime is simpler with less paperwork."

---

## 13. Live Preview Panel Logic

### 13.1 Panel States

**State 1: Insufficient Data (Steps 0–2)**
- Show placeholder: "Answer a few more questions to see your tax estimate"
- Animated skeleton loader

**State 2: Partial Data (Steps 3–8)**
- Show running estimate with confidence indicator:
  - "Based on current inputs (Medium confidence)"
- Display:
  - Estimated Gross Salary
  - Old Regime Tax: ₹X
  - New Regime Tax: ₹Y
  - Current Winner: "New regime saves ₹Z" or "Old regime saves ₹Z"

**State 3: Complete Data (Step 9)**
- Full detailed preview

### 13.2 Panel Content Structure

```
┌─────────────────────────────┐
│  📊 LIVE TAX PREVIEW        │
│  Based on your inputs so far│
├─────────────────────────────┤
│                             │
│  Gross Salary        ₹X     │
│  ─────────────────────────  │
│  OLD REGIME                 │
│  Taxable Income      ₹A     │
│  Tax Before Cess     ₹B     │
│  Cess (4%)           ₹C     │
│  ─────────────────────────  │
│  TOTAL TAX           ₹D     │
│                             │
│  NEW REGIME                 │
│  Taxable Income      ₹E     │
│  Tax Before Cess     ₹F     │
│  Cess (4%)           ₹G     │
│  ─────────────────────────  │
│  TOTAL TAX           ₹H     │
│                             │
│  ─────────────────────────  │
│  🏆 [Regime] saves   ₹I     │
│                             │
├─────────────────────────────┤
│  SLAB BREAKDOWN (Old)       │
│  ┌─────────────────────┐   │
│  │ Mini bar chart      │   │
│  │ showing each slab   │   │
│  └─────────────────────┘   │
├─────────────────────────────┤
│  SLAB BREAKDOWN (New)       │
│  ┌─────────────────────┐   │
│  │ Mini bar chart      │   │
│  └─────────────────────┘   │
└─────────────────────────────┘
```

### 13.3 Update Triggers
- Recalculate on every input blur (not on every keystroke — debounce 300ms)
- Show "Calculating..." spinner if computation takes >100ms
- Animate number changes with count-up animation

---

## 14. Result Screen Specifications

### 14.1 Hero Recommendation Card

**Layout:** Full-width card at top, centered, high contrast background

**Content:**
```
┌─────────────────────────────────────────┐
│                                         │
│     🏆 PICK THE [NEW/OLD] REGIME        │
│                                         │
│     You save ₹47,820 per year           │
│                                         │
│     [Old Tax: ₹3,90,000]                │
│     [New Tax: ₹3,42,180]                │
│                                         │
│     [Share Result] [Start Over]         │
│                                         │
└─────────────────────────────────────────┘
```

**Colors:**
- If New Regime wins: Violet gradient background (`#7C3AED` to `#A78BFA`)
- If Old Regime wins: Emerald gradient background (`#059669` to `#34D399`)
- If tie: Gray background with neutral messaging

### 14.2 Side-by-Side Comparison Table

| Component | Old Regime | New Regime | Difference |
|-----------|------------|------------|------------|
| Gross Salary | ₹X | ₹X | — |
| Less: Exemptions | ₹A | ₹0 | ₹A |
| Less: Standard Deduction | ₹50,000 | ₹75,000 | -₹25,000 |
| Less: 80C Deductions | ₹B | ₹0 | ₹B |
| Less: 80D (Health) | ₹C | ₹0 | ₹C |
| Less: Home Loan Interest | ₹D | ₹0 | ₹D |
| Less: NPS (Employee) | ₹E | ₹0 | ₹E |
| Less: NPS (Employer) | ₹F | ₹G | ₹F-₹G |
| Less: Other Deductions | ₹H | ₹0 | ₹H |
| **Taxable Income** | **₹I** | **₹J** | ₹I-₹J |
| Tax Before Rebate | ₹K | ₹L | ₹K-₹L |
| Less: Rebate 87A | ₹M | ₹N | ₹M-₹N |
| Tax After Rebate | ₹O | ₹P | ₹O-₹P |
| Add: Cess (4%) | ₹Q | ₹R | ₹Q-₹R |
| **TOTAL TAX** | **₹S** | **₹T** | **₹S-₹T** |

### 14.3 Slab-by-Slab Breakdown (Visual)

**Old Regime Chart:**
- Horizontal stacked bar chart showing each slab
- Color-coded: Green for taxed amount, Gray for untaxed
- Tooltip on hover: "₹X taxed at Y% = ₹Z"

**New Regime Chart:**
- Same format, different slab boundaries

### 14.4 Personalized Education Section

**Title:** "How Your Inputs Affected Your Tax"

**Dynamic Cards (show only relevant ones):**

1. **Your Salary Structure**
   - "Your gross salary of ₹X puts you in the [Y%] tax bracket under the [regime]."

2. **Your HRA Benefit**
   - "You pay ₹X rent in [city]. This gives you ₹Y HRA exemption under the old regime. The new regime doesn't allow this."
   - OR: "You don't receive HRA, so this didn't affect your tax."

3. **Your 80C Investments**
   - "You invested ₹X in tax-saving instruments. This reduced your taxable income by ₹Y under the old regime."
   - If < ₹1.5L: "You can still invest ₹Z more to maximize your 80C benefit."

4. **Your Health Insurance**
   - "Your health insurance premium of ₹X saved you ₹Y in tax under the old regime."

5. **Your Home Loan**
   - "You pay ₹X home loan interest. Under the old regime, this reduces your taxable income by ₹Y."

6. **Your NPS Contribution**
   - "Your NPS contribution of ₹X gives you ₹Y extra deduction under the old regime (beyond 80C)."

7. **Interest Income**
   - "You earned ₹X from savings/FDs. Under the old regime, ₹Y is tax-free. Under the new regime, the full amount is taxable."

8. **Age Benefit**
   - For seniors: "As a senior citizen, your tax-free income under the old regime is ₹3 lakh instead of ₹2.5 lakh."

9. **Marginal Relief (if applicable)**
   - "Your income is just above ₹12 lakh. Marginal relief ensures you don't pay more tax than the amount above ₹12 lakh."

### 14.5 Practical Suggestions Section

**Title:** "Smart Moves for You"

**Dynamic suggestions based on inputs:**

1. **If 80C not maxed out:**
   - "You can invest ₹X more under 80C to save an additional ₹Y in tax under the old regime. Consider ELSS mutual funds or PPF."

2. **If no health insurance for parents:**
   - "Buying health insurance for your parents could save you up to ₹Y in tax (80D) under the old regime."

3. **If home loan interest > ₹2L:**
   - "Your home loan interest exceeds the ₹2 lakh Section 24 cap. Consider a joint loan with your spouse to double the benefit."

4. **If no NPS:**
   - "Starting an NPS account could give you an extra ₹50,000 tax deduction (80CCD1B) under the old regime."

5. **If new regime is clearly better:**
   - "The new regime saves you ₹X because your deductions (₹Y) are less than the benefit from lower tax rates."
   - "Tip: You don't need to submit investment proofs to your employer if you choose the new regime."

6. **If old regime is better by small margin (<₹5,000):**
   - "The old regime saves only ₹X. Consider if the paperwork and proof submission are worth it."

7. **If income < ₹12.75L:**
   - "Good news! Under the new regime, your income up to ₹12.75 lakh can be completely tax-free thanks to the ₹75,000 standard deduction and ₹60,000 rebate."

8. **If professional tax not claimed:**
   - "You pay ₹X professional tax. Make sure it's deducted from your taxable salary under the old regime."

### 14.6 Disclaimer Footer

"This calculation is based on the information you provided and current tax laws for FY 2025-26. It excludes surcharge (income > ₹50 lakh), capital gains, and agricultural income. For complex tax situations, please consult a Chartered Accountant."

---

## 15. Personalized Insights & Suggestions Engine

### Logic Rules for Suggestions

```
function generateSuggestions(data, result): string[] {
  suggestions = [];

  // 80C suggestion
  total80C = data.epfEmployee + data.ppf + data.elss + data.lic + ...;
  if (total80C < 150000 && result.oldRegime.totalTax > 0) {
    gap = 150000 - total80C;
    taxSaved = gap * getMarginalRate(data, 'old');
    suggestions.push(`Invest ₹${gap} more in 80C to save ₹${taxSaved} more tax.`);
  }

  // Health insurance suggestion
  if (data.healthInsuranceSelf === 0 && result.oldRegime.totalTax > 0) {
    suggestions.push("Consider health insurance — premiums up to ₹25,000 are deductible.");
  }

  // NPS suggestion
  if (data.npsEmployee === 0 && data.npsAdditional === 0) {
    suggestions.push("NPS gives an extra ₹50,000 deduction beyond 80C under the old regime.");
  }

  // Home loan suggestion
  if (data.homeLoanInterest > 200000 && data.homeLoanType === 'selfOccupied') {
    suggestions.push("Your interest exceeds the ₹2L cap. A joint loan could double this benefit.");
  }

  // Regime-specific
  if (result.recommendation === 'new') {
    suggestions.push("New regime means no investment proof submission to your employer. Less paperwork!");
  }

  // Marginal relief
  if (data.taxableIncomeNew > 1200000 && data.taxableIncomeNew < 1275000) {
    suggestions.push("You're in the marginal relief zone. Every extra rupee you earn is taxed at 100% until you cross ~₹12.75L.");
  }

  return suggestions.slice(0, 4); // Max 4 suggestions
}
```

---

## 16. FAQ Content Matrix (Per Step)

### Global FAQs (Available on all steps)
- Q: "What's the difference between Old and New tax regime?"
  A: "The old regime has higher tax rates but allows many deductions like 80C, HRA, and home loan interest. The new regime has lower tax rates but almost no deductions."

- Q: "Which regime is the default?"
  A: "The new regime is the default from FY 2023-24 onwards. If you don't specifically choose the old regime, you'll be taxed under the new regime."

- Q: "Can I switch regimes every year?"
  A: "Yes, salaried individuals can choose either regime every financial year when filing their ITR."

- Q: "Is this calculator accurate?"
  A: "Yes, for the inputs and rules we cover. We don't calculate surcharge (income > ₹50L) or capital gains."

### Step-Specific FAQs (Documented in each step above)

---

## 17. Error Handling & Messaging

### 17.1 Validation Errors

| Scenario | Message | Severity |
|----------|---------|----------|
| Empty required field | "This field is required" | Error |
| Negative number | "Amount cannot be negative" | Error |
| In-hand > Gross | "Your in-hand salary can't be more than your gross salary. Please check." | Error |
| Rent > HRA by 10x | "Your rent seems very high compared to your HRA. Please verify." | Warning |
| 80C > ₹1.5L | "80C benefits are capped at ₹1.5 lakh. Extra investments won't reduce tax further." | Info |
| PT > ₹2,500 | "Professional tax is capped at ₹2,500/year by the Constitution." | Warning |
| Income > ₹50L | "Surcharge applies on income above ₹50 lakh. This app doesn't calculate it." | Warning |

### 17.2 System Errors

| Scenario | Message |
|----------|---------|
| Calculation overflow | "The numbers entered are too large. Please check your inputs." |
| Browser unsupported | "Please use a modern browser (Chrome, Firefox, Safari, Edge) for the best experience." |

---

## 18. Analytics & Tracking (Privacy-Preserving)

Since the app is privacy-first, analytics must be anonymous and optional:

- **No user identifiers:** No IP logging, no fingerprinting
- **Aggregate only:** Count of calculations, average time spent, drop-off points
- **Opt-in:** "Help us improve — allow anonymous usage stats" toggle
- **If opted in:** Send to privacy-friendly analytics (Plausible, Fathom, or self-hosted Matomo)
- **Events tracked:**
  - `wizard_started`
  - `step_completed` (with step number)
  - `calculation_completed`
  - `regime_recommended` (old/new/tie)
  - `result_shared` (if share feature added)

---

## 19. Accessibility Requirements

- **WCAG 2.1 AA Compliance**
- All inputs have associated `<label>` elements
- Error messages linked via `aria-describedby`
- Focus indicators visible on all interactive elements
- Color contrast ratio ≥ 4.5:1 for text
- Charts have aria-labels and data tables for screen readers
- Keyboard navigation: Tab order logical, Enter submits, Escape closes modals
- Reduced motion support: `@media (prefers-reduced-motion: reduce)`

---

## 20. Performance & Browser Support

### Performance Targets
- First Contentful Paint: < 1.5s
- Time to Interactive: < 3s
- Lighthouse Score: > 90
- Bundle size: < 200KB gzipped (excluding charts)

### Browser Support
- Chrome/Edge: Last 2 versions
- Firefox: Last 2 versions
- Safari: Last 2 versions
- Mobile browsers: iOS Safari, Chrome Android
- **Not supported:** Internet Explorer

### Optimization
- Lazy load result page components
- Code-split wizard steps
- Preload critical fonts and CSS
- Use `will-change` sparingly for animations

---

## 21. Appendix: Complete Tax Formula Reference

### 21.1 Old Regime Formula (Summary)

```
Gross Salary = Basic + DA + HRA + Special + LTA + Bonus + Other Allowances

Less Exemptions:
  HRA = min(HRA_Received, 50%/40% × (Basic+DA), Rent_Paid − 10% × (Basic+DA))
  LTA = min(LTA_Received, Actual_Travel_Cost) [if claimed]
  Children Education = ₹1,200 × min(children, 2)
  Hostel = ₹3,600 × min(children, 2)
  Professional Tax = min(PT_Paid, ₹2,500)

Less Standard Deduction = ₹50,000

Add/Less House Property:
  Self-occupied: −min(Home_Loan_Interest, ₹2,00,000)
  Rented out: −Home_Loan_Interest (full, but rental income to be added)

Add Other Sources:
  Savings Interest − min(Savings_Interest, ₹10,000) [80TTA]
  FD Interest [fully taxable for non-seniors]
  For seniors: (Savings + FD) − min(total, ₹50,000) [80TTB]

Gross Total Income = above result

Less Deductions:
  80C = min(sum of eligible investments, ₹1,50,000)
  80CCD(1B) = min(NPS_Additional, ₹50,000)
  80CCD(2) = min(Employer_NPS, 10% × (Basic+DA))
  80D = min(Self_Health, ₹25K/₹50K) + min(Parents_Health, ₹25K/₹50K)
  80EE = min(max(0, Interest − ₹2L), ₹50,000) [if eligible]
  80EEA = min(max(0, Interest − ₹2L), ₹1,50,000) [if eligible]
  80G = Donations × 50%

Taxable Income = max(0, Gross_Total_Income − Total_Deductions)

Tax = Apply slab rates based on age
Less Rebate 87A = min(Tax, ₹12,500) if Taxable_Income ≤ ₹5,00,000
Add Cess = 4% × (Tax − Rebate)
Total Tax = Tax − Rebate + Cess
```

### 21.2 New Regime Formula (Summary)

```
Gross Salary = Basic + DA + HRA + Special + LTA + Bonus + Other Allowances

Less Standard Deduction = ₹75,000

No exemptions for HRA, LTA, Professional Tax, Children Allowances

Add Other Sources:
  Savings Interest [fully taxable, no 80TTA]
  FD Interest [fully taxable, no 80TTB even for seniors]

Gross Total Income = Gross − ₹75,000 + Other_Sources

Less Deductions:
  Only 80CCD(2) = min(Employer_NPS, 14% × (Basic+DA))

Taxable Income = max(0, Gross_Total_Income − Employer_NPS_Deduction)

Tax = Apply new slab rates (same for all ages)

Rebate 87A:
  If Taxable_Income ≤ ₹12,00,000:
    Rebate = min(Tax, ₹60,000)
  Else if Taxable_Income ≤ ~₹12,75,000:
    Excess = Taxable_Income − ₹12,00,000
    If Tax > Excess:
      Rebate = Tax − Excess
    Else:
      Rebate = 0
  Else:
    Rebate = 0

Add Cess = 4% × (Tax − Rebate)
Total Tax = Tax − Rebate + Cess
```

### 21.3 Rebate 87A Comparison

| Regime | Max Rebate | Income Limit | Effective Zero-Tax Income |
|--------|------------|--------------|---------------------------|
| Old | ₹12,500 | ≤ ₹5,00,000 | ₹5,00,000 + ₹50,000 = ₹5,50,000* |
| New | ₹60,000 | ≤ ₹12,00,000 | ₹12,00,000 + ₹75,000 = ₹12,75,000 |

*For general category. Seniors: ₹5,00,000 + ₹50,000 = ₹5,50,000. Super seniors: ₹5,00,000 + ₹50,000 = ₹5,50,000.

---

## 22. Development Checklist

### Frontend
- [ ] Landing page with hero section and result mockup
- [ ] Wizard container with progress indicator
- [ ] All 10 steps with inputs and validations
- [ ] Live preview panel with real-time updates
- [ ] Step-specific FAQ components
- [ ] Result page with recommendation, comparison, charts, and suggestions
- [ ] Mobile responsive design
- [ ] Dark mode support (optional but recommended)

### Calculation Engine
- [ ] Old regime tax calculator
- [ ] New regime tax calculator
- [ ] HRA exemption calculator
- [ ] 80C, 80D, 80CCD calculators
- [ ] Home loan interest calculator (24b, 80EE, 80EEA)
- [ ] Marginal relief calculator
- [ ] Slab breakdown generator
- [ ] Edge case handling (all 12 cases)

### Testing
- [ ] Unit tests for all tax calculations
- [ ] Test cases for each age category
- [ ] Test cases for marginal relief
- [ ] Test cases for HRA metro/non-metro
- [ ] Test cases for 80EE/80EEA eligibility
- [ ] Cross-browser testing
- [ ] Mobile responsiveness testing
- [ ] Accessibility audit (axe-core)

### Content
- [ ] All FAQ content written and reviewed
- [ ] Suggestion engine logic implemented
- [ ] Educational content for result page
- [ ] Disclaimer and legal text

### Performance
- [ ] Bundle size < 200KB
- [ ] Lighthouse score > 90
- [ ] No external API calls
- [ ] Works offline (Service Worker optional)

---

*End of PRD*
