# Liquidity 

## What it is

### Core Definition

Liquidity measures whether a company has enough cash and near-cash assets to meet its obligations that are due within the next 12 months. It answers: *"if the company had to pay everything it owes in the next year using only what it already has on hand or can quickly convert to cash, could it do it?"*

---

### Liquidity vs Solvency

The distinction between liquidity and solvency is the most important concept to understand before defining this metric.

| Concept | Definition | Time Horizon |
|---------|------------|--------------|
| **Solvency** | Does the company's asset base exceed its total liabilities? | Long-term |
| **Liquidity** | Does the company have enough cash and near-cash assets to meet near-term obligations? | Short-term |

**Key insights:**
- A company can be solvent on paper (assets exceed liabilities) but illiquid in practice (cash and near-cash insufficient to meet near-term obligations)
- A company can be technically insolvent (liabilities exceed assets) but remain liquid for an extended period if it continues generating operating cash flow
- Most corporate defaults are triggered by **illiquidity, not insolvency** — the company runs out of cash before it runs out of assets
- This is why liquidity is tracked as a standalone metric rather than subsumed into leverage or coverage

---

### The Mechanics of Liquidity Stress

A company under stress typically experiences a simultaneous tightening from **three directions**:

| Direction | Description |
|-----------|-------------|
| **1. Operating cash generation declines** | FCF compresses, reducing the internal source of cash |
| **2. Near-term debt obligations increase** | Maturities arrive; revolving credit facilities come up for renewal; covenant breaches accelerate debt that was not expected to be due |
| **3. Access to external liquidity contracts** | Lenders become cautious; credit lines are pulled or not renewed; capital markets become less accessible precisely when the company needs them most |

> The combination of reduced inflows, increased outflows, and restricted external access defines a liquidity crisis. None of the three alone is necessarily fatal. **All three together, arriving simultaneously, is how companies default.**

---

### The Two Complementary Lenses

Liquidity is measured through two lenses that your system tracks simultaneously.

#### Lens 1 — Stock of Liquidity (what the company currently holds)

| Component | Description |
|-----------|-------------|
| Unrestricted cash and cash equivalents | Balance sheet current assets |
| Short-term investments | Can be liquidated quickly |
| **Undrawn portion of committed revolving credit facilities** | Contractually committed source of cash that the company can draw on at short notice — not a balance sheet asset but functions as one for liquidity purposes |

> A company with $200M of cash and a $500M undrawn revolver has **$700M of immediately available liquidity** even though the balance sheet shows only $200M.

#### Lens 2 — Flow of Obligations (what the company owes in the near term)

| Component | Description |
|-----------|-------------|
| Current liabilities (balance sheet) | Accounts payable, accrued liabilities, short-term debt, current portion of long-term debt |
| Other contractual obligations due within 12 months | Any other obligations with contractual due dates |

> **Core liquidity metric:** Available liquidity (stock) divided by near-term obligations (flow)

---

### The Time Dimension

Balance sheet ratios alone cannot capture intra-year timing risk.

- A company might have adequate current assets to cover current liabilities in aggregate
- But if obligations come due in the next **30 days** and assets will not be converted to cash for **90 days**, the company faces a short-term cash crunch
- Even though its 12-month liquidity looks acceptable

> This intra-year timing risk is what the **Debt Maturity Wall** metric captures in more detail. Liquidity and maturity wall are companion metrics that together give a complete picture of near-term cash adequacy.

---

### Role in Your Credit Warning System

Liquidity serves two distinct analytical functions:

| Condition | Function |
|-----------|----------|
| **Normal conditions** | Baseline adequacy check — confirming that the company has sufficient near-term resources to operate without relying on new financing |
| **Stressed conditions** | **Most urgent metric in the entire spec** — once liquidity falls to a level where the company cannot meet near-term obligations from available resources, the timeline to a potential credit event is measured in **months rather than years** |

> The system must escalate immediately when liquidity reaches stress levels.

**Note on relationship to Quick Ratio and Current Ratio:**
Those ratios are the most commonly used quantitative liquidity metrics,
computed directly from balance sheet line items.
This Liquidity metric incorporates the same current/quick ratio logic,
then extends it by adding undrawn committed facilities and
cross-referencing the Debt Maturity Wall for timing precision.
