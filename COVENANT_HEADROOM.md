# Covenant Headroom 

## What it is

### Core Definition

Covenant Headroom measures how close a company is to breaching the financial maintenance conditions embedded in its debt agreements. It answers: *"given the financial ratios the company has promised its lenders it will maintain, how much deterioration can the company absorb before it violates those promises?"*

### What Is a Financial Covenant?

When a company borrows money — whether through a bank loan, a revolving credit facility, or a term loan — lenders make a multi-year commitment based on their assessment of the company's financial health at the time of the loan.

**Lender's concern:** The company's financial condition may deteriorate between the loan date and maturity.

**Lender's solution:** A mechanism that gives them the right to act — demand repayment, restrict further borrowing, or renegotiate terms — if deterioration crosses an unacceptable threshold.

**That mechanism is the financial maintenance covenant.**

### Financial Maintenance Covenant Definition

A financial maintenance covenant is a contractual promise, written into the credit agreement, that the company will maintain certain financial ratios above or below specified thresholds throughout the life of the loan.

**Most common types:**

| Covenant Type | Example Threshold |
|---|---|
| Maximum leverage ratio | Net Debt / EBITDA ≤ 5.5x |
| Minimum interest coverage ratio | EBITDA / Interest Expense ≥ 2.0x |
| Minimum liquidity threshold | Maintain ≥ $X million of unrestricted cash or available revolver capacity at all times |

Less common: minimum net worth covenant, maximum capital expenditure covenant.

### Covenant Headroom Defined

Covenant headroom is the distance between where the company's ratio currently sits and where the covenant threshold is.

| Scenario | Current Leverage | Covenant Cap | Headroom | Implication |
|---|---|---|---|---|
| Company A | 4.2x | 5.5x | **1.3x** | EBITDA would need to fall ~24% or debt would need to increase materially |
| Company B | 5.1x | 5.5x | **0.4x** | Small buffer — a single bad quarter could eliminate it |

> The word "headroom" is important. It is **not the ratio itself** — it is the gap between the ratio and the line that cannot be crossed.

**Key insight:** Two companies can have identical leverage ratios but completely different covenant headroom if their respective covenants were set at different levels. A company with 4.0x leverage and a 4.5x covenant is more stressed from a covenant perspective than a company with 5.0x leverage and an 8.0x covenant, even though the first company's absolute leverage is lower.

### The Central Extraction Challenge

Covenant headroom is qualitatively different from every other metric in this spec in one important way:

> It is defined by a **private contractual agreement**, not by a public accounting standard.

| Challenge | Description |
|---|---|
| Threshold values | Negotiated between the company and its lenders — not set by GAAP |
| Standardisation | Not standardised across companies |
| Disclosure | Not always fully disclosed in public filings; may be described imprecisely, qualified by numerous exceptions, or not disclosed at all |

> This is why covenant headroom is one of the primary use cases for the LLM extraction layer — not because the computation is complex but because the inputs are locked inside unstructured legal prose.

### Maintenance vs Incurrence Covenants

An important distinction your system must understand:

| | Maintenance Covenants | Incurrence Covenants |
|---|---|---|
| **Testing frequency** | Tested continuously at every reporting period | Tested only when the company attempts a specific action (issuing more debt, paying a dividend, making an acquisition) |
| **Breach consequence** | Lenders have the right to demand immediate repayment regardless of when the debt actually matures | Do not give lenders the right to accelerate existing debt — simply prohibit the triggering action |
| **Where typically found** | Leveraged loans and high-yield bank facilities | Investment-grade bonds |

> Maintenance covenants are the stress signals. Incurrence covenants are constraints on future actions but are not themselves default triggers.

### Relationship with Other Metrics

Covenant headroom directly quantifies the legal boundaries that the other metrics measure from a financial analysis perspective.

| Covenant Type | What It Tells You | Related Metric |
|---|---|---|
| Leverage covenant headroom | How much EBITDA can fall before a breach | FCF, Coverage |
| Coverage covenant headroom | How much interest expense can rise before a breach | Debt Maturity Wall (interest rate reset analysis) |
| Liquidity covenant headroom | How much cash can be consumed before a breach | Liquidity (cash runway calculation) |

> Covenant headroom is the legal formalisation of the stress thresholds that the other metrics approach from the direction of financial analysis.
