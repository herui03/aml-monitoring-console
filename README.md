# AML Transaction Monitoring Dashboard

An interactive, browser-based **transaction monitoring (TM) console** for anti-money-laundering surveillance. It screens a synthetic payment ledger against three AML typologies, generates explainable alerts, and drafts Suspicious Transaction Report (STR) narratives for MLRO review.

**[▶ Open the live dashboard](https://herui03.github.io/aml-transaction-monitoring/)**

![Transaction Monitoring Console](docs/screenshot.png)

## Why I built this

During my internships at Bank of China Singapore (KYC/CDD, counterparty risk) and Tencent (treasury reconciliation) I saw how much of financial-crime work is about *justifying* a decision, not just producing a flag. This project is a small, end-to-end version of that workflow: data → rules → alert → analyst rationale → filing draft, with every step visible.

## What it does

| Layer | Responsibility |
|---|---|
| **Data layer** | Generates ~685 synthetic transactions across 70 accounts over a 60-day window from a *seeded* RNG, so results are fully reproducible |
| **Detection engine** | Runs three independent, threshold-based typology rules over the ledger |
| **Triage layer** | Assigns severity (High / Medium / Low), status (Open / Under Review / Escalated) and a plain-English rationale to every alert |
| **Reporting layer** | Produces draft STR narratives with a recommended next action |

The synthetic baseline models realistic benign activity — payroll, retail spend, B2B invoice settlement and P2P transfers — with a small number of accounts deliberately seeded to exhibit suspicious behaviour.

## Detection rules

| Rule | Typology | Trigger logic |
|---|---|---|
| **R-01** | Structuring / smurfing | 5+ SGD transactions valued 8,000–9,999 from a single sender within a rolling 7-day window (just below the SGD 10,000 CTR threshold) |
| **R-02** | Rapid pass-through (layering) | Account receives funds and remits ≥90% of that specific inflow cross-border within 24–48h, across 3+ repeated cycles, where pass-through is the account's dominant behaviour |
| **R-03** | Round-number wires | 3+ wire transfers in exact multiples of 10,000 (≥ SGD 30,000 equivalent) to overseas beneficiaries |

Each rule surfaces its own threshold and rationale in the UI, so a reviewer can audit *why* an alert fired rather than trusting a black box.

## Design notes

**Explainability over automation.** Every alert exposes its trigger logic, a severity rationale and the underlying transactions. In a real compliance function an analyst must justify a filing decision, so the tool is built to make that justification visible.

**False-positive tuning.** Rule R-02 initially flagged a legitimate high-volume business whose outflows incidentally exceeded 90% of an inflow. It was tightened to require the outflow to match the *specific* inflow amount (90–110%) and include a cross-border leg, and to require conduit behaviour to be the account's dominant pattern rather than an incidental one. Rules produce investigative leads, not verdicts — managing the false-positive rate is part of the work.

**Analyst judgment.** Each alert carries an inline note identifying where a benign explanation is plausible (e.g. cash-intensive businesses legitimately transacting near the threshold, or intercompany arrangements explaining rapid onward movement) and what documentation would resolve it.

## Related work

This console is the rule-based, front-end companion to my larger AML project on the SAML-D dataset (9.5M transactions), where I compare rule-based screening against a gradient-boosting model with SHAP explanations — see [my profile](https://github.com/herui03) for that repository.

## Terminology

- **CTR** — Cash Transaction Report (mandatory reporting threshold)
- **STR** — Suspicious Transaction Report
- **MLRO** — Money Laundering Reporting Officer
- **EDD** — Enhanced Due Diligence

## Running it

`index.html` is fully self-contained — no build step, no dependencies, no network calls. Open it in any browser, or use the GitHub Pages link above.

## Disclaimer

All accounts, names and transactions are randomly generated synthetic data created for demonstration purposes. No real customer information is represented, and the thresholds used are illustrative rather than institution-specific.

---

**Herui Dou** · MSc Business Analytics, NTU · [LinkedIn](https://www.linkedin.com/in/heruidou)
