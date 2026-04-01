# CVA Analysis — FX Forward & EUR EURIBOR 3M Receiver IRS

> **Academic Capstone Project** | MSc Quantitative Finance | Poznań University of Economics and Business | 2025–2026  
> **Author:** Gautam Prasad | [LinkedIn](https://linkedin.com/in/gautamprasadwork)

---

## What This Project Is About

This project models **Credit Valuation Adjustment (CVA)** — the market price of counterparty default risk — for two OTC derivative instruments:

1. **Buy EUR / Sell PLN FX Forward** — 3-year maturity, €1,000,000 notional, strike 4.4439
2. **Receiver EUR EURIBOR 3M IRS** — 3-year maturity, €10,000,000 notional, fixed rate 2.02% received annually

Calculation date: **31 December 2025**

CVA is what banks charge (or reserve) to account for the risk that a derivatives counterparty defaults before contract maturity. This project builds the full computational pipeline: from market data calibration and Monte Carlo simulation through to bilateral CVA/DVA calculation at both single-instrument and portfolio level.

---

## Why CVA Matters

Under **Basel III / FRTB** frameworks, banks must hold regulatory capital against CVA risk. Understanding how CVA is computed — and what drives it — is core knowledge for:
- Credit Risk Analysts
- Market Risk teams
- Model Validation units
- Derivatives structuring desks

---

## Project Structure

```
cva-analysis/
│
├── Stage_1/                    # Derivative mechanics & payoff profiles
│   ├── report_stage1.pdf       # FX Forward & IRS: mechanics, payoff formulas, valuation
│   └── slides_stage1.pptx
│
├── Stage_2/                    # CDS bootstrapping — implied default probabilities
│   ├── report_stage2.pdf       # CDS mechanics, premium leg, default leg, spread formula
│   ├── cds_bootstrapping.py    # Algorithm: extract survival probabilities from CDS spreads
│   └── slides_stage2.pptx
│
├── Stage_3/                    # Expected Exposure simulation
│   ├── report_stage3.pdf       # GBM calibration for FX; Vasicek/ABM for IR
│   ├── fx_forward_ee.py        # Monte Carlo EE simulation for FX Forward (10,000 paths)
│   ├── irs_ee.py               # Monte Carlo EE simulation for IRS (Cholesky joint simulation)
│   └── slides_stage3.pptx
│
├── Stage_4/                    # CVA Calculation (in progress)
│   └── ...
│
├── Stage_5/                    # Portfolio CVA + Variation Margin impact (in progress)
│   └── ...
│
└── README.md
```

---

## Methodology

### Stage I — Derivative Instruments

| Instrument | Model | Key Parameters |
|---|---|---|
| FX Forward (EUR/PLN) | GBM | Spot rate calibration, drift = ESTR − PLN risk-free rate |
| EUR IRS (Receiver) | Vasicek / ABM | Mean-reversion to EURIBOR 3M, σ calibrated to historical data |

**Payoff — FX Forward:**
```
Payoff(T) = N × (S(T) − K)   [from buyer's perspective]
```

**Payoff — Receiver IRS (per period):**
```
Payoff(t_i) = N × (R_fixed − L(t_{i-1}, t_i)) × Δt
```

### Stage II — CDS Bootstrapping

Implied survival probabilities extracted from CDS market quotes using the standard no-arbitrage condition:

```
Premium Leg NPV = Default Leg NPV
S_N = LGD × Σ DF(0,T_n) × [PS(T_{n-1}) − PS(T_n)]
      / Σ DF(0,T_n) × PS(T_n) × Δt_n
```

The bootstrapping algorithm iteratively solves for the piecewise-constant hazard rate λ that matches observed CDS spreads at each maturity.

### Stage III — Expected Exposure Simulation

- **Simulation horizon:** 3 years | **Time step:** 1 month | **Paths:** 10,000+
- **FX Forward EE:** GBM under risk-neutral measure calibrated to EUR/PLN historical data (source: Stooq)
- **IRS EE:** Interest rate simulation using Vasicek model calibrated to ESTR history (source: ECB Data Portal)
- **Joint simulation:** Cholesky decomposition applied to correlate FX and IR risk factors at portfolio level

```python
# Simplified EE profile formula
EE(t) = E[max(V(t), 0)]   # Expected Positive Exposure
ENE(t) = E[min(V(t), 0)]  # Expected Negative Exposure
```

### Stages IV–V — CVA Calculation (in progress)

```
CVA ≈ (1−R) × Σ EE(t_i) × DF(0,t_i) × [PS(t_{i-1}) − PS(t_i)]
```

Portfolio CVA incorporates:
- FX and IR correlation via Cholesky decomposition
- Variation Margin (VM) impact: `VM(T) = MtM_IRS(T−1) × FX(T−1) + MtM_FXFwd(T−1)`

---

## Key Concepts

| Term | Definition |
|---|---|
| CVA | Credit Valuation Adjustment — cost of counterparty default risk |
| DVA | Debit Valuation Adjustment — benefit from own default risk |
| EE | Expected Exposure — expected positive value of the derivative |
| ENE | Expected Negative Exposure |
| EPE | Expected Positive Exposure (time-averaged EE) |
| LGD | Loss Given Default = 1 − Recovery Rate |
| PS(t) | Survival probability to time t |
| CDS | Credit Default Swap — used to extract market-implied default probabilities |
| ISDA netting | Legal agreement allowing offsetting of exposures across trades |

---

## Tools & Libraries

| Tool | Purpose |
|---|---|
| Python (NumPy, Pandas, SciPy, Matplotlib) | Monte Carlo simulation, data processing, visualisation |
| Microsoft Excel | Market data calibration, CVA calculation grids |
| R | Statistical validation |
| LaTeX | Report typesetting |

---

## Data Sources

| Data | Source |
|---|---|
| EUR/PLN historical FX rates | [Stooq](https://stooq.com) |
| ESTR (EUR short-term rate) | [ECB Data Portal](https://data.ecb.europa.eu) |
| CDS market quotes | Course-provided market data (31.12.2025) |

---

## Academic Context

- **Course:** Credit Valuation Adjustment for Derivative Contracts
- **Supervisor / Lecturer:** Paweł Olsza, PUEB
- **Framework:** Consistent with Basel III counterparty credit risk capital requirements
- **References:** Hull (2022); Brigo & Mercurio (2006); Gregory — *The xVA Challenge* (2020)

---

## Status

| Stage | Topic | Status |
|---|---|---|
| I | Derivative mechanics & payoff profiles | ✅ Complete |
| II | CDS bootstrapping — implied default probabilities | ✅ Complete |
| III | Expected Exposure simulation (FX Forward + IRS) | ✅ Complete |
| IV | CVA calculation — single instrument | 🔄 In progress |
| V | Portfolio CVA + Variation Margin | 🔄 In progress |

---

## Contact

**Gautam Prasad**  
MSc Quantitative Finance — PUEB, Poznań, Poland  
[linkedin.com/in/gautamprasadwork](https://linkedin.com/in/gautamprasadwork)
