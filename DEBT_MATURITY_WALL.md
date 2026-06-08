# Debt Maturity Wall

## What it is

### Core Definition

The Debt Maturity Wall measures the schedule of when a company's debt obligations come due — mapping every tranche of debt to the year or quarter in which principal repayment is required. It answers: *"how much debt does this company need to repay or refinance, and when?"*

### Not a Ratio — A Schedule

Unlike every other metric in this spec, the Debt Maturity Wall is **not a ratio**. It does not produce a single number that can be compared to a threshold. It produces a **schedule** — a time series of future cash obligations — that must be interpreted in the context of the company's current liquidity, projected FCF, and capital market access.

| Scenario | Position |
|----------|----------|
| $5B debt maturing in year 5 | Time to improve position |
| $5B debt maturing in year 1 | Immediate existential test |

Same total debt. Same leverage ratio. Completely different risk profile.

### The "Wall" Metaphor

When debt maturities are plotted as a bar chart by year, companies with concentrated maturities in a single year show a tall isolated bar — a **wall** — that represents a moment when the company must either:

- Generate enough cash internally, OR
- Successfully refinance a large amount of debt in the capital markets simultaneously

If that moment arrives during a period of market stress, elevated credit spreads, or company-specific deterioration, **the wall becomes a cliff**.

### Two Risk Dimensions

The maturity wall captures two distinct risk dimensions that no other metric in this spec addresses directly.

| Risk Dimension | Description |
|----------------|-------------|
| **Refinancing risk** | The risk that when debt matures, the company cannot refinance it on acceptable terms or at all. Low when maturities are spread across many years, markets are open, and credit quality is strong. High when maturities are concentrated, markets are tight, or credit quality has deteriorated. |
| **Interest rate reset risk** | The risk that when fixed-rate debt is refinanced, it must be replaced at a higher coupon rate — mechanically increasing interest expense and compressing coverage even if the business itself has not deteriorated. In a rising rate environment, refinancing becomes more expensive, reducing future coverage ratios. |

**Cross-metric signal:** The interaction between the maturity wall and the interest coverage metric is one of the most important cross-metric signals in the system.

### Data Source

The maturity wall is extracted primarily from the **debt footnote** in 10-K and 10-Q filings, where companies are required to disclose the schedule of future principal payments under long-term debt arrangements.

| Disclosure Format | Description |
|-------------------|-------------|
| Table structure | Amounts due in each of the next **five fiscal years** and the aggregate amount due thereafter |

**XBRL limitation:** The total debt is tagged, but the **year-by-year breakdown is not**. This makes the maturity wall one of the primary use cases for the LLM extraction layer in Phase 3.

### Phase 2 vs Phase 3

| Phase | What the System Captures | Limitation |
|-------|--------------------------|-------------|
| **Phase 2** | Only the current portion of long-term debt — the amount maturing within 12 months that has been reclassified to current liabilities on the balance sheet | Tells you what is due immediately, but **nothing about the shape of the wall in years 2 through 5** |
| **Phase 3** | Full maturity schedule extracted by LLM from the debt footnote | Analytically complete picture |


## Why it signals stress

The debt maturity wall signals stress through four mechanisms, each operating on a different timeline and with a different interaction with the other metrics in this spec.

---

### Mechanism 1 — The refinancing imperative (timeline-dependent)

Every debt maturity creates a refinancing imperative — the company must either repay the maturing debt from internal cash generation or replace it with new debt.

**Normal conditions:** Routine treasury management. Capital markets are open, credit quality is acceptable, new debt replaces maturing debt at similar terms.

**Stress conditions — three conditions converge simultaneously:**

| Condition | Description |
|-----------|-------------|
| 1. Maturity is large relative to available liquidity and projected FCF | Internal repayment not feasible; company entirely dependent on market access |
| 2. Credit quality has deteriorated | Lenders demand higher spreads, tighter covenants, or refuse to lend |
| 3. Capital markets are tight | Even creditworthy borrowers face elevated costs and reduced access |

> None of these three conditions alone creates a crisis. **All three together, arriving at the moment a large maturity comes due** — distressed refinancing, covenant-driven default, or bankruptcy.

