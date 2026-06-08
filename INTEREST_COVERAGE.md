# Credit Warning System — Data Extraction Specification

**Purpose:** Define exactly what the system extracts from SEC filings, why each metric matters, where it lives, and when it should trigger a warning.

## Section 1: Metric Definitions

For each metric, cover:

- **What it is**

- **Why it signals stress**

- **Formula**

- **Where it lives**

- **Structured** (XBRL tag) **or unstructured** (footnote → LLM)

- **Extraction fallback logic**

- **Stress threshold**

- **Signal timing**

- **Frequency**

Metrics to cover:

- **Leverage** — how much debt relative to earnings

- **Interest Coverage** — can they afford the interest payments

- **Free Cash Flow** — are they generating or burning cash

- **Liquidity** — do they have cash to cover near-term obligations

- **Debt Maturity Wall** — when does debt come due

- **Covenant Headroom** — how close are they to breaching loan conditions

- **Debt-to-Equity ratio** — overlaps heavily with leverage

- **Quick ratio / Current ratio** — overlaps with liquidity

- **EBITDA margin trend** — tells you if earnings quality is deteriorating, which feeds into leverage and coverage

- **Revenue trend** — declining revenue is an upstream signal before ratios blow up

- **Loss provisions / litigation contingencies** — the intern plan actually mentions this explicitly in Phase 1, so this one you should add

- **Asset coverage** — do assets cover debt if liquidated? More relevant for secured debt



## 2. Interest Coverage

**What it is**

Interest Coverage measures whether a company generates enough operating earnings to pay the interest on its debt. 

> It answers: "for every $1 of interest the company owes, how many dollars of earnings does it have to cover it?" A company with $100M of EBITDA and $25M of interest expense has a coverage ratio of 4.0x — it earns four times what it needs just to service interest.

Unlike leverage, which measures the stock of debt relative to earnings, interest coverage measures the flow 
> it is a direct test of whether this year's earnings are sufficient to meet this year's interest bill. A company can have high leverage and still be comfortable if interest rates are low and maturities are far away. Conversely, a company with moderate leverage can face acute stress if its debt carries high coupons or if EBITDA has fallen sharply, compressing coverage toward 1.0x.

The ratio sits at the intersection of two independent risk factors: 
**earnings quality** and **debt cost**. 

Either can move it — a revenue shock compresses the numerator; a refinancing at higher rates expands the denominator. When both move against the company simultaneously, coverage can deteriorate very rapidly.

Coverage is also the metric most directly connected to cash reality. 
> A company cannot defer interest payments the way it can defer capex or hiring. Interest is contractual and due on a fixed schedule. A coverage ratio below 1.0x means the company cannot pay its interest bill from operations — it must draw on cash reserves, sell assets, or borrow more just to stay current. That is the definition of a company in acute financial distress.


## Why it signals stress
> Appendix A

Interest coverage signals stress through three distinct mechanisms, each operating on a different timeline.

### Mechanism 1 — The contractual floor problem (immediate)

- Interest payments are not discretionary. A company that misses an interest payment is in technical default within the grace period specified in its indenture — typically 30 days for public bonds.
- Unlike dividends, which can be cut, or capex, which can be deferred, interest cannot be negotiated away unilaterally after the debt is issued.
- As coverage falls toward 1.0x, the company has essentially no margin for error.
- A single bad quarter — an unexpected cost, a revenue shortfall, a working capital swing — can push it from barely covering interest to not covering it at all.
- The ratio does not need to reach zero to trigger a crisis. It only needs to reach a point where the next quarterly variance could push it below 1.0x.
  **conclusion: no need to be close to 0, it has been dangerous in 1.0x**

### Mechanism 2 — The refinancing feedback loop (medium term)

- Lenders and rating agencies monitor interest coverage as a primary indicator of debt serviceability.
- When coverage deteriorates, the credit consequences arrive before the company actually misses a payment. Rating agencies downgrade. Bond prices fall.
- New borrowing becomes more expensive — which directly increases future interest expense, which further compresses coverage.
- This feedback loop is self-reinforcing: a falling coverage ratio raises the cost of the debt that is causing the coverage to fall.
- Companies that enter this loop at coverage of 2.0x–3.0x can find themselves at 1.5x within two quarters simply because the cost of refinancing maturing debt has risen, even if EBITDA has not changed.
   **conclusion: The refinancing feedback loop is self-reinforcing**

