#  Quick Ratio / Current Ratio

---

## What it is

The Quick Ratio and Current Ratio measure whether a company has enough short-term assets to cover its short-term obligations. They answer: *"can the company meet its obligations due within the next 12 months from assets it already holds?"*

These ratios are already computed as core outputs of the Liquidity metric — Outputs 1A (Current Ratio) and 1B (Quick Ratio) in Liquidity Formula 1. This standalone metric entry exists for two reasons: to document the ratios as independently tracked credit signals with their own alert history, and to capture the analytical value of tracking them separately from the broader liquidity framework in which they were first defined.

The distinction between the two:
- **Current Ratio** = Current Assets / Current Liabilities — broad; includes inventory and prepaid expenses
- **Quick Ratio** = (Current Assets − Inventory − Prepaid Expenses) / Current Liabilities — conservative; liquid assets only

The quick ratio is the more credit-analytically relevant of the two because it excludes assets that cannot be quickly converted to cash. For non-manufacturing, non-retail companies with minimal inventory, the two ratios are nearly identical. For retailers, manufacturers, and distributors the gap is meaningful and the quick ratio is the primary signal.

---

## Why it signals stress

Cross-reference: Liquidity metric — Why it signals stress, Mechanisms 1 and 4. The payment cliff and working capital trap mechanisms documented there apply directly here.

**Incremental signal relative to Liquidity metric:** The standalone value of tracking Quick and Current ratios as a separate metric from the broader liquidity assessment is the **quality deterioration signal** — when the current ratio is stable or improving while the quick ratio is declining, it reveals that the current asset mix is shifting toward less liquid assets (inventory building, receivables stretching) even though the headline liquidity figure looks fine. This divergence between the two ratios is an early warning of working capital deterioration that the combined liquidity metric may obscure.

```
Key divergence signal:
If Current Ratio stable AND Quick Ratio declining:
   Working capital quality deteriorating —
   liquid assets being replaced by inventory
   or receivables that are slower to convert
   Cross-reference FCF OCF/EBITDA conversion
   ratio — the same dynamic appears there
```

---

## Formula

**Extraction note:** All inputs for this metric are already extracted as part of the Liquidity metric. No additional XBRL queries are required. This section documents only what is incremental.

```
Current Ratio = Current Assets / Current Liabilities
Quick Ratio   = (Current Assets − Inventory
                − Prepaid Expenses)
                / Current Liabilities
```

All XBRL tags, fallback chains, and error handling: cross-reference Liquidity Formula 1 — identical in every respect.

**One incremental derived output:**

```
Current-Quick Gap = Current Ratio − Quick Ratio

This gap measures how much of the current ratio
is supported by illiquid assets.

Gap < 0.3x: current and quick ratios essentially
            equivalent; inventory/prepaid immaterial
Gap 0.3x – 0.8x: moderate inventory/prepaid weight
Gap > 0.8x: significant illiquid asset component;
            quick ratio materially more conservative;
            use quick ratio as primary signal
Gap > 1.5x: heavy inventory dependence;
            flag: "current ratio significantly
            overstates liquidity quality —
            quick ratio is the relevant measure
            for this issuer"
```

---

## Where it lives

Identical to Liquidity metric — Where it lives, Formula 1 table. Cross-reference that section entirely. No new locations.

---

## Structured or Unstructured

Identical to Liquidity metric — Structured or Unstructured, Formula 1 rows. All inputs fully structured XBRL. No LLM required.

---

## Extraction Fallback Logic

Cross-reference Liquidity metric Extraction Fallback Logic in full — identical fallback chains for Current Assets, Current Liabilities, Inventory, and Prepaid Expenses. No incremental fallback logic required for this metric.

---

## Stress Threshold

**Extraction note:** The absolute thresholds for Current Ratio and Quick Ratio are already defined in the Liquidity metric Stress Threshold — Dimensions 1 and 2. Cross-reference those tables for the full graduated bands (Watch through Critical) and sector adjustments. This section documents only what is incremental to the standalone tracking of these ratios.

**Incremental threshold — Current-Quick Gap:**

| Current-Quick Gap | Interpretation | Alert Level |
|---|---|---|
| < 0.3x | Ratios essentially equivalent; quality not a concern | No alert |
| 0.3x – 0.8x | Moderate illiquid asset weight; monitor | Watch if Quick Ratio also declining |
| 0.8x – 1.5x | Significant gap; quick ratio materially lower | Flag — use quick ratio as primary signal |
| > 1.5x | Heavy inventory dependence | Flag — current ratio misleading; quick ratio governs |
| Gap widening for 3 consecutive quarters | Quality deterioration trend | Watch — regardless of absolute gap size |

**Incremental threshold — Quick Ratio relative to Current Ratio trend:**

The most analytically valuable threshold for this metric is not an absolute level but a relative divergence rule:

```
Divergence alert:
Current Ratio change quarter-over-quarter: +X%
Quick Ratio change quarter-over-quarter: −Y%
If Current improving AND Quick deteriorating
for 2 consecutive quarters:
   Flag: "ratio quality divergence —
   current ratio improving while quick ratio
   declining; working capital mix deteriorating;
   cross-reference inventory and receivables
   trends in FCF metric"
```

**Sector adjustment for retail and manufacturing:** Cross-reference Liquidity metric Stress Threshold — Dimension 2 sector adjustment table. The 0.2x downward adjustment for retail and manufacturing quick ratio thresholds documented there applies identically here.

---

## Signal Timing

**Coincident to lagging — identical to Liquidity metric.**

Cross-reference: Liquidity metric — Signal Timing, Absorption Phase and Cliff Phase analysis. The timing characteristics documented there apply directly.

**Incremental signal timing observation:** The Current-Quick Gap divergence signal is slightly earlier than the absolute ratio threshold signals. A widening gap is visible 1–2 quarters before either ratio crosses an alert threshold because it detects the shift in working capital mix before the absolute liquidity level deteriorates. This makes the gap computation the most forward-looking output of this metric and the primary reason to track Quick and Current ratios as standalone signals rather than purely as components of the Liquidity framework.

**Filing lag:** Identical to all balance sheet metrics — 40 days after quarter-end for large accelerated filers. Both ratios are balance sheet instant measures; no intra-quarter updates available. Cross-reference Liquidity Frequency section for full update schedule.

---

## Frequency

Identical to Liquidity metric Frequency section in its entirety. Updates quarterly via 10-Q and annually via 10-K. 8-K monitoring applies as documented in the Liquidity Frequency section — particularly Item 2.04 (automatic Critical escalation) and going concern keyword detection.

Cross-reference: Liquidity Frequency section for full channel definitions, Phase 2 vs Phase 3 priority framework, and the Q4 dark window analysis. No incremental frequency considerations for this metric.