**The maturity wall metric identifies condition 1.** Leverage and coverage identify condition 2. Market conditions are external to filing data.

**The timeline-dependent signal:**

| Time to Maturity | Alert Level |
|------------------|-------------|
| 36 months | Monitoring item |
| 18 months | Flag |
| 12 months | Stress |
| 6 months | Critical |

> Time is the primary variable that converts a future risk into an immediate one.

---

### Mechanism 2 — The concentration effect (structural risk)

The shape of the maturity wall matters as much as its total size.

| Scenario | Risk Level |
|----------|-----------|
| $3B debt spread evenly across 6 years ($500M/year) | Manageable annual refinancing |
| $3B debt all maturing in a single year | May exceed market absorption or lender capacity |

**Why concentration creates structural risk:**

| Maturity Profile | Flexibility |
|------------------|-------------|
| Staggered maturities | Can refinance early in favourable years, wait out difficult periods — has time and flexibility |
| Concentrated wall | Must refinance on whatever terms the market offers at the moment debt matures — **no flexibility** |

**Interaction with Leverage:**
- Highly leveraged company (Aggressive or Highly Leveraged band) + concentrated maturity wall = compounding risk
- Credit quality makes refinancing expensive; lack of flexibility means cannot wait for better conditions
- This combination is most commonly associated with **distressed exchanges and pre-packaged bankruptcies**

**The concentration signal:**

| Single-Year Maturities % of Total Debt | Signal |
|----------------------------------------|--------|
| > 30% | Worth flagging — refinancing burden disproportionate to annual management capacity |
| > 50% | Severe concentration |

---

### Mechanism 3 — The interest rate reset amplifier (medium term)

When fixed-rate debt matures and is refinanced at higher market rates, interest expense increases mechanically — not because the business deteriorated, but because the cost of debt rose.

**This effect directly compresses interest coverage ratio** even if EBITDA is unchanged.

**Three variables determine amplification:**

| Variable | Description |
|----------|-------------|
| Size of maturing tranche | Larger tranche = larger impact |
| Coupon vs current market rate gap | Larger gap = larger impact |
| Proportion of total interest expense | Higher proportion = larger impact |

**Example:**
- Company refinances $500M of 3% bonds at 7%
- Adds $20M of annual interest expense
- With $200M EBITDA, coverage drops from 4.0x to approximately 3.0x
- **No operational deterioration required**

**System capability (Phase 3):** Combine maturity wall schedule (tranche-level coupon rate and maturity date) with current market spreads. Projected post-refinancing coverage ratio — one of the most valuable forward-looking signals.

**The amplifier signal:**
Any tranche maturing within 18 months with a coupon rate **more than 200 basis points below** the issuer's current market spread. Identifies bonds where refinancing will materially increase interest expense and compress coverage.

---

### Mechanism 4 — The lender confidence signal (early warning)

How a company manages its maturity wall **in advance** reveals lender confidence information not available from any balance sheet metric.

| Behavior | Signal |
|----------|--------|
| Refinances 12–18 months ahead of maturity | Capital markets are open; lenders willing to commit at acceptable terms |
| Cannot refinance until forced at maturity | Lender confidence is limited |
| Accepts onerous terms (high spreads, tight covenants, shorter maturities) | Reveals limited lender confidence |

**Visible in your system through:** 424B prospectus filings (new bond issuances) and 8-K Item 2.03 filings (new credit facilities).

**The most alarming version — short-dated refinancing:**

| Before | After | Signal |
|--------|-------|--------|
| 7-year bond | 2-year term loan | Nominal maturity extended but wall merely pushed forward; market willing to lend but only short-term, reflecting uncertainty about medium-term credit trajectory |

**The lender confidence signal:**
**Average debt maturity declining across consecutive annual 10-K filings.**

Example: 6 years (2021) → 4.5 years (2022) → 3 years (2023)

> The company is not resolving its maturity wall — it is **compressing it**. Each refinancing pushes the wall closer rather than further away.

---

### Interaction with Other Metrics

The debt maturity wall does not generate isolated stress signals — it **amplifies and contextualises** signals from every other metric in this spec.