### Mechanism 3 — The earnings quality signal (early warning)

- A coverage ratio that is falling over time — even from a comfortable level — tells you that either earnings are deteriorating, interest costs are rising, or both. Both are upstream signals of broader credit stress.
- A company declining from 8.0x to 5.0x to 3.0x over six quarters is sending a consistent directional message even if the absolute level has not yet crossed into danger territory.
- The trend is the warning.
- Coverage can function as an early warning indicator — a company does not need to reach a distressed absolute level for the trend to be actionable.
  **conclusion: Coverage functions as a leading indicator**



**Formula**
> Appendix B
---

**Interest Coverage = EBIT / Interest Expense**

This is the base definition used across most rating agency methodologies and academic literature. It uses EBIT rather than EBITDA because interest is a financing cost that sits below operating income — EBIT represents what the company earned from operations before paying lenders, which is the most direct measure of ability to service debt.

However, in practice three formula versions are used depending on the analytical purpose, exactly as with leverage.

---

**Formula 1 — Automated Baseline (XBRL / Deterministic)**

```
Interest Coverage = EBIT / Interest Expense

EBIT = Operating Income
     = us-gaap:OperatingIncomeLoss

Interest Expense = us-gaap:InterestExpense
                 OR us-gaap:InterestAndDebtExpense (fallback)
```

**XBRL tags:**

| Input | Primary XBRL Tag | Fallback Tag |
|---|---|---|
| EBIT (Operating Income) | `us-gaap:OperatingIncomeLoss` | `us-gaap:IncomeLossFromContinuingOperationsBeforeIncomeTaxes` |
| Interest Expense | `us-gaap:InterestExpense` | `us-gaap:InterestAndDebtExpense` |

**What is excluded:** D&A addback, interest income netting, capitalized interest adjustment, lease interest component. Purely what the structured filing reports.

**Error handling:** If either input tag is missing, mark the ratio null and log which tag failed. Never silently compute with a zero substitute. If Interest Expense = 0 and the company carries visible debt, flag as "interest expense tag missing or zero — verify company has no debt before accepting."

**Rule:** If Interest Expense = 0 but Total Debt (from Leverage Formula 1 extraction) > 0, then:
1. Mark Interest Coverage = null (do not compute 0 or infinite)
2. Log: "Interest expense reported as zero but debt exists — possible netting or tag error"
3. Escalate to manual review queue

**Important note on EBIT vs EBITDA for coverage:**
Some practitioners use EBITDA/Interest Expense rather than EBIT/Interest Expense, arguing that D&A is non-cash and therefore available to service interest. Both versions are valid — they answer slightly different questions. EBIT/Interest asks "do operating earnings cover interest?" EBITDA/Interest asks "does operating cash generation cover interest?" For Formula 1, EBIT is preferred because it is directly tagged and requires no derivation. EBITDA coverage is computed separately as a supplementary figure.



**Note on EBIT definition:** For Formula 1, EBIT is defined as `us-gaap:OperatingIncomeLoss`. The system does not attempt to add back non-operating income or expense. This is the cleanest XBRL-based definition and matches the most common rating agency starting point.

---

**Formula 2 — Moody's-Style (Adjusted, requires LLM footnote extraction)**

```
Interest Coverage = Moody's Adjusted EBIT / Moody's Adjusted Interest Expense

Moody's Adjusted EBIT =
    Operating Income
  + Pension service cost reclassification
  + Operating lease depreciation component (2/3 of annual rent expense)
  − Non-recurring gains (if any)
  [Does NOT add back restructuring charges]

Moody's Adjusted Interest Expense =
    Reported Interest Expense
  + Pension interest cost reclassification
    (excess pension expense over service cost, reclassified from operating to interest)
  + Operating lease interest component (1/3 of annual rent expense)
  − Capitalized interest (if any — not yet cash paid)
```

