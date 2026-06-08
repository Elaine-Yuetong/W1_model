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

Interest coverage signals stress through three distinct mechanisms, each operating on a different timeline.

### Mechanism 1 — The contractual floor problem (immediate)

- Interest payments are not discretionary. A company that misses an interest payment is in technical default within the grace period specified in its indenture — typically 30 days for public bonds.
- Unlike dividends, which can be cut, or capex, which can be deferred, interest cannot be negotiated away unilaterally after the debt is issued.
- As coverage falls toward 1.0x, the company has essentially no margin for error.
- A single bad quarter — an unexpected cost, a revenue shortfall, a working capital swing — can push it from barely covering interest to not covering it at all.
- The ratio does not need to reach zero to trigger a crisis. It only needs to reach a point where the next quarterly variance could push it below 1.0x.

### Mechanism 2 — The refinancing feedback loop (medium term)

- Lenders and rating agencies monitor interest coverage as a primary indicator of debt serviceability.
- When coverage deteriorates, the credit consequences arrive before the company actually misses a payment. Rating agencies downgrade. Bond prices fall.
- New borrowing becomes more expensive — which directly increases future interest expense, which further compresses coverage.
- This feedback loop is self-reinforcing: a falling coverage ratio raises the cost of the debt that is causing the coverage to fall.
- Companies that enter this loop at coverage of 2.0x–3.0x can find themselves at 1.5x within two quarters simply because the cost of refinancing maturing debt has risen, even if EBITDA has not changed.

### Mechanism 3 — The earnings quality signal (early warning)

- A coverage ratio that is falling over time — even from a comfortable level — tells you that either earnings are deteriorating, interest costs are rising, or both. Both are upstream signals of broader credit stress.
- A company declining from 8.0x to 5.0x to 3.0x over six quarters is sending a consistent directional message even if the absolute level has not yet crossed into danger territory.
- The trend is the warning.
- Coverage can function as an early warning indicator — a company does not need to reach a distressed absolute level for the trend to be actionable.


















**Appendix A**
### Academic and institutional validation

- Moody's explicitly states that interest coverage — measured as EBIT/Interest Expense — is one of its primary financial metrics across virtually all non-financial corporate rating methodologies, alongside leverage.
- S&P's Corporate Methodology treats fixed charge coverage as a direct input into the financial risk profile assessment, alongside Debt/EBITDA, and assigns lower financial risk profile scores as coverage declines toward and below 2.0x.
- Altman's original Z-score model (1968) included EBIT/Total Assets as one of its five discriminating variables, capturing the same earnings-relative-to-obligations logic that coverage embeds.

### The Rite Aid confirmation

- As validated in the Leverage section, Rite Aid's EBITDA fell from $505.9M in fiscal 2022 to $429.2M in fiscal 2023 — a $76.7M decline.
- Over the same period, its interest expense remained elevated because its debt load had not meaningfully decreased.
- The result was a coverage ratio compressing toward and eventually below 1.0x in the quarters leading up to its October 2023 bankruptcy.
- The coverage signal was visible in the same quarterly filings that showed leverage deteriorating — confirming that the two metrics move together in distress but that coverage often deteriorates faster because interest expense is sticky while EBITDA can fall quickly.