| Metric | Interaction |
|--------|-------------|
| **Leverage** | High leverage + near-term maturity wall = refinance large debt load when credit quality already impaired — worst possible combination for refinancing terms |
| **Coverage** | Near-term maturity wall + deteriorating coverage = even if refinancing achieved, new debt carries higher coupon, further compressing coverage in self-reinforcing loop |
| **FCF** | Negative FCF + near-term maturity wall = eliminates internal repayment option; entirely dependent on capital market access exactly when cash generation is insufficient |
| **Liquidity** | Available liquidity below size of near-term maturity = most direct and quantifiable definition of near-term default risk — company literally does not have cash to repay what is due |
| **Covenant Headroom** | Covenant breach that accelerates debt = extreme version of maturity wall event where entire debt stack becomes current simultaneously |

---

### Institutional Validation

| Source | Content |
|--------|---------|
| **Moody's** | Debt maturity analysis is core to liquidity assessment; near-term maturities relative to available liquidity are primary determinant of SGL rating |
| **S&P** | Liquidity descriptor framework explicitly requires assessment of debt maturities in Year 1 and Year 2 as inputs to sources-and-uses calculation |
| **Empirical evidence** | Companies with >40% of debt maturing within two years default at materially higher rates than those with staggered profiles — holding leverage and coverage constant |

> The maturity wall provides information about default risk that is **not captured by the other metrics**.

---

### Summary of Debt Maturity Wall Alert Signals

| Mechanism | Signal | Timeline | Alert Level |
|-----------|--------|----------|-------------|
| M1 — Refinancing imperative | Large maturity approaching with limited resources | 18 mo → Flag; 12 mo → Stress; 6 mo → Critical | Timeline-dependent |
| M2 — Concentration | >30% of debt maturing in single year | Any | Flag |
| M2 — Concentration | >50% of debt maturing in single year | Any | Severe concentration |
| M3 — Interest rate reset | Tranche maturing within 18 months with coupon >200bps below market | 18 months | Forward-looking signal (Phase 3) |
| M4 — Lender confidence | Average debt maturity declining across consecutive years | Annual | Stress indicator |

## Formula

---

**The Debt Maturity Wall is not a single ratio — it is a structured schedule combined with four derived analytical outputs.**

Unlike all prior metrics which produce one or more ratios from financial statement inputs, the maturity wall's primary output is a time series — a year-by-year table of principal repayments. The four derived outputs are computed from this schedule in combination with inputs already extracted for other metrics. This makes the maturity wall the most structurally different metric in the spec and the one with the sharpest Phase 2 vs Phase 3 capability gap.

---

**Formula 1 — Automated Baseline (XBRL / Deterministic)**

Phase 2 can extract only one component of the maturity wall from structured XBRL — the amount maturing within the next 12 months that has been reclassified to current liabilities.

```
Phase 2 Maturity Capture:

Near-Term Maturity (12 months) =
    us-gaap:LongTermDebtCurrent
  + us-gaap:ShortTermBorrowings
  + us-gaap:CommercialPaper

Maturity Coverage Ratio =
    Available Liquidity (from Liquidity metric)
    / Near-Term Maturity

Where Available Liquidity =
    Cash & Equivalents
  + Short-Term Investments
  (Phase 2 — revolver excluded)
  (Phase 3 — revolver included)
```

**What Phase 2 cannot capture:** The maturity schedule beyond 12 months. Years 2 through 5 and the thereafter bucket are disclosed in the debt footnote as a table but are not tagged in XBRL at the individual year level. Phase 2 therefore sees only the immediate maturity obligation — the first bar of the wall — without the full wall shape.

**XBRL tags for Formula 1:**

| Input | Primary XBRL Tag | Fallback Tag |
|---|---|---|
| Current portion of LT debt | `us-gaap:LongTermDebtCurrent` | `us-gaap:DebtCurrent` |
| Short-term borrowings | `us-gaap:ShortTermBorrowings` | `us-gaap:CommercialPaper` |
| Commercial paper | `us-gaap:CommercialPaper` | `us-gaap:NotesPayableCurrent` |
| Cash (for coverage ratio) | `us-gaap:CashAndCashEquivalentsAtCarryingValue` | `us-gaap:CashCashEquivalentsAndShortTermInvestments` |
| Short-term investments | `us-gaap:ShortTermInvestments` | `us-gaap:MarketableSecuritiesCurrent` |