**What LLM must extract from footnotes:**
- Annual operating rent expense (pre-ASC 842) or ROU lease liability (post-ASC 842) — same as Leverage Formula 2
- Pension service cost and total pension expense — same as Leverage Formula 2
- Capitalized interest amount (disclosed in PP&E footnote or interest expense footnote)

**Net effect vs Formula 1:** Moody's adjusted interest expense is typically higher than reported interest expense because it adds the lease interest component and pension interest reclassification. Adjusted EBIT is also typically higher. The net direction of the coverage ratio depends on which adjustment dominates — for heavily leased businesses, the interest expense increase typically outweighs the EBIT increase, compressing coverage.

---

**Formula 3 — S&P-Style (Conservative, requires LLM footnote extraction)**

S&P uses a fixed charge coverage ratio rather than pure interest coverage, which is more conservative because it includes lease payments as a fixed charge alongside interest.

```
Fixed Charge Coverage = S&P Adjusted EBITDA / Fixed Charges

S&P Adjusted EBITDA =
    Revenue
  − Operating Expenses (as reported)
  + D&A
  [Restructuring charges: NOT added back]
  [Stock-based compensation: NOT added back]
  [Management fees: NOT added back]

Fixed Charges =
    Interest Expense (gross, not netted against interest income)
  + Operating Lease Payments (current year)
  + Preferred Dividends (if any)
  [Capitalized interest: included — S&P treats it as a real cost]
```

Source: S&P Corporate Methodology: Ratios and Adjustments (maalot.co.il); S&P defines fixed charge coverage as a key ratio alongside Debt/EBITDA in the financial risk profile assessment.

**Key difference from Moody's:** S&P includes the full operating lease payment in fixed charges (not just the interest component) and does not add lease depreciation back to EBIT. This makes S&P's fixed charge coverage consistently lower than Moody's interest coverage for leased businesses — S&P is more conservative on this metric as it is on leverage.

**What LLM must extract from footnotes:**
- Current year operating lease payments (from Lease footnote maturity table — the amount due within 12 months)
- Preferred dividend amount (from equity section or dividend footnote — if applicable)
- Capitalized interest (from PP&E footnote or interest expense footnote)



**Implementation guidance**

Formula 1 is the automated baseline for Phase 2. Both EBIT and Interest Expense are among the most reliably tagged items in XBRL — this ratio has fewer extraction challenges than leverage. The main failure mode is companies that net interest income against interest expense and report only a net figure, which understates gross interest expense and overstates coverage. Flag any case where `us-gaap:InterestExpense` appears lower than expected relative to the company's total debt load.

Formulas 2 and 3 are reserved for Phase 3. Once implemented, the divergence between Formula 1 coverage and Formula 3 fixed charge coverage for the same company is itself an analytical signal — a large gap indicates the company has significant lease obligations that are not reflected in the headline interest coverage number.

---

**Where it lives**

---

**Formula 1 — Automated Baseline**

All items are structured, on the face of financial statements, available in both 10-K and 10-Q.

| Input | Financial Statement | Exact Line Item | Available In |
|---|---|---|---|
| EBIT (Operating Income) | Income Statement | "Operating income" or "Income from operations" | 10-K and 10-Q |
| Interest Expense | Income Statement — below operating income, in the "Other income / expense" section | "Interest expense" or "Interest and debt expense" | 10-K and 10-Q |

**Note on Interest Expense location:** Unlike operating income which sits in the operating section, interest expense lives below the operating income line in the non-operating / other income and expense section. In the XBRL document it is tagged separately from operating items. Some companies present a subtotal called "Total other expense, net" that bundles interest expense with other non-operating items — if this is the only tag available, LLM extraction from the income statement footnote is required to isolate interest expense alone.

**Note on interest income netting:** Some companies — particularly those with large cash balances like Apple or Microsoft — report only net interest (interest expense minus interest income) as a single line item rather than gross interest expense. This overstates coverage because it reduces the denominator. Your system must detect this and flag it. The signal is: `us-gaap:InterestExpense` is absent but `us-gaap:InterestIncomeExpenseNet` is present. When this occurs, gross interest expense must be reconstructed from the interest footnote via LLM.