**Error handling:** If LongTermDebtCurrent is null, Near-Term Maturity = sum of short-term components only — flag: "current portion of LT debt not found — near-term maturity figure captures short-term borrowings only; may be materially understated." If all components null → Near-Term Maturity = null → Maturity Coverage Ratio = null.

---

**Formula 2 — Full Maturity Schedule (LLM extraction, Phase 3)**

The complete maturity wall requires LLM extraction of the debt footnote maturity table. This is the analytically complete version of the metric.

```
Full Maturity Schedule:

Raw extraction from Debt Footnote maturity table:
Year 1:  $X million  (next fiscal year)
Year 2:  $X million
Year 3:  $X million
Year 4:  $X million
Year 5:  $X million
After:   $X million  (aggregate beyond year 5)
Total:   $X million  (sum — verify equals total debt)

Derived Output A — Maturity Concentration Ratio:
Concentration(year n) =
    Maturities in year n / Total Debt Outstanding
Flag if any single year > 30% of total debt

Derived Output B — Near-Term Maturity Ratio:
Near-Term Ratio =
    (Year 1 + Year 2 Maturities) / Total Debt
Flag if > 40% — severe near-term concentration

Derived Output C — Weighted Average Debt Maturity (WADM):
WADM =
    Σ (Year n Maturity × Years to Maturity n)
    / Total Debt

Where Years to Maturity:
Year 1 = 1.0
Year 2 = 2.0
Year 3 = 3.0
Year 4 = 4.0
Year 5 = 5.0
After  = 7.0 (convention — midpoint of years 6–8)

Flag if WADM < 3.0 years — short average maturity
Flag if WADM declining across consecutive annual filings
— maturity wall compressing over time

Derived Output D — Liquidity-to-Maturity Coverage:
Full Coverage =
    Available Liquidity (cash + revolver, Phase 3)
    / Year 1 Maturities

Extended Coverage =
    Available Liquidity + Projected Annual FCF
    / (Year 1 + Year 2 Maturities)

Flag if Full Coverage < 1.0x — cannot cover
Year 1 maturities from available liquidity alone
Flag if Extended Coverage < 1.0x — cannot cover
two-year maturities from liquidity plus FCF
```

---

**Formula 3 — Tranche-Level Analysis (LLM extraction, Phase 3)**

The most granular level of maturity wall analysis requires tranche-by-tranche extraction — identifying each individual debt instrument, its maturity date, principal amount, coupon rate, and instrument type. This enables the interest rate reset analysis described in Why it signals stress Mechanism 3.

```
Tranche-Level Data Structure:

For each debt tranche identified in Debt Footnote:
{
  instrument_type:    "Senior Notes" / "Term Loan" /
                      "Revolving Credit" / "Convertible Notes" /
                      "Subordinated Notes" / other
  principal_amount:   $X million (face value)
  maturity_date:      YYYY-MM-DD
  coupon_rate:        X.XX% (fixed) or
                      "SOFR + X.XX%" (floating)
  coupon_type:        "fixed" / "floating"
  currency:           USD / EUR / other
  secured:            true / false
  callable:           true / false
  call_date:          YYYY-MM-DD (if callable)
}

Derived Output E — Projected Interest Rate Reset Impact:
For each tranche maturing within 18 months:

Current annual interest = Principal × Coupon Rate
Estimated new coupon = Current market spread
                       for this issuer's credit quality
                       (requires external market data
                       — not from filing)
Estimated new annual interest = Principal × New Coupon
Interest rate reset impact = New − Current (annual)

Cumulative reset impact across all tranches
maturing within 18 months:
Total annual interest increase = Σ reset impacts

Projected coverage post-reset =
    Current EBITDA /
    (Current Interest Expense + Total annual increase)

Flag if projected coverage post-reset crosses
a threshold band relative to current coverage:
Coverage compression ≥ 0.5x → Watch
Coverage compression ≥ 1.0x → Flag
Coverage compression ≥ 1.5x → Stress

Derived Output F — Refinancing Tenor Analysis:
Track average tenor of new debt issued vs
average tenor of debt retired over trailing
four quarters:
New issuance tenor = weighted average maturity
                     of 424B and 8-K Item 2.03
                     filings in trailing 4 quarters
Retired debt tenor = weighted average remaining
                     maturity of debt repaid

If new issuance tenor < retired debt tenor
for two consecutive annual periods:
Flag: "average debt maturity shortening —
possible lender confidence deterioration;
maturity wall compressing"
```

---

**Companion Metric — Days to Nearest Maturity**

```
Days to Nearest Maturity =
    Calendar days from filing date to
    earliest debt maturity date

(Requires tranche-level maturity dates from
Formula 3 extraction — Phase 3 only)

Phase 2 approximation:
If LongTermDebtCurrent > 0:
   Nearest maturity is within 365 days
   Exact date unknown — flag as "within 12 months"
If LongTermDebtCurrent = 0:
   No maturity within 12 months from balance sheet
   Exact next maturity date unknown in Phase 2

Phase 3:
   Exact days computed from tranche-level dates
   Alert thresholds:
   > 365 days:  No immediate alert
   180–365 days: Watch — maturity approaching
   90–180 days:  Flag — refinancing window narrowing
   30–90 days:   Stress — imminent maturity
   < 30 days:    Critical — maturity imminent;
                 refinancing or repayment must
                 be confirmed or underway
```

---

**Comparison Table**

| | Formula 1 — Phase 2 Baseline | Formula 2 — Full Schedule | Formula 3 — Tranche Level |
|---|---|---|---|
| **Primary output** | Near-term maturity (12M) + coverage ratio | Year-by-year schedule + 4 derived outputs | Per-tranche data + interest rate reset + tenor analysis |
| **Maturity horizon** | 12 months only | Years 1–5 + thereafter | Exact dates per tranche |
| **Concentration analysis** | ❌ No | ✅ Yes | ✅ Yes (by instrument type) |
| **WADM computation** | ❌ No | ✅ Yes (approximate) | ✅ Yes (exact) |
| **Interest rate reset** | ❌ No | ❌ No | ✅ Yes (requires market data) |
| **Tenor trend analysis** | ❌ No | ❌ No (annual only) | ✅ Yes (quarterly) |
| **Requires LLM** | ❌ No | ✅ Yes | ✅ Yes (extensive) |
| **Requires market data** | ❌ No | ❌ No | ✅ Yes (current spreads) |
| **Implementation phase** | Phase 2 | Phase 3 | Phase 3 |

---

**Implementation Guidance**

Formula 1 is the Phase 2 baseline. It captures the most urgent component of the maturity wall — what is due within 12 months — and combines it with the liquidity metric's cash figure to produce a Maturity Coverage Ratio. This is a narrow but actionable signal: a Maturity Coverage Ratio below 1.0x means the company cannot cover its near-term debt maturities from cash alone, which is a direct and unambiguous liquidity stress signal that requires no further analytical judgement.

Formula 2 is the Phase 3 primary target. The full maturity schedule — available in every 10-K debt footnote — is the most valuable piece of credit information the LLM layer will extract. The four derived outputs (Concentration Ratio, Near-Term Ratio, WADM, Liquidity-to-Maturity Coverage) together give a complete picture of refinancing risk that is unavailable from any other source. The WADM trend across consecutive annual filings is particularly valuable — it is a slow-moving signal that requires no threshold calibration and is unambiguous in its interpretation: a declining WADM means the debt structure is becoming more fragile.

Formula 3 is a Phase 3 enhancement that unlocks the most forward-looking analytical capability in this spec — projecting future coverage compression from interest rate resets before they occur. It requires both LLM extraction and external market data (current spread levels for the issuer), making it the most complex computation in the entire system. It should be implemented after Formula 2 is validated and stable.

The maturity wall's primary Section 6 deviation — noted in the earlier discussion of implementation parameters — is that its output is a schedule and a set of derived metrics rather than a single ratio. The data storage schema must accommodate a variable-length array of maturity data points per issuer per filing rather than a fixed set of ratio fields. This is the most significant schema difference between the maturity wall and all other metrics in this spec.