> **Phase 2 rule for bundled interest expense:**
If `us-gaap:InterestExpense` is absent and only `us-gaap:InterestIncomeExpenseNet` or a bundled "Other expense, net" line exists:
- Mark Interest Coverage = null
- Log: "interest expense cannot be isolated from net interest or other non-operating items — LLM required for Phase 3"
- Do not compute a ratio using the net figure (it would overstate coverage)

> **Phase 3 rule:**
Use LLM to extract gross interest expense from the interest footnote or income statement footnote, then recompute coverage.

---

**Formula 2 — Moody's-Style Adjustments**

All adjustment items are unstructured — they require LLM extraction from footnotes. All items below are in addition to the Formula 1 base inputs.

**Item 2a — Capitalized Interest**

| Field | Detail |
|---|---|
| Where it lives | PP&E Footnote (typically Note 4–7, titled "Property, Plant and Equipment") or the Interest Expense footnote if one exists separately |
| Available in | 10-K only in most cases; occasionally in 10-Q for capital-intensive companies mid-construction |
| Exact location within footnote | Look for a sentence or table disclosing "capitalized interest" or "interest capitalized during the period." It typically appears as a reconciliation: gross interest incurred minus capitalized interest equals interest expense charged to income. |
| What LLM should extract | Single number: total interest capitalized during the period. Moody's subtracts this from reported interest expense because capitalized interest has not yet been charged to the income statement — it is not a current cash obligation against earnings. |

**Item 2b — Pension Interest Cost Reclassification**

| Field | Detail |
|---|---|
| Where it lives | Pension / Employee Benefits Footnote (same note as described in Leverage Formula 2 — typically Note 8–12) |
| Available in | 10-K only |
| Exact location within footnote | The "Net Periodic Benefit Cost" table. Look for the line items "Interest cost" and "Service cost" within that table. Moody's reclassifies the excess of total pension expense over service cost — including the interest cost component — from operating expense to interest expense. |
| What LLM should extract | Two numbers from the Net Periodic Benefit Cost table: (1) Service cost, (2) Interest cost. The interest cost component is added to the interest expense denominator under Moody's adjusted methodology. |

**Item 2c — Operating Lease Interest Component**

| Field | Detail |
|---|---|
| Where it lives | Lease Footnote (same note as described in Leverage Formula 2 — typically Note 5–8) |
| Available in | 10-K and 10-Q (post-ASC 842) |
| Exact location within footnote | The "Lease Cost" table in the Lease Footnote, which breaks down total lease cost into: operating lease cost, finance lease interest cost, and finance lease amortization. For operating leases, the interest component is not separately stated — Moody's estimates it as 1/3 of total annual operating lease expense. |
| What LLM should extract | Total annual operating lease expense (same figure used in Leverage Formula 2). The system then applies the 1/3 rule to derive the interest component for the denominator adjustment. |

---

**Formula 3 — S&P-Style Adjustments**

**Item 3a — Current Year Operating Lease Payments (for Fixed Charges denominator)**

| Field | Detail |
|---|---|
| Where it lives | Lease Footnote — maturity schedule table |
| Available in | 10-K and 10-Q |
| Exact location within footnote | The operating lease maturity table shows future minimum lease payments by year. The first row — "Less than 1 year" or "2025" (the next fiscal year) — is the current year payment used in S&P's fixed charges denominator. This is the full lease payment, not just the interest component, which is what distinguishes S&P from Moody's on this item. |
| What LLM should extract | Single number: operating lease payments due within the next 12 months from the maturity schedule. |

**Item 3b — Preferred Dividends (for Fixed Charges denominator)**

| Field | Detail |
|---|---|
| Where it lives | Equity section of the Balance Sheet AND Dividends footnote or Equity footnote (typically Note 10–15, titled "Stockholders' Equity" or "Capital Stock") |
| Available in | 10-K and 10-Q |
| Exact location | Income statement may show "Preferred stock dividends" as a deduction from net income attributable to common shareholders. Alternatively, the equity footnote discloses dividend rate and shares outstanding, from which the annual preferred dividend can be computed. |
| What LLM should extract | Total preferred dividends paid or declared during the period. For most corporate bond issuers this will be zero — flag if non-zero as it meaningfully reduces S&P fixed charge coverage. |
| XBRL tag if available | `us-gaap:PreferredStockDividendsAndOtherAdjustments` — semi-structured; verify against footnote |

**Item 3c — Gross vs Net Interest Expense Confirmation**

| Field | Detail |
|---|---|
| Where it lives | Interest Expense footnote or Note 1 (Summary of Significant Accounting Policies) |
| Available in | 10-K and 10-Q |
| Exact location | Note 1 or a standalone interest footnote will describe the company's policy on presenting interest income and expense — whether gross or net. S&P uses gross interest expense. If the company reports only net interest, LLM must extract gross interest expense from the footnote breakdown. |
| What LLM should extract | Confirmation of gross vs net presentation. If net: extract (1) gross interest expense and (2) interest income separately so the system can use gross interest in the denominator. |

---















## Appendix A
### Academic and institutional validation

- Moody's explicitly states that interest coverage — measured as EBIT/Interest Expense — is one of its primary financial metrics across virtually all non-financial corporate rating methodologies, alongside leverage.
- S&P's Corporate Methodology treats fixed charge coverage as a direct input into the financial risk profile assessment, alongside Debt/EBITDA, and assigns lower financial risk profile scores as coverage declines toward and below 2.0x.
- Altman's original Z-score model (1968) included EBIT/Total Assets as one of its five discriminating variables, capturing the same earnings-relative-to-obligations logic that coverage embeds.

### The Rite Aid confirmation

- As validated in the Leverage section, Rite Aid's EBITDA fell from $505.9M in fiscal 2022 to $429.2M in fiscal 2023 — a $76.7M decline.
- Over the same period, its interest expense remained elevated because its debt load had not meaningfully decreased.
- The result was a coverage ratio compressing toward and eventually below 1.0x in the quarters leading up to its October 2023 bankruptcy.
- The coverage signal was visible in the same quarterly filings that showed leverage deteriorating — confirming that the two metrics move together in distress but that coverage often deteriorates faster because interest expense is sticky while EBITDA can fall quickly.



## Appendix B


```
Supplementary: EBITDA Coverage = (Operating Income + D&A) / Interest Expense
```

---

**Comparison Table**

| | Formula 1 — Baseline | Formula 2 — Moody's | Formula 3 — S&P |
|---|---|---|---|
| **Numerator** | EBIT (Operating Income) | Adjusted EBIT | Adjusted EBITDA |
| **Denominator** | Interest Expense | Adjusted Interest Expense | Fixed Charges (interest + lease payments + preferred dividends) |
| **D&A in numerator** | ❌ No | ❌ No | ✅ Yes (EBITDA base) |
| **Lease payments in denominator** | ❌ No | Partial (1/3 of rent as interest) | ✅ Full lease payment |
| **Restructuring addback** | n/a | ❌ No | ❌ No |
| **Capitalized interest excluded** | ❌ Not adjusted | ✅ Subtracted from interest | ✅ Included as fixed charge |
| **Requires LLM** | ❌ No | ✅ Yes | ✅ Yes |
| **Relative coverage level** | Mid | Higher than F1 for most companies | Lower than F2 |
| **Implementation phase** | Phase 2 | Phase 3 | Phase 3 |

---


## Appendix C

**Summary Table — Where Each Item Lives**

| Item | Formula | Statement / Document | Section | 10-K Only or Both |
|---|---|---|---|---|
| Operating Income (EBIT) | F1 | Income Statement | Operating section | Both |
| Interest Expense | F1 | Income Statement | Non-operating / Other expense section | Both |
| Capitalized interest | F2 | PP&E Footnote | Capitalized interest disclosure | 10-K only (usually) |
| Pension interest cost | F2 | Pension Footnote | Net Periodic Benefit Cost table | 10-K only |
| Operating lease interest component | F2 | Lease Footnote | Lease Cost table | Both |
| Current year lease payments | F3 | Lease Footnote | Maturity schedule — Year 1 row | Both |
| Preferred dividends | F3 | Income Statement + Equity Footnote | Below net income / Equity note | Both |
| Gross interest confirmation | F3 | Note 1 or Interest Footnote | Accounting policies or interest breakdown | Both |


